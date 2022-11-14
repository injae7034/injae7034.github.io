---
layout: single
title: "redis repository에서 다형성 적용하여 리팩토링하기"
categories: Kotlin
tag: [다형성, redis, 스프링, JPA, spring data redis, 상속, abstract, 추상 팩토리 메소드 패턴]
toc: true
toc_label: "목차"
toc_sticky: true
---

# redis에서 상속 개념 적용
처음 redis를 사용해보면서 redis에 데이터를 저장하는 방법이 두가지 있다는 것을 알게 되었습니다.  

첫 번째는 redisTemplete을 이용하는 방법이고, 두 번째는 redis repository를 이용하는 방법입니다.  

redis repository가 jpa repository와 사용 방법도 비슷하여서 **redis repository**를 사용하였습니다.  

그렇게 redis repository를 사용하여 data를 저장하다 보니 거의 jpa와 흡사하여 궁굼증이 생기기 시작했습니다.  

**'상속개념을 적용하여 부모repository에 자식entity를 저장하면 알아서 분리해서 자식 repository에도 저장해주는 것인가?'**  

결론적으로 말씀드리면 네, **부분적으로 저장(save)에서는 가능**합니다.  

그러나 **삭제(deleteby or deleteAllBy)또는 찾기(findBy or findAllBy, findAll)의 경우 동작하지 않습니다.**  


## 상속 개념을 적용하기 전에 redis 클래스들
처음에는 당연히 redis 자료구조는 key와 value로 이뤄져있고, 이건 jpa와 별도이기 때문에 당연히 안될거라고 생각하고,  

처음에는 적용해볼 생각을 안하고, 그냥 코드를 짰습니다.  


### redis entity 클래스
우선 redis repository에 저장할 data들을 클래스로 정의하였습니다.  

```kotlin
@RedisHash("bicycle")
+class RedisBicycle(
+    @Id
+    val id: String,
+    var lat: Double? = null,
+    var lng: Double? = null,
+)
```
```kotlin
@RedisHash("kickBoard")
+class RedisKickBoard(
+    @Id
+    val id: String,
+    var lat: Double? = null,
+    var lng: Double? = null,
+)
```
```kotlin
@RedisHash("motorcycle")
+class RedisMotorcycle(
+    @Id
+    val id: String,
+    var lat: Double? = null,
+    var lng: Double? = null,
+)
```
클래스명과 redisHash만 다르지 필드멤버는 모두 같습니다.  

벌써부터 상속을 이용하고 싶은 생각이 강하게 드네요.  

RedisHash는 redis repository를 이용할 때 반드시 적어줘야 합니다.  

**RedisHash의 명칭과 필드멤버의 Id 애너테이션이 붙은 id를 통해서 redis의 key가 생성**되고,  

**객체(RedisBicycle, RedisKickBoard, RedisMotorcycle의 인스턴스)가 value로 저장**됩니다.  

### redis Repository
다음으로는 위의 redis entity들을 저장해 줄 repository들을 정의합니다.  

```kotlin
interface BicycleRedisRepository : CrudRepository<RedisBicycle, String> {
}
```
```kotlin
interface KickboardRedisRepository : CrudRepository<RedisKickboard, String> {
}
```
```kotlin
nterface MotorcycleRedisRepository : CrudRepository<RedisMotorcycle, String> {
}
```
CrudRepository를 상속받아서 자신이 저장할 클래스명을 적어주면 됩니다.  

이런 면에서 볼 때 Spring Data JPA와 거의 흡사합니다.  

(Spring Data JPA repository에서 미리 정의해둔 메소드들도 모두 이용할 수 있습니다.  

예를 들어, save, saveAll, deleteAllById, findAll 등...)

### redis publish data 클래스
```kotlin
class RedisMobilityPublishData(
    val id: String,
    val mobilityType: String,
    var lat: Double? = null,
    var lng: Double? = null
)
```
publish용 data 클래스이며 mobilityType을 통해 지금 publish되는 data가 kickBoard, bicycle, motorcycle 인지 구분할 수 있습니다.  


# 비지니스 로직 구현
이제 redis를 활용하여 비지니스로직을 구현하려고 합니다.  

현재 비지니스 목표는 관리자 페이지에서 대여 중 또는 대여 준비 중인 kickBoard, bicycle, motorcycle의 위치를 실시간으로 반영하여 보여주는 것입니다.

실시간으로 변하는 kickBoard, bicycle, motorcycle의 위치 정보를 매개변수로 받아서 이를 db에 반영하고, 수정된 정보를 redis publish를 통해 구독하는 채널들에게 출력해주는 비지니스 로직을 구현하려고 합니다.  

이를 위해 여기서는 update와 read의 기능만을 구현하도록 하겠습니다.  

또한 db에 저장되는 엔티티 클래스들을 정의하도록 하겠습니다.  

## 상속 개념을 적용한 엔티티 클래스
가장 먼저 부모클래스인 Moblility 개념부터 정의합니다.  

```kotlin
@Entity
@Table(name = "mobility")
@Inheritance(strategy = InheritanceType.JOINED)
@DiscriminatorColumn(name = "mobility_type")
open class Mobility(
    @Id
    open var id: String = "",

    @ApiModelProperty(value = "실시간 위도")
    @Column
    open var lat: Double? = null,

    @ApiModelProperty(value = "실시간 경도")
    @Column
    open var lng: Double? = null
) {
    open fun getMobilityType(): String {
        return "moblility"
    }

    open fun changeMobilityPos(lat: Double, lng: Double) {
        this.lat = lat,
        this.lng = lng
    }
}
```

다음으로 자식들인 bicycle, kickBoard, motorcycle을 정의합니다.  

```kotlin
@Entity
@Table(name = "bicycle")
@DiscriminatorValue(value = "bicycle")
class Bicycle(
        id: String = "",
        lat: Double? = null,
        lng: Double? = null
): Mobility(
        id = id,
        lat = lat,
        lng = lng
) {
    override fun getMobilityType(): String {
        return "bicycle"
    }
}
```
```kotlin
@Entity
@Table(name = "kickBoard")
@DiscriminatorValue(value = "kickBoard")
class KickBoard(
        id: String = "",
        lat: Double? = null,
        lng: Double? = null
): Mobility(
        id = id,
        lat = lat,
        lng = lng
) {
    override fun getMobilityType(): String {
        return "kickBoard"
    }
}
```
```kotlin
@Entity
@Table(name = "motorcycle")
@DiscriminatorValue(value = "motorcycle")
class Motorcycle(
        id: String = "",
        lat: Double? = null,
        lng: Double? = null
): Mobility(
        id = id,
        lat = lat,
        lng = lng
) {
    override fun getMobilityType(): String {
        return "motorcycle"
    }
}
```
위도와 경도를 바꿀 때는 부모의 changeMobilityPos 메서드를 그대로 가져다 쓰도록 하고,  

getMobilityType은 부모의 메서드를 오버라이드하여 각자 자기 자신에 맞는 mobilityType을 출력하도록 정의하였습니다.  

## 엔티티들을 저장할 repository
```kotlin
interface MobilityRepository: CrudRepository<Mobility, String> {
    fun findAllByIdIn(ids: List<String>): List<Mobility>
}
```
```kotlin
interface BicycleRepository: CrudRepository<Bicycle, String> {
}
```
```kotlin
interface KickBoardRepository: CrudRepository<KickBoard, String> {
}
```
```kotlin
interface MotorcycleRepository: CrudRepository<Motorcycle, String> {
}
```

## MoblilityRedisService 클래스
```kotlin
@Service
class MoblilityRedisService {
     @Autowired
    lateinit var bicycleRedisRepository: BicycleRedisRepository

    @Autowired
    lateinit var kickboardRedisRepository: KickboardRedisRepository

    @Autowired
    lateinit var motorcycleRedisRepository: MotorcycleRedisRepository

    @Autowired
    lateinit var redisTemplate: RedisTemplate<String, String>
}
```
우선 MoblilityRedisService는 멤버로 RedisKickBoard, RedisBicycle, RedisMotorcycle의 객체들을 저장할 각각의 repository들을 멤버로 가집니다.  

그리고 topic을 subscribe하는 채널에 publish 기능을 수행하기 위해 redisTemplate도 멤버로 가집니다.  

다음으로 service의 updateMobilities 구현할 차례입니다.  


## updateMobilities 메소드의 파라미터
updateMobilities 메소드의 파라미터로 UpdatelMobilitiyDto를 정의합니다.  

```kotlin
 class UpdateMobilitiyDto(
        var mobilityInfoList: List<MobilityInfo>
    ) {
        class MobilityInfo(
        val id: String,
        val lat: Double? = null,
        val lng: Double? = null
    )
    }
```

UpdatelMobilitiyDto는 변경이 된 이동수단(moblility)들의 정보(id와 위도 및 경도)를 리스트로 가지고 있습니다.  

이 리스트를 활용하여 db에도 변경된 데이터를 저장하고, redis repository에도 변경사항을 저장한 다음 이를 구독자에게 publish하는 과정까지 구현합니다.  


## updateMobilities 메소드
```kotlin
@Transactional
fun updateMobilities(dto: UpdateMobilitiyDto): Response {
    var mobilityIds = dto.mobilityInfoList.map { it.id }

    //해당 id의 모든 mobility를 찾는다. 반환형은 부모인 Mobility의 리스트
    var mobilities = mobilityRepository.findAllByIdIn(mobilityIds)

    //db에 없는 id가 하나라도 있으면 예외처리
    if (mobilities.size != mobilityIds.size) {
            return Response(MOBILITY_NOT_FOUND)
    }

    //db에 저장된 모빌리티들의 원래 위치 정보
    var originalMobilityMap = mobilities.associateBy { it.id }
    //매개변수로 입력받은 위치 정보(db에 업데이트 해야 할)
    var changeMobilityInfoMap = dto.mobilityInfoList.associateBy { it.id }

    var redisBicycles = ArrayList<RedisBicycle>()
+   var redisKickboards = ArrayList<RedisKickboard>()
+   var redisMotorcycles = ArrayList<RedisMotorcycle>()
+   var redisMobilityPublishDataList = ArrayList<RedisMobilityPublishData>()

    //mobilityIds을 반복을 돌리면서 mobilityId를 기준으로 원래 정보와 바꿔야할 정보들을 구해서 변경시킨다.
+   mobilityIds.forEach {
+       var mobility = originalMobilityMap[it]!!
        var updateMobilityInfo = changeMobilityInfoMap[it]!!
+
+       //dirty-checking(db) 부모 클래스의 메소드를 이용해 정보변경
        mobility.changeMobilityPos(updateMobilityInfo.lat, updateMobilityInfo.lng)

        //부모클래스의 메소드를 오버라이드하여 각자 자기 타입에 맞는 값을 구함.
+       when (mobility.getMobilityType()) {
+           "kickboard" -> {
                //db에 저장된 정보와 redis repository의 정보 불일치 문제가 발생할 수 있어서 무조건 새로 저장함.
+               redisKickboards.add(
                    RedisKickBoard(
                        id = mobility.id,
                        lat = mobility.lat,
                        lng = mobility.lng,
                    )    
                )
+           }
+
+           "bicycle" -> {
+               redisBicycles.add(
                    RedisBicycle(
                        id = mobility.id,
                        lat = mobility.lat,
                        lng = mobility.lng,
                    )    
                )
+           }
+
+           "motorcycle" -> {
+               redisMotorcycles.add(
                    RedisMotorcycle(
                        id = mobility.id,
                        lat = mobility.lat,
                        lng = mobility.lng,
                    )    
                )
+           }
        }
+
        //publish할 데이터 저장
+       redisMobilityPublishDataList.add(
+           RedisMobilityPublishData(
                id = mobility.id,
+               mobilityType = mobility.getMobilityType(),
+               lat = personalMobility.lat,
+               lng = personalMobility.lng,
+            )
+       )
    }
 
    try {
            //redis update
+           kickboardRedisRepository.saveAll(redisKickboards)
+           bicycleRedisRepository.saveAll(redisBicycles)
+           motorcycleRedisRepository.saveAll(redisMotorcycles)
+           //redis publish
+           redisTemplate.convertAndSend(
                update.topic,
                message
            )
    } catch (_: Exception) {
        return Response(INVALID_PARAMETERS)
    }

    return Response(OK)
}
```

db에 저장하는 엔티티의 경우 상속과 다형성으로 인해 타입별로 구분없이 한 줄로 해결할 수 있었으나  

redis의 경우 현재 상속과 다형성의 개념이 적용되지 않아서 각자 레포지토리에 저장하느라 중복되는 코드가 많습니다.  

다음으로는 redis repository에 저장된 모든 데이터들을 publish 형태로 출력하는 getAllMobilities메소드를 구현합니다.  

## getAllMobilities 메소드
```kotlin
fun getPersonalMobilities(): List<RedisMobilityPublishData> {
    var redisMobilityPublishDataList = ArrayList<RedisMobilityPublishData>()

    kickboardRedisRepository.findAll().forEach {
        redisMobilityPublishDataList.add(
            RedisMobilityPublishData(
                id = it.id,
+               mobilityType = "kickboard",
                lat = it.lat,
                lng = it.lng,
            )
        )
    }

+   bicycleRedisRepository.findAll().forEach {
        redisMobilityPublishDataList.add(
            RedisMobilityPublishData(
                id = it.id,
+               mobilityType = "bicycle",
                lat = it.lat,
                lng = it.lng,
            )
        )
    }


+   motorcycleRedisRepository.findAll().forEach {
        redisMobilityPublishDataList.add(
            RedisMobilityPublishData(
                id = it.id,
+               mobilityType = "motorcycle",
                lat = it.lat,
                lng = it.lng,
            )
        )
    }

    return redisMobilityPublishDataList
}
```
현재 getPersonalMobilities의 경우도 상속개념과 다형성을 적용하지 않아서  

각자 repository에서 값을 가져와야하고,

무엇보다도 mobilityType에 하드코딩으로 값을 직접 입력하느라  

코드 중복이 심합니다.  

이 중복되는 코드를 줄이기 위해서 redis entity와 redis repository에 상속개념과 다형성이 적용되는지 실험을 하였고,  

성공하여 그 결과 코드 중복을 많이 줄일 수 있었습니다.  


### 상속 개념을 적용한 redis entity 클래스
부모클래스인 redisMobility 클래스를 정의합니다.  
```kotlin
@RedisHash("mobility")
+class RedisMobility(
+    @Id
+    val id: String,
+    var lat: Double? = null,
+    var lng: Double? = null
+) {
        open fun getMobilityType(): String {
            return "moblility"
        }
}
```
getPersonalMobilities에서 타입을 하드코딩으로 입력하지 않기 위해서  

getMobilityType을 정의하고 자식들이 각자 타입에 맞게 이를 오버라이딩합니다.  


```kotlin
@RedisHash("bicycle")
+class RedisBicycle(
    id: String,
    lat: Double? = null,
    lng: Double? = null
) : RedisMobility(
    id = id,
    lat = lat,
    lng = lng
) {
    override fun getMobilityType(): String {
        return "bicycle"
    }
}
```
```kotlin
@RedisHash("kickBoard")
+class RedisKickBoard(
    id: String,
    lat: Double? = null,
    lng: Double? = null
) : RedisMobility(
    id = id,
    lat = lat,
    lng = lng
) {
    override fun getMobilityType(): String {
        return "kickBoard"
    }
}
```
```kotlin
@RedisHash("motorcycle")
+class RedisMotorcycle(
    id: String,
    lat: Double? = null,
    lng: Double? = null
) : RedisMobility(
    id = id,
    lat = lat,
    lng = lng
) {
    override fun getMobilityType(): String {
        return "motorcycle"
    }
}
```

### MobilityRedisRepository 정의
```kotlin
interface MobilityRedisRepository: CrudRepository<RedisMobility, String> {
}
```

## redis에 상속과 다형성을 적용하여 코드 리팩토링
### updateMobilities 메소드 리팩토링
```kotlin
@Transactional
fun updateMobilities(dto: UpdateMobilitiyDto): Response {
    var mobilityIds = dto.mobilityInfoList.map { it.id }

    //해당 id의 모든 mobility를 찾는다. 반환형은 부모인 Mobility의 리스트
    var mobilities = mobilityRepository.findAllByIdIn(mobilityIds)

    //db에 없는 id가 하나라도 있으면 예외처리
    if (mobilities.size != mobilityIds.size) {
            return Response(MOBILITY_NOT_FOUND)
    }

    //db에 저장된 모빌리티들의 원래 위치 정보
    var originalMobilityMap = mobilities.associateBy { it.id }
    //매개변수로 입력받은 위치 정보(db에 업데이트 해야 할)
    var changeMobilityInfoMap = dto.mobilityInfoList.associateBy { it.id }

    var redisMobility: RedisMobility?
    var redisMobilities = ArrayList<RedisMobility>()
+   var redisMobilityPublishDataList = ArrayList<RedisMobilityPublishData>()

    //mobilityIds을 반복을 돌리면서 mobilityId를 기준으로 원래 정보와 바꿔야할 정보들을 구해서 변경시킨다.
+   mobilityIds.forEach {
+       var mobility = originalMobilityMap[it]!!
        var updateMobilityInfo = changeMobilityInfoMap[it]!!
+
+       //dirty-checking(db) 부모 클래스의 메소드를 이용해 정보변경
        mobility.changeMobilityPos(updateMobilityInfo.lat, updateMobilityInfo.lng)

        //추상 팩토리 메소드 패턴을 적용해서 redisMobility 생성(다형성 적용)
        redisMobility = redisMobilityFactory(mobility)
            ?: return Response(INVALID_PARAMETERS)
        redisMobilities.add(redisMobility!!)
+
        //publish할 데이터 저장
+       redisMobilityPublishDataList.add(
+           RedisMobilityPublishData(
                id = mobility.id,
+               mobilityType = mobility.getMobilityType(),
+               lat = personalMobility.lat,
+               lng = personalMobility.lng,
+            )
+       )
    }
 
    try {
+           //redis update
            mobilityRedisRepository.saveAll(redisMobilities)
+           //redis publish
+           redisTemplate.convertAndSend(
                update.topic,
                message
            )
    } catch (_: Exception) {
        return Response(INVALID_PARAMETERS)
    }

    return Response(OK)
}

fun redisMobilityFactory(mobility: Mobility): RedisMobility? {
    return when (mobility.getMobilityType()) {
        "kickboard" -> {
            RedisKickboard(
                id = mobility.id,
                lat = mobility.lat,
                lng = mobility.lng
            )
        }

        "bicycle" -> {
            RedisBicycle(
                id = mobility.id,
                lat = mobility.lat,
                lng = mobility.lng
            )
        }

        "motorcycle" -> {
            RedisMotorcycle(
                id = mobility.id,
                lat = mobility.lat,
                lng = mobility.lng
            )
        }

        else -> {
            null
        }
    }
}
```
보시다시피 redis에 다형성을 적용하여 추상 팩토리 메소드 패턴을 활용할 수 있었고,  

아까보다 코드를 확연히 줄일 수 있게 되었습니다.  

그리고 여기서 알게 된 것은 바로 다음과 같습니다.  

## Spring Data redis 적용 범위
제가 실험한 바로는 Spring Data redis는 위의 코드에서 보시다시피  

**MobilityRedisRepository**에 **RedisMobility**를 저장하면  

**자식(RedisKickBoard, RedisBicycle, RedisMotorCycle)**들의 repository에 분리해서 저장을 해줍니다.  

이를 실제로 콘솔창에서 확인해보면 key 값은 자**식의 RedisHash 값인 kickBoard, bicycle, motorCycle**로 들어가고,  

**부모의 RedisHash인 mobility**는 **empty**로 뜹니다.  

즉, MobilityRedisRepository에 다형성을 활용하여 RedisMobility를 저장하면  

redis repository는 부모인 mobility로 key값을 형성하지 않고,  

자식들의 RedisHash 값인 kickBoard, bicycle, motorCycle을 활용하여 key를 생성하고,  

자식들의 key값에 value를 저장합니다.  

부모의 repository는 단지 자식들을 쉽게 분류해서 저장하도록 돕는 역할만 하고 자기 자신은 정작 아무것도 저장하지 않습니다.  

처음에는 save에서 jpa repository처럼 사용할 수 있으니까 find나 delete에도 다 적용이 될 줄 알았습니다.  

그러나 MobilityRedisRepository에서 deleteAll이나 findAll을 해도  

자식들의 repository에 저장된 redis data들을 지워지거나 찾아지지 않습니다.  

제가 실험한 한에서는 아직 해법을 찾지 못했습니다(누가 알려주시면 감사합니다...)  

추측으로는 MobilityRedisRepository에서 save를 할 때 자식들의 repository로 나눠서 저장해주는 기능만 수행하고,  

정작 자신은 비어있기 때문에 당연히 deleteAll이나 findAll을 해도 자기 자신이 비어있기 때문에 당연히 아무 일도 일어나지 않는 것 같습니다.  

이로 인해 getPersonalMobilities 메소드를 리팩토링할 때 고심하게 되었습니다.  

### getPersonalMobilities 메소드 리팩토링
MobilityRedisRepository에서 findAll을 호출하여 emptyList가 반환되기 때문에 어쩔 수 없이  

지식들의 repository에서 각각 findAll을 호출해야 하는 번거로움이 있었습니다.  

여기서 생각난 것이 3번 호출하는 것은 바꿀 수 없지만  

RedisMobilityPublishData를 만들어 List에 저장하는 과정은 한 번으로 줄일 수 있겠다는 생각이 들었습니다.  

일단 부모클래스인 RedisMobility애서 getMobilityType 메소드를 정의하고 자식들이 이를 오버라이딩하면  

아까 위에서 mobilityType을 하드코딩으로 3번 나눠서 넣던 것을 getMobilityType 한 번의 호출로 대체할 수 있습니다.  

그 결과 아래와 같이 코드 중복을 줄일 수 있었습니다.  

```kotlin
fun getPersonalMobilities(): List<RedisMobilityPublishData> {
    return  (
                kickboardRedisRepository.findAll() +
                bicycleRedisRepository.findAll() +
                motorcycleRedisRepository.findAll()
            ).map {
                    RedisMobilityPublishData(
                        id = it.id,
                        mobilityType = it.getMobilityType(),
                        lat = it.lat,
                        lng = it.lng
                    )
                }

}
```

이처럼 redis에서도 다형성을 활용하면 코드를 간걀하게 짤 수 있습니다.  
