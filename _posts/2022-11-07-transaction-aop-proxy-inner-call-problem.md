---
layout: single
title: "스프링 트랙잭션 AOP 주의사항 - 프록시 내부 호출 문제"
categories: Kotlin
tag: [스프링, JPA, transactional, proxy, 코틀린]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 상황 설명
이동수단(자전거 등)을 대여하고 반납하는 공간이 있습니다.  

회원은 본인의 폰 gps를 기반으로 해당 공간과 같은 Geo-fence에 위치하면 해당 대여장소에서 이동수단을 대여할 수도 있고,  

아니면 지금 본인이 타고 있는 이동수단을 해당 반납장소에서 반납할 수 있습니다.  

이 대여 및 반납하는 공간은 각 요일마다 운영시간이 정해져 있고 주말이나 공휴일에도 각각 운영시간이 정해져있습니다.  

또한 이 공간은 자기 현재 위치뿐만 아니라 gps가 튀는 경우를 대비해 Geo-fence도 가지고 있습니다.  

이 공간을 등록하고, 수정하고, 삭제하고, 읽는, 즉, CRUD api를 구현하는데 여기서 스프링 트랜잭션 AOP 문제가 발생한 update의 경우를 공유합니다. 


## RentalPlace 엔티티
대여 및 반납 장소를 나타내는 RentalPlace의 코드는 아래와 같습니다.  

```kotlin
@Entity
@Table(name = "rental_place")
class RentalPlace(
    @Id
    @Column(name = "rental_place_id")
    val id: String = CommonUtils.uuid(),

    var name: String = "",
    var address: String = "",
    var lat: Double,
    var lng: Double,
) {
    fun changeRentalPlaceInfo(changeRentalPlace: RentalPlace) {
        this.name = changeRentalPlace.name
        this.address = changeRentalPlace.address
        this.lat = changeSmartHub.lat
        this.lng = changeRentalPlace.lng
    }
}
```

RentalPlace는 자신만의 id와 장소이름, 주소, 위도 및 경도를 필드멤버로 가집니다.  

나중에 dirty-checking할 때 사용할 정보 변경 메소드인 changeRentalPlaceInfo도 구현하였습니다.  


## PlaceOperationTime 엔티티
RentalPlace의 운영 요일과 운영 시간을 나타내는 PlaceOperationTime 클래스를 정의합니다.  

하나의 RentalPlace는 월화수목금토일과 공휴일을 포함하여 8개의 PlaceOperationTime을 가집니다.  

```kotlin
Entity
@Table(name = "place_operation_hour")
@IdClass(PlaceOperationTimeId::class)
class PlaceOperationTime(
        @Id
        @Column(name = "rental_place_id")
        var rentalPlaceId: String,

        @Id
        @Column(name = "day_type")
        var dayType: String,//월화수목금토일,공휴일

        @ApiModelProperty(value = "휴무 여부")
        @Column(nullable = false)
        var isClosed: Boolean = false,

        @ApiModelProperty(value = "운영 시작 시간")
        @Column
        var startTime: LocalTime,

        @ApiModelProperty(value = "운영 마감 시간")
        @Column
        var endTime: LocalTime,
) {

    fun isRentalPlaceClosed(): Boolean {
        var presentTime = LocalTime.now()

        if (isClosed) {
            return true
        }
        if (presentTime.isAfter(this.startTime) && presentTime.isBefore(this.endTime)) {
            return false
        }

        return true
    }

}
```
PlaceOperationTime은 rentalPlaceId와 dayType(월화수목금토일, 공휴일)을 복합키로 가집니다.  

```kotlin
class PlaceOperationHourId : Serializable {
    val placeId: String = ""
    val dayType: String = ""
}
```

즉, 각 rentalPlace마다 8개의 dayType이 있기 때문에 RentalPlace는 8개의 복합키와 함께 8개의 PlaceOperationTime과 연결되어 있습니다.  


## Geofence 엔티티
RentalPlace는 자기 자신의 위치뿐만 아니라 Geofence를 가지고 있습니다. 

Geofence는 하나의 점인데 이 점들이 모여서 5각형이 될 수도 있고 6각형이 될 수도 있으며 또는 4각형이 될 수도 있습니다.  

즉, Geofence는 몇 개로 한정되어 있기보다는 자유롭게 설정할 수 있습니다.  

그래서 아까 PlaceOperationTime은 각 RentalPlace마다 8개로 고정되어 있지만 Geofence의 개수는 고정되어 있지 않습니다.  

```kotlin
@Entity
@Table(name = "geofence")
@IdClass(GeofenceKey::class)
class Geofence(
    @Id
    @Column(name = "rental_place_id")
    var rentalPlaceId: String = "",

    @Id
    @Column(name = "geofence_order")
    var geofenceOrder: Int = 0,

    @Column(nullable = false)
    var lat: Double = 0.0,

    @Column(nullable = false)
    var lng: Double = 0.0
)
```

Geofence 역시 rentalPlaceId와 Geo-fence 각 점의 순서를 나타내는 geofenceOrder를 복합키로 가집니다.  

```kotlin
data class GeofenceKey : Serializable {
    var smartHubId: String = ""
    var geofenceOrder: Int = 0
}
```

또한 Geofence는 각 점의 위도와 경도를 필드멤버로 가지고 있습니다.  


## RentalPlace 정보 수정 로직 구현
RentalPlace의 수정 기능을 구현할 때 스프링 트랜잭션 문제가 일어 났기 때문에 수정에 대해서만 구현하고 나머지 기능들은 생략하도록 하겠습니다.  

여기서 중요한 점은 PlaceOperationTime이나 Geofence는 스스로 존재할 수 없고, RentalPlace와 항상 함께 움직인다는 점입니다.  

그래서 이러한 점에 착안하여 RentalPlace 등록 서비스 로직에서 RentalPlace를 등록할 때, PlaceOperationTime이나 Geofence도 같이 등록하였고,  

여기서는 딱히 문제가 없었으나 후에 수정할 때도 RentalPlace 변경 서비스 로직에서 RentalPlace를 변경할 때, PlaceOperationTime이나 Geofence도 같이 변경하였는데,  

여기서 바로 **스프링 트랙잭션 AOP의 프록시 내부 호출 문제**가 발생하여 RentalPlace의 변경된 내용이 db에 반영되지 않은 이슈가 발생합니다.  

문제의 코드는 다음과 같습니다.  

UpdateRentalPlaceReq는 dto이고, RentalPlace와 PlaceOperationTime, Geofence를 변경할 내용을 가지고 있습니다.  

```kotlin
@Service
class RentalPlaceService {

    @Autowired
    lateinit var rentalPlaceRepository: RentalPlaceRepository

    @Autowired
    lateinit var placeOperationTimeRepository: PlaceOperationTimeRepository

    @Autowired
    lateinit var geofenceRepository: GeofenceRepository


    fun updateRentalPlaceAndPlaceOperationTimeAndGeofence(
            updateRentalPlaceReq: UpdateRentalPlaceReq
    ): UpdateRentalPlaceRes {

        //1.
        updateRentalPlace(
                RentalPlace(
                        updateRentalPlaceReq.rentalPlaceInfo.id,
                        updateRentalPlaceReq.rentalPlaceInfo.name,
                        updateRentalPlaceReq.rentalPlaceInfo.address,
                        updateRentalPlaceReq.rentalPlaceInfo.lat,
                        updateRentalPlaceReq.rentalPlaceInfo.lng,
                )
        )

        //2.
        
        (
                //3.
                makeChangePlaceOperationTimes(
                        updateRentalPlaceReq
                )
        )

        //4. 
        deleteAllGeofence(updateRentalPlaceReq.rentalPlaceInfo.id)

        //5.
        updateAllGeofence(
                //6.
                changeGeofenceList(
                        updateRentalPlaceReq
                )
        )

        return UpdateRentalPlaceRes(code = OK)

    }

    //1. 
    @Transactional
    fun updateRentalPlace(
            changeRentalPlace: RentalPlace
    ) {
        var findRentalPlace = rentalPlaceRepository.findByIdOrNull(changeRentalPlace.id)
            ?: throw WebException(WebResponseCode.SMART_HUB_NOT_FOUND)

        //더티체킹
        findRentalPlace.changeSmartHubInfo(changeSmartHub)
    }

    //2. 
    @Transactional
    fun updatePlaceOperationTimes(
            changePlaceOperationTimes: List<PlaceOperationTime>
    ) {
        placeOperationTimeRepository.saveAll(changePlaceOperationTimes)
    }

    //3. 
    fun makeChangePlaceOperationHours(
            updateRentalPlaceReq: UpdateRentalPlaceReq
    ): ArrayList<PlaceOperationTime> {
        var changePlaceOperationTimes = ArrayList<PlaceOperationTime>()
        //8번(월화수목금토일, 공휴일 8번 루프)
        for (operationTime in updateRentalPlaceReq.rentalPlaceInfo.operationTimes) {
            changePlaceOperationTimes.add(
                    PlaceOperationTime(
                            updateRentalPlaceReq.rentalPlaceInfo.id,
                            operationTime.day,
                            operationTime.isClosed,
                            LocalTime.parse(
                                    operationTime.openTime,
                                    DateTimeFormatter.ofPattern("HH:mm")
                            ),
                            LocalTime.parse(
                                    operationTime.closeTime,
                                    DateTimeFormatter.ofPattern("HH:mm")
                            ).plusSeconds(59),
                    )
            )
        }
        return changePlaceOperationTimes
    }

    //4. 
    @Transactional
    fun deleteAllGeofence(
            rentalPlaceId: String
    ) {
        geofenceRepository.deleteAllByRentalPlaceId(rentalPlaceId)
    }

    //5.
    @Transactional
    fun updateAllGeofence(
            changeGeofenceList: List<Geofence>
    ) {
        geofenceRepository.saveAll(changeGeofenceList)
    }

    //6.
    fun changeGeofenceList(
            updateRentalPlaceReq: UpdateRentalPlaceReq
    ): ArrayList<Geofence> {
        var changeGeofenceList = ArrayList<Geofence>()
        var geofenceList = updateRentalPlaceReq.rentalPlaceInfo.geofenceList

        for ((index, geofencePos) in geofenceList.withIndex()) {
            changeGeofenceList.add(
                    Geofence(
                            updateRentalPlaceReq.rentalPlaceInfo.id,
                            index,
                            geofencePos.lat,
                            geofencePos.lng
                    )
            )
        }
        return changeGeofenceList
    }
}
```

updateAllGeofence를 하기 전에 deleteAllGeofence를 하는 이유는 geofence가 6개였다가 5개로 수정되는 경우가 있는데  

이 때, updateAllGeofence만 하면 5개의 geofence가 수정되고, 나머지 1개의 geofence는 예전 정보를 가지고 그대로 db에 저장되어 있기 때문입니다.  

우리가 원하는 것은 geofence가 6개였다가 5개로 수정되면 geofence의 5개의 정보가 수정되는 것은 기본이고,  

기존에 남아 있던 한 개 더 많았던 geofence의 정보는 삭제되길 바라기 때문에 먼저 deleteAllGeofence를 하고 그 다음 updateAllGeofence를 합니다.  

위 코드에서 보면 삭제와 변경이 필요한 메소드(delete와 update) 각각에 모두 Transactional 애너테이션을 붙였습니다.  


### TransactionRequiredException 발생
이제 이 코드를 빌드한 다음 수정 기능을 수행하면  

**javax.persistence.TransactionRequiredException: No EntityManager with actual transaction available for current thread - cannot reliably process 'remove' call**

에러가 발생합니다.  

일단 현재 로직에서 remove는 하나 밖에 없으니 **deleteAllGeofence**에서 에러가 난 것을 유츄해볼 수 있습니다.  

구글링을 해보니 보통 이 에러는 delete를 할 때, Transactional 애너테이션이 없을 경우 발생한다고 합니다.  

근데 분명 위의 코드를 보시면 **deleteAllGeofence** 위에 Transactional 애너테이션이 붙어 있음을 확인할 수 있습니다.  

그래서 도무지 왜 여기서 에러가 나는지 알 수가 없었습니다.  

디버깅을 통해 어디서 에러가 나는지 다시 한 번 확인해보니 예상했던대로  

**updateRentalPlaceAndPlaceOperationTimeAndGeofence** 메소드에서 **//4.deleteAllGeofence**에서 에러가 발생합니다.  


### 에러 발생 전 호출된 메소드의 db 반영 결과
일단 에러가 왜 났는지는 알 수가 없어서 수정 결과는 어떻게 됬는지 궁굼해서 확인했습니다.  

**//1.updateRentalPlace**은 transactional 애너테이션이 붙어있음에도 불구하고, 더티체킹이 안됩니다.  

즉, **rentalPlace**에서 변경할 내용은 변경이 안되고, db에는 원본 그대로 남아 있습니다.  

다음으로 **//2.updatePlaceOperationTimes**은 transactional 애너테이션이 붙어 있고, 내부에서 repository의 saveAll을 호출하는데,

**PlaceOperationTimes**의 경우 변경할 내용이 db에 반영이 되었습니다.  

즉, 에러는 **//4.deleteAllGeofence**에서 발생했고, 에러가 나기 전에 호출된 **updatePlaceOperationTimes**는 정상적으로 트랜잭션 작업이 마무리 된 것을 확인할 수 있었습니다.  


### TransactionRequiredException 해결
구글링을 하다 보니 repository의 delete에 직접 transactional 애너테이션을 붙이는 경우가 있길래 **deleteAllGeofence**내부에서 호출하는  

repository의 **deleteAllByRentalPlaceId**에 직접 transactional 애너테이션 붙여 봤습니다.  

그리고 다시 수정 기능을 실행을 시켰습니다.  

더이상 **TransactionRequiredException**이 발생하지 않습니다.  

**deleteAllGeofence**가 성공적으로 자신의 기능을 수행하여 Geofence를 모두 지우고, 

**//5.updateAllGeofence**에서 변경된 Geofence를 모두 새로 저장하는 것도 성공적으로 수행됩니다.  

에러가 발생되지 않으니 도중에 중단이 될 일도 없어서 수정 기능 수행 후 db 결과를 확인했습니다.  

예를 들어, 원래 Geofence가 5개 였고, 값도 55.xxx로 설정하였습니다.  

수정할 때 Geofence를 4개로 줄이고, 값을 77.xxx 로 변경하였습니다.  

수정 기능이 수행한 후 원하는대로 Geofence는 4개로 줄고, 값도 변경되었음을 확인할 수 있었습니다.  

물론 **PlaceOperationTimes**의 값도 당연히 변경되었습니다.  

### 더티체킹이 되지 않는 문제
문제는 바로 여전히 **rentalPlace**에서 변경할 내용은 변경이 안되고, db에는 원본 그대로 남아 있다는 것이었습니다.  

즉, 더티체킹은 여전히 안되고 있다는 것이었습니다.  

물론 더티체킹 대신에 save로 바꾸면 해결될 것이지만 뭔가 근본적으로 문제를 해결하고 싶었습니다.  

(**deleteAllGeofence**내부에서 호출하는 repository의 **deleteAllByRentalPlaceId**에 직접 transactional 애너테이션 붙이는 것도 뭔가 찝찝하고...)

분명히 각 메소드들은 모두 transactional 애너테이션이 붙어 있는데 왜 delete에서 에러가 발생핬었고, 왜 여전히 더티체킹은 안되는지 궁굼했습니다.  

그러던 와중에 예전에 인프런에서 **김영한님의 강의 - 스프링 DB 2편 - 데이터 접근 활용 기술**에서 봤던 내용이 생각났습니다.  

왠지 그 때 김영한님이 이 내용이 엄청 중요해서 면접에서도 많이 나오고, 실무에서도 이걸로 실수하는 바람에 시간을 많이 잡아먹는 경우가 있다고 반드시 알아둬야 한다고 말씀하셨던 것이 생각났습니다.  

바로 **트랜잭션 AOP 주의 사항 - 프록시 내부 호출** 문제였습니다.  

인강을 다시 보고, 강의자료를 다시 보니 역시 **프록시 내부 호출**문제였고, 어디서 문제가 발생했는지 바로 이해할 수 있었습니다.  


# 스프링 트랙잭션 AOP 주의사항 - 프록시 내부 호출 문제
## 스프링 트랙잭션 AOP 기본 원리
스프링에서는 클래스 상단에 Transactional 애너테이션을 붙이든 메소드에만 Transactional 애너테이션을 붙이든 위치에 상관없이 Transactional 애너테이션이 있다면 무조건 해당 클래스의 프록시를 생성하여 스프링 빈에 등록합니다.  

그리고 개발자가 해당 클래스를 호출하면 실제로는 **1.프록시가 먼저 호출**이 되고, 그 다음 **2.프록시에서 트랜잭션을 시작**하고, **3.타켓(진짜 우리가 원하는 해당클래스)의 메소드를 호출하여 비지니스 로직을 수행**한 다음에 메소드가 종료되면 **4.트랜잭션을 종료**하는 과정이 실행됩니다.  

## 클래스 상단에 transactional 애너테이션이 있는 경우
일반적으로 클래스에 transactional 애너테이션을 붙일 경우 모든 메소드에 트랜잭션 기능이 추가됩니다.(**readOnly = true**를 하지 않는 이상)  

즉, 해당 클래스의 모든 메소드는 호출되기 전에 트랜잭션이 시작되고, 해당 메소드 종료 후에는 트랜잭션 종료 기능이 수행됩니다.  

이렇게 되면 create, update, delete의 경우 당연히 트랜잭션 기능이 있어야 하기 때문에 메소드마다 따로 transactional 애너테이션을 안붙여도 되는 이점이 있습니다.  

그래서 만약 이 서비스가 create, update, delete 기능만 수행한다면 클래스 상단에 transactional 애너테이션을 붙이면 됩니다.  

그러나 만약에 해당 서비스가 read의 기능도 수행한다면 그 때는 비효율이 발생합니다.  

즉, read의 경우는 db데이터 변경이 없이 db데이터를 읽어오는 작업이기 때문에 트랜잭션 작업이 필요없음에도 불구하고, 클래스 상단에 transactional이 있는 경우

메소드 호출 전후로 트랜잭션 작업이 실행됩니다.  

이는 매우 비효율적이기 때문에 그래서 보통은 클래스 상단에 transactional이 있는 경우는 read를 하는 모든 메소드 상단에 각각 **transactional(readOnly = true)**를 하거나  

아니면 애초에 클래스 상단에 transactional을 두지 않고, 트랜잭션 작업이 필요한 메소드의 경우 그 상단에 transactional 애너테이션을 두는 경우입니다.  


## 메소드 위에 transactional 애너테이션이 있는 경우
위에서도 언급했듯이 메소드에 transactional 애너테이션이 있어도 해당 클래스의 프록시는 생성됩니다.  

다만 해당 클래스에서 transactional 애너테이션이 붙어 있지 않은 메소드를 호출할 때는 트랜잭션 작업을 수행하지 않고,  

해당 클래스에서 transactional 애너테이션이 붙어 있는 메소드를 호출할 때만 트랜잭션 작업을 수행합니다.  

그리고 바로 여기서 **프록시 내부 호출 문제**가 발생합니다.  


## 해당 클래스에서 transactional 애너테이션이 없는 메소드 내부에서 같은 클래스의 transactional 애너테이션이 있는 메소드를 호출하는 경우
transactional 애너테이션이 없는 메소드는 아까 말했듯이 트랜잭션 작업을 수행하지 않고, 바로 **타켓(진짜 우리가 원하는 해당클래스)의 메소드를 호출하여 비지니스 로직을 수행**합니다.  

즉, 바로 타켓(진짜 우리가 원하는 해당클래스)의 메소드 내(비지니스 로직)에서 transactional 애너테이션이 있는 메소드를 호출해봤자  

이 메소드는 **프록시의 메소드가 호출되는 것(프록시에서 메소드가 호출되야 트랜잭션 작업을 시작하고, 비지니스 로직을 수행한 다음 트랜잭션을 종료하는데)이 아니라**  

타겟의 메소드가 그대로 호출되기 때문에 트랜잭션 작업을 수행하지 않고 바로 비지니스 로직만 수행할 뿐입니다.  

예를 들어 설명하겠습니다.  

클래스 A가 있습니다. 그리고 A에는 A-a라는 transactionl이 없는 메소드가 있고, A-b라는 transactionl이 있는 메소드가 있고,  

A-a라는 메소드는 내부에서 비지니스 로직 중에 A-b라는 메소드를 호출하도록 설계되어 있습니다.  

빌드시 클래스 A 내부에 A-b라는 transactionl이 있는 메소드가 있기 때문에  

AProxy라는 프록시 클래스가 생성되고, AProxy라는 프록시 클래스는 AProxy-a와 AProxy-b라는 메소드를 가집니다.  

이제 특정 기능을 수행하기 위해 A클래스의 A-a라는 메소드를 실행합니다.(A-a 메소드 내부에서 호출되는 A-b에서는 트랜잭션 작업이 수행되길 바라면서)  

우리가 보기엔 A클래스의 A-a처럼 보이지만 실제로는 스프링 트랜잭션 AOP에 의해 AProxy에서 AProxy-a가 실행됩니다.  

근데 A-a라는 transactionl이 없는 메소드이기 때문에  

AProxy-a는 트랜잭션 작업을 시작하지 않고 바로 Target(A)의 A-a 메소드를 호출합니다.  

이제 A라는 클래스의 A-a 메소드 내부에 들어왔습니다.  

여기서 A-b라는 비지니스 로직이 바로 수행됩니다.  

왜냐하면 여기는 A라는 클래스의 내부이기 때문입니다.  

그리고 A-a 메소드는 종료됩니다.  

그리고 다시 AProxy-a로 돌아와서 트랜잭션 작업을 시작하지 않았기 때문에 따로 종료하지 않고,  

바로 AProxy-a 메소드가 종료됩니다.  

AProxy-b라는 메소드가 호출되어야만 우리가 원하는 트랜잭션 작업을 시작하고,  

Target(A)의 A-b 메소드의 비지니스 로직이 수행되고,  

트랜잭셕 작업이 종료됩니다.  

하지만 AProxy-a에서는 우리가 원하는대로 AProxy-b를 호출하지 않고,  

Target(A)의 A-b 메소드의 비지니스 로직이 수행되기 때문에  

트랜잭션 작업도 수행되지 않습니다.  


## 스프링 트랙잭션 AOP 주의사항 - 프록시 내부 호출 문제 해결 - 1
해당 문제를 해결하기 위한 방법은 많습니다.  

저는 여기서 2가지 방법을 제시하려고 합니다.  

먼저 가장 간단한 방법은 **transactional 애너테이션이 없는 메소드 내부에서 같은 클래스의 transactional 애너테이션이 있는 메소드를 호출하는 경우**를 막는 것입니다.  

그러기 위해서 **transactional 애너테이션이 없는 메소드**에 transactional을 추가하고,  

**내부에서 호출되는 메소드에서 transactional 애너테이션을 제거**하는 방법입니다.(당연히 상위 클래스에서 transactional 애너테이션이 있기 때문에 여기에는 없어도 됩니다.)  

이걸 위의 비지니스로직에 적용하면 **//1.updateRentalPlace, //2.updatePlaceOperationTimes, //4.deleteAllGeofence, //5.updateAllGeofence**에서 transactional 애너테이션을 모두 제거하고,  

가장 상위의 메소드인 **updateRentalPlaceAndPlaceOperationTimeAndGeofence**에 transactional 애너테이션을 적용합니다.  

그럼 이제 위의 deleteAll애서 발생했던 **TransactionRequiredException** 에러도 해결되고,  

**//1.updateRentalPlace**에서 더티체킹한 내용이 db에 반영이 안되는 문제도 해결이 됩니다.(db에 변경된 내용이 저장됩니다.)  

즉, 가장 상위 메소드에 transactional 애너테이션을 추가함으로써 **updateRentalPlaceAndPlaceOperationTimeAndGeofence**의 실제 로직이 시작하기 전에 트랜잭션이 시작되고,  

**updateRentalPlaceAndPlaceOperationTimeAndGeofence**의 실제 비지니스 로직이 수행되고,  

**updateRentalPlaceAndPlaceOperationTimeAndGeofence** 메소드가 종료되면 트랜잭션 종료작업이 수행됨으로써  

성공적으로 db에 변경된 내용이 저장됩니다.  


## 스프링 트랙잭션 AOP 주의사항 - 프록시 내부 호출 문제 해결 - 2
이럴 경우 단점으로는 **updateRentalPlaceAndPlaceOperationTimeAndGeofence**에서 하나만 에러가 발생해도 모든 내용이 다 rollback이 되어 db에 반영되지 않는다는 점입니다.  

아까 위의 코드에서는 transaction 애너테이션이 가장 상위 메소드에 적용되어 있지 않았기 때문에 **TransactionRequiredException**에러가 발생해도 부분적으로는 변경 내용이 db에 저장되었습니다.  

즉, 지금 현재 상황에서는 모두 다 rollback되는 것이 낫지만 도메인이나 상황에 따라서는 에러가 발생해도 부분적으로는 db에 반영되는 것이 나은 상황일 수도 있습니다.  

이렇게 에러가 발생하더라도 모두 다 rollback하지 않고 부분적으로는 저장하기 위해서는 분리가 필요합니다.  

그러기 위해서는 우선 가장 상위의 메소드인 **updateRentalPlaceAndPlaceOperationTimeAndGeofence**에서 다시 transactional 애너테이션을 없앱니다.  

그리고 이제  **//1.updateRentalPlace, //2.updatePlaceOperationTimes, //4.deleteAllGeofence, //5.updateAllGeofence**처럼 트랜잭셕 작업이 수행되야 하는 메소드들을 호출하는 주체를 분리합니다.  

즉, **//1.updateRentalPlace**를 호출하는 별도의 rentalPlaceService 클래스를 생성하고, 거기서 **//1.updateRentalPlace**를 정의하고 transactional 애너테이션을 붙여줍니다.  

**//2.updatePlaceOperationTimes**를 호춣하는 별도의 PlaceOperationTimeService 클래스를 생성하고, 거기서 **//2.updatePlaceOperationTimes**를 정의하고 transactional 애너테이션을 붙여줍니다.  

**//4.deleteAllGeofence, //5.updateAllGeofence**를 호출하는 별도의 GeofenceService 클래스를 생성하고, 거기서 **//4.deleteAllGeofence, //5.updateAllGeofence**를 정의하고 transactional 애너테이션을 붙여줍니다.  

이렇게 클래스를 별도로 나누고 책임을 분리합니다.  

그런 다음 가장 상위 클래스(transactional 애너테이션이 없고, 그러므로 당연히 스프링 트랜잭션 AOP도 적용이 안되는)에서 rentalPlaceService, PlaceOperationTimeService, GeofenceService 클래스의 객체를 멤버로 가지고,  

필요할 때 마다 rentalPlaceService, PlaceOperationTimeService, GeofenceService의 메소드를 호출해주면  

rentalPlaceService, PlaceOperationTimeService, GeofenceService 모두 프록시가 적용된(스프링 트랙잭션을 수행하는) 메소드를 호출할 수 있습니다.  

그래서 3개의 클래스 중에서 하나가 에러가 발생하더라도 예를 들어, 실행 순서가 1.rentalPlaceService, 2.PlaceOperationTimeService, 3.GeofenceService인 경우  

3.GeofenceService에서 에러가 발생하면  1.rentalPlaceService, 2.PlaceOperationTimeService는 성공적으로 db에 반영이 됩니다.  

물론  1.rentalPlaceService에서 에러가 발생하면(따로 try catch로 에러를 잡지 않는 이상) 당연히 뒤의  2.PlaceOperationTimeService, 3.GeofenceService도 db에 저장되지 않습니다.(실행이 안되니까)  

아무튼 이렇게 최상위 메소드에 transactional 애너테이션을 붙여주는 방법과 최상위 메소드에서 호출되는 내부 메소드들을 호출하는 주체를 모두 클래스로 분리시키는 방법 두가지를 소개하였습니다.  

(사실 거대 클래스를 운용하기보다 항상 클래스를 이렇게 잘게 잘라서 **책임과 역할을 명확히 분리**하면 사실 이런 문제는 발생하지 않습니다.  

다만 실무에서 가끔 레거시나 기존 코드 컨벤션에 따라서 코드를 작성하다보면 이러한 문제를 만날 수 있기 때문에 알아두면 좋을 거라고 생각합니다.)


### 여담
이번 에러를 통해서 repository의 save는 이를 호출하는 서비스 메소드에서 transactional 애너테이션이 없어도  

또는 서비스 메소드가 트랜잭션 작업을 수행하는 프록시 메소드가 아니더라도 항상 트랜잭션 작업을 수행하는 것을 알 수 있습니다.  

즉, 항상 db에 저장이 됩니다.  

또한 deleteAllBy또는 deleteBy의 경우는 서비스 메소드에서 트랜잭션 작업이 수행되지 않는다면  

**TransactionRequiredException**에러가 발생함을 알 수 있습니다.  

그리고 deleteAllBy또는 deleteBy의 경우 repository에서 transactional 애너테이션을 붙이면  

이 문제가 해결됩니다.(이 방법보다는 아까 위에서 언급한 2가지 방식이 더 좋습니다.)  

아무튼 deleteAllBy또는 deleteBy의 경우 repository에서 transactional 애너테이션을 붙이면  

왜 트랜잭션 작업이 수행되는지는 위에서 언급한 원리로 쉽게 이해할 수 있습니다.  

비록 현재 repository의 deleteAllBy를 호출하는 서비스의 메소드는 트랜잭션 작업을 수행하지 않는다고 해도  

repository는 서비스와는 별도의 다른 클래스이기 때문에  

서비스 메소드에서 repository의 deleteAllBy를 호출하면  

실제로는 repository의 프록시가 호출되고,  

트랜잭션 작업을 시작하고,  

실제로 Target(repository)을 호출하여 비지니스 로직(deleteAllBy)를 수행한 뒤에  

트랜잭션 작업이 종료되고,  

다시 repository의 deleteAllBy를 호출하는 서비스 메소드로 돌아옵니다.  
