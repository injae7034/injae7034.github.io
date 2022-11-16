---
layout: single
title: "TMAP Open API를 활용해 도보 경로 안내 api 구현하기"
categories: Kotlin
tag: [kotlin, 스프링, JPA, OpenAPI, TMAP, 도보 경로 안내]
toc: true
toc_label: "목차"
toc_sticky: true
---

# TMAP Open API return값 확인 주소 (보행자 경로안내)
주소는 아래와 같습니다.  

https://skopenapi.readme.io/reference/%EB%B3%B4%ED%96%89%EC%9E%90-%EA%B2%BD%EB%A1%9C%EC%95%88%EB%82%B4

여기 들어가면 친절하게 옆에 무슨 값이 반환되는지 확인할 수 있게 잘 구성이 되어 있습니다.  

단, 이 Open API를 사용하기 전에 key를 발급 받아야 합니다.  

유효한 key를 발급 받아서 AUTHENTICATION의 header에 key 값을 넣어주고 try it을 실행하면  

어떤 반환값(RESPONSE)을 주는지 알 수 있습니다.  

이 반환값(RESPONSE)을 보면 어떠한 정보를 주는지 알 수 있고,  

어디서 어떤 정보를 구해야 하는지 더 나아가서 이 정보를 활용해 무엇을 할 수 있을지 정할 수 있습니다.  

또한 입력값(REQUEST)도 알 수 있는데 입력값(REQUEST)을 통해 어떠한 정보를 입력해야 원하는 결과를 얻을 수 있을지도 알 수 있습니다.  

## 입력값(REQUEST) 예시
필수값만 입력하였습니다.  

data의 필수값으로 출발지 위도 및 경도와 이름, 도착지 위도 및 경도와 이름이 있습니다.  

```json
curl --request POST \
     --url 'https://apis.openapi.sk.com/tmap/routes/pedestrian?version=1&callback=function' \
     --header 'accept: application/json' \
     --header 'appKey: 본인이 발급받은 유효한 key값 입력' \
     --header 'content-type: application/json' \
     --data '
{
    {
        "startX": 126.92365493654832,
        "startY": 37.556770374096615,
        "endX": 126.92432158129688,
        "endY": 37.55279861528311,
        "startName": "내위치",
        "endName": "홍익대앞"
    }
}
'
```

## 반환값(RESPONSE) 예시
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [
          126.92364851900282,
          37.556774278906374
        ]
      },
      "properties": {
        "totalDistance": 632,
        "totalTime": 513,
        "index": 0,
        "pointIndex": 0,
        "name": "",
        "description": "80m 이동",
        "direction": "",
        "nearPoiName": "",
        "nearPoiX": "0.0",
        "nearPoiY": "0.0",
        "intersectionName": "",
        "facilityType": "14",
        "facilityName": "",
        "turnType": 200,
        "pointType": "SP"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [
            126.92364851900282,
            37.556774278906374
          ],
          [
            126.92299025979091,
            37.55627432526626
          ]
        ]
      },
      "properties": {
        "index": 1,
        "lineIndex": 0,
        "name": "",
        "description": ", 80m",
        "distance": 80,
        "time": 80,
        "roadType": 24,
        "categoryRoadType": 0,
        "facilityType": "14",
        "facilityName": ""
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [
          126.92299025979091,
          37.55627432526626
        ]
      },
      "properties": {
        "index": 2,
        "pointIndex": 1,
        "name": "리본",
        "description": "리본 에서 좌회전 후 37m 이동 ",
        "direction": "",
        "nearPoiName": "리본",
        "nearPoiX": "0.0",
        "nearPoiY": "0.0",
        "intersectionName": "",
        "facilityType": "14",
        "facilityName": "",
        "turnType": 12,
        "pointType": "GP"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [
            126.92299025979091,
            37.55627432526626
          ],
          [
            126.92311525161186,
            37.55616045185638
          ],
          [
            126.92300137537782,
            37.556079903629936
          ],
          [
            126.92298748924466,
            37.55602713174048
          ]
        ]
      },
      "properties": {
        "index": 3,
        "lineIndex": 1,
        "name": "",
        "description": ", 37m",
        "distance": 37,
        "time": 36,
        "roadType": 24,
        "categoryRoadType": 0,
        "facilityType": "14",
        "facilityName": ""
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [
          126.92298748924466,
          37.55602713174048
        ]
      },
      "properties": {
        "index": 4,
        "pointIndex": 2,
        "name": "홍대입구역  9번출구",
        "description": "홍대입구역  9번출구 에서 우회전 후 136m 이동 ",
        "direction": "",
        "nearPoiName": "홍대입구역  9번출구",
        "nearPoiX": "0.0",
        "nearPoiY": "0.0",
        "intersectionName": "홍대입구역9번출구",
        "facilityType": "11",
        "facilityName": "",
        "turnType": 13,
        "pointType": "GP"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [
            126.92298748924466,
            37.55602713174048
          ],
          [
            126.92289583281521,
            37.55596047118556
          ],
          [
            126.92208203536136,
            37.55534941657116
          ],
          [
            126.92185983809875,
            37.55518276530824
          ]
        ]
      },
      "properties": {
        "index": 5,
        "lineIndex": 2,
        "name": "",
        "description": ", 136m",
        "distance": 136,
        "time": 98,
        "roadType": 24,
        "categoryRoadType": 1,
        "facilityType": "11",
        "facilityName": ""
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [
          126.92185983809875,
          37.55518276530824
        ]
      },
      "properties": {
        "index": 6,
        "pointIndex": 3,
        "name": "",
        "description": "좌회전 후 양화로 을 따라 5m 이동 ",
        "direction": "",
        "nearPoiName": "",
        "nearPoiX": "0.0",
        "nearPoiY": "0.0",
        "intersectionName": "홍대입구역",
        "facilityType": "11",
        "facilityName": "",
        "turnType": 12,
        "pointType": "GP"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [
            126.92185983809875,
            37.55518276530824
          ],
          [
            126.921820953617,
            37.55515221260984
          ]
        ]
      },
      "properties": {
        "index": 7,
        "lineIndex": 3,
        "name": "양화로",
        "description": "양화로, 5m",
        "distance": 5,
        "time": 3,
        "roadType": 21,
        "categoryRoadType": 1,
        "facilityType": "11",
        "facilityName": ""
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [
            126.921820953617,
            37.55515221260984
          ],
          [
            126.9218042898807,
            37.555102218125455
          ],
          [
            126.92181817883642,
            37.55505500164249
          ],
          [
            126.92209038290973,
            37.55481892285528
          ],
          [
            126.92214593481263,
            37.55476892966291
          ],
          [
            126.92247924583822,
            37.55448285778263
          ],
          [
            126.92254035255513,
            37.55444119705401
          ],
          [
            126.92289866232144,
            37.55411901870853
          ],
          [
            126.92299587795566,
            37.5540384742588
          ],
          [
            126.92419024238164,
            37.5530163922624
          ],
          [
            126.92420413031822,
            37.55300528269168
          ],
          [
            126.9243318970768,
            37.552983065339326
          ],
          [
            126.92438466941691,
            37.55300528592197
          ]
        ]
      },
      "properties": {
        "index": 8,
        "lineIndex": 4,
        "name": "홍익로",
        "description": "홍익로, 338m",
        "distance": 338,
        "time": 272,
        "roadType": 21,
        "categoryRoadType": 1,
        "facilityType": "11",
        "facilityName": ""
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [
          126.92438466941691,
          37.55300528592197
        ]
      },
      "properties": {
        "index": 9,
        "pointIndex": 4,
        "name": "호아빈 홍대점",
        "description": "호아빈 홍대점 에서 횡단보도 후 보행자도로 을 따라 20m 이동 ",
        "direction": "",
        "nearPoiName": "호아빈 홍대점",
        "nearPoiX": "0.0",
        "nearPoiY": "0.0",
        "intersectionName": "홍익대앞",
        "facilityType": "15",
        "facilityName": "",
        "turnType": 211,
        "pointType": "GP"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [
            126.92438466941691,
            37.55300528592197
          ],
          [
            126.92448466484962,
            37.5528441953326
          ]
        ]
      },
      "properties": {
        "index": 10,
        "lineIndex": 5,
        "name": "보행자도로",
        "description": "보행자도로, 20m",
        "distance": 20,
        "time": 13,
        "roadType": 21,
        "categoryRoadType": 1,
        "facilityType": "15",
        "facilityName": ""
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [
          126.92448466484962,
          37.5528441953326
        ]
      },
      "properties": {
        "index": 11,
        "pointIndex": 5,
        "name": "세븐일레븐 홍대정문점",
        "description": "세븐일레븐 홍대정문점 에서 우회전 후 와우산로 을 따라 16m 이동 ",
        "direction": "",
        "nearPoiName": "세븐일레븐 홍대정문점",
        "nearPoiX": "0.0",
        "nearPoiY": "0.0",
        "intersectionName": "홍익대앞",
        "facilityType": "11",
        "facilityName": "",
        "turnType": 13,
        "pointType": "GP"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [
            126.92448466484962,
            37.5528441953326
          ],
          [
            126.92437356512016,
            37.552799754067905
          ],
          [
            126.92432912519702,
            37.55278308854395
          ]
        ]
      },
      "properties": {
        "index": 12,
        "lineIndex": 6,
        "name": "와우산로",
        "description": "와우산로, 16m",
        "distance": 16,
        "time": 11,
        "roadType": 21,
        "categoryRoadType": 1,
        "facilityType": "11",
        "facilityName": ""
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [
          126.92432912519702,
          37.55278308854395
        ]
      },
      "properties": {
        "index": 13,
        "pointIndex": 6,
        "name": "%EB%8F%84%EC%B0%A9",
        "description": "도착",
        "direction": "",
        "nearPoiName": "%EB%8F%84%EC%B0%A9",
        "nearPoiX": "0.0",
        "nearPoiY": "0.0",
        "intersectionName": "홍익대앞",
        "facilityType": "",
        "facilityName": "",
        "turnType": 201,
        "pointType": "EP"
      }
    }
  ]
}
```

반환값은 좀 긴데 보시면 type에 FeatureCollection이라고 적혀 있습니다.  

즉, 반환값으로 복수의 Feature가 반환됨을 알 수 있습니다.  

또 이 Feature는 geometry와 properties로 이루어진 것을 알 수 있습니다.(type: Feature를 제외하고)  

geometry의 멤버는 type과 coordinates입니다.  

geometry의 type은 LineString과 Point로 분류됩니다.  

geometry의 coordinates는 type이 Point일 경유 현재 Point의 위도 및 경도(단수)를 나타내고,  

type이 LineString일 경우 LineString의 위도 및 경도(복수)를 나타냅니다.  

properties도 geometry의 type이 Point인지 LineString인지에 따라 필드멤버가 약간씩 차이가 있습니다.  

geometry의 type이 Point는 시작점, 도착점 그리고 경유지점을 나타내고,  

geometry의 type이 LineString은 각각지점마다 경로를 나타냅니다.   

**가장 중요한 정보인 totalDistance와 totalTime은 FeatureCollection의 가장 첫번쩨 Feature(geometry type: Point)의 properties에만 포함**되어 있습니다.  

그래서 총거리나 총소요시간을 알고 싶으면 첫번째 Feature의 properties에서 정보를 구해오면 됩니다.  

## TMAP Open API - 보행자 경로안내 Response값을 받을 DTO 정의
위의 Response값을 그대로 반영하되 본인이 사용하고 싶은 값들만 받도록 구현하면 됩니다.  

```kotlin
class PedestrianRoutesApiRes(
            val type: String = "",//FeatureCollection
            val features: List<Feature>? = null,
    ) {
        class Feature(
                val type: String? = null,//Feature
                val geometry: Geometry? = null,
                val properties: Property,
        ) {
            @JsonTypeInfo(
                    use = JsonTypeInfo.Id.NAME,
                    include = JsonTypeInfo.As.PROPERTY,
                    property = "type"
            )
            @JsonSubTypes(
                    JsonSubTypes.Type(value = Point::class, name = "Point"),
                    JsonSubTypes.Type(value = LineString::class, name = "LineString")
            )
            open class Geometry(
                    val type: String? = null,
            ){
                companion object {
                    const val GEOMETRY_TYPE_POINT = "Point"
                    const val GEOMETRY_TYPE_LINE_STRING = "LineString"
                }
            }
            class Point (
                    type: String = "Point",
                    var coordinates: List<Double>? = null
            ): Geometry(type)

            class LineString (
                    type: String = "LineString",
                    var coordinates: ArrayList<ArrayList<Double>> = ArrayList()
            ): Geometry(type)

            @JsonIgnoreProperties(ignoreUnknown = true)
            class Property(
                    @ApiModelProperty("meter")
                    var distance: Int? = null, // meter(LineString에만 존재함)
                    @ApiModelProperty("second")
                    var time: Int? = null, // second(LineString에만 존재함)

                    @ApiModelProperty("meter")
                    var totalDistance: Int? = null, // meter(첫 번째 Feature(Point)에만 존재함)
                    @ApiModelProperty("second")
                    var totalTime: Int? = null, // second(첫 번째 Feature(Point)에만 존재함)
            )
        }
    }
```

## TMAP Open API - 보행자 경로안내 Request값을 보낼 DTO 정의
```kotlin
class PedestrianRoutesApiReq(
    startName: String,
    endName: String,
    startX: Double,
    startY: Double,
    endX: Double,
    endY: Double
)   
```

## TMAP API Client 정의
**FeignClient**를 사용하고 url은 Tmap Open API 주소를 입력합니다.  

그리고 메소드는 위의 홈페이지 요구사항에 따라 PostMapping을 이용하고,  

FeignClient에 적은 url에 '/pedestrian'을 추가해주시면 됩니다.  

메소드 파라미터로는 아까 위의 request에 필요한 값들을 넘기면 되는데,  

일단 필수값만 넘기는 것으로 설정하였습니다.  

일단 해당 openAPI를 이용하기 위해서 appKey는 필수이기 때문에 첫번째 매개변수로 설정합니다.  

두번째로 version정보도 필수인데 "1"로 설정하여 매개변수로 설정합니다.  

나머지는 출발지 이름과 도착지 이름, 그리고 출발지 위도 및 경도, 도착지 위도 및 경도를 매개변수로 설정합니다.  

```kotlin
@FeignClient(
        name = "TMapAPI",
        url = "https://apis.openapi.sk.com/tmap/routes",
)
interface TMapAPIClient {
    @PostMapping(
        value = ["/pedestrian"],
        headers = ["Content-Type=application/x-www-form-urlencoded"]
    )
    fun findPedestrianRoutes(
        @RequestHeader
        appKey: String,
        @RequestParam("version")
        version: String,
        @RequestParam
        startName: String = "start",
        @RequestParam
        endName: String = "end",
        @RequestParam("startX")
        startX: Double,
        @RequestParam("startY")
        startY: Double,
        @RequestParam("endX")
        endX: Double,
        @RequestParam("endY")
        endY: Double
    ): PedestrianRoutesApiRes?
}
```

이렇게 구현하고 해당 메소드를 호출하면서 필요한 매개변수를 넘기면 우리가 구현한 DTO에 원하는 정보가 입력되어 반환됩니다.  

이제 이 메소드를 호출하는 로직을 구현할 차례입니다.  

## PedestrianRouteService 클래스 구현
```kotlin
@Service
class PedestrianRouteService {
    @Autowired
    lateinit var tMapApiClient: TMapAPIClient

    @Autowired
    lateinit var tMapApiKeys: TMapApiKeys


    fun findWalkingRoutes(
        req: PedestrianRoutesApiReq
    ): ResponseEntity<FindPedestrianRoutesRes> {

        try {
            // Tmap Open API 호출
            val response = tMapApiClient.findPedestrianRoutes(
                appKey = ApiKeys.getTmapApiKey(),//발급받은 유효한 key를 입력
                version = "1",//version은 1로 고정됨.
                startName = req.startName
                startX = req.startX,
                startY  = req.startY,
                endName = req.endName,
                endX = req.endX,
                endY = req.endY
            )

            //보행자 경로 생성
            val routes = buildRouteList(response)

            val findPedestrianRoutesRes = FindPedestrianRoutesRes(
                routes = routes,
                routesCount = routes.size,
                totalTimeSec = response?.features?.get(0)?.properties?.totalTime,
                totalDistanceMeter = response?.features?.get(0)?.properties?.totalDistance
            )

            return ResponseEntity<FindPedestrianRoutesRes>(findPedestrianRoutesRes, HttpStatus.OK)

        } catch (e: Exception) {
            e.printStackTrace()
            return ResponseEntity(HttpStatus.BAD_REQUEST)
        }
    }

    //type: Point는 제외하고, LineString경우만 추가해서 보행자 경로를 생성함.
    fun buildRouteList(
        response: PedestrianRoutesApiRes?
    ): ArrayList<ArrayList<Double>>{
        val routeList = ArrayList<ArrayList<Double>>()
        response?.features
            ?.forEach {
                if(it.geometry is FindWalkingRoutesApiRes.TmapFeature.LineString){
                    routeList.addAll(it.geometry.coordinates)
                }
            }
        return routeList
    }
}
```
```kotlin
class FindPedestrianRoutesRes(
    val routes: ArrayList<ArrayList<Double>>?,// 보행자 경로 위치(위도 및 경도) 리스트
    val routesCount: Int = 0,
    val totalTimeSec: Int? = null, // 초 (second)
    val totalDistanceMeter: Int? = null, // 미터 (meter)
)
```
이로써 TMAP OpenAPI를 통해 도보 길 안내 결과를 얻을 수 있습니다.  

# 더 나아기기
지금 이 TMAP OpenAPI를 호출하는 PedestrianRouteService가 있고, MSA 구조 하에서 도보 길 안내 결과를 얻고 싶어하는 다른 서비스 클래스가 있다고 가정합시다.  

예를 들어, 사용자가 폰으로 도보 길 찾기를 할 수 있는 모바일 서비스인 PedestrianRouteMobileService가 있다고 가정합니다.  

PedestrianRouteMobileService가 PedestrianRouteService의 findWalkingRoutes 메소드를 호출하여 사용하려면  

PedestrianRouteService가 TMAP OpenAPI를 호출했을 때처럼 FeignClient를 사용하면 됩니다.  

## PedestrianRouteServerClient 클래스 구현

```kotlin
@FeignClient(name = "PedestrianRouteServer", url = "PedestrianRouteService가 가동되고 있는 서버주소")
interface PedestrianRouteServerClient {
    @GetMapping("/map/routes/pedestrian")
    fun findPedestrianRoutes(
            @RequestBody req: PedestrianRoutesApiReq
    ): ResponseEntity<FindPedestrianRoutesRes>
}

```
PedestrianRouteServerClient에서 PedestrianRouteService가 가동되고 있는 서버에 접근하여  

PedestrianRouteService의 findWalkingRoutes 메소드를 호출하여 원하는 값을 반환받습니다.  


## PedestrianRouteMobileService 클래스 구현
```kotlin
class PedestrianRouteMobileService {

    @Autowired
    lateinit var pedestrianRouteServerClient: PedestrianRouteServerClient


    @Transactional
    fun getPedestrianRoutes(
        customerId: String,
        startLat: Double,
        startLng: Double,
        startName: String,
        endLat: Double,
        endLng: Double,
        endName: String
    ): GetPedestrianRoutesRes {

        var response = pedestrianRouteServerClient.findPedestrianRoutes(
            PedestrianRoutesApiReq(
                startName = startName,
                endName = endName,
                startX = startLat,
                startY = startLng,
                endX = endLat,
                endY = endLng
            )
        )

        if (response == null) {
            return GetWalkingRouteResultRes(
                UNKNOWN_ERROR
            )
        }

        //필요하게 가공한 후에 return

}
```

이렇게 MSA 환경에서는 어떻게 하면 되는지까지 알아봤습니다.  
