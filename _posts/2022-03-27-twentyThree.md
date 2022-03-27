---
layout: single
title:  "소프트웨어 개발의 지혜 - 스프링 편 1주차 회고"
categories: Java
tag: [Java, 코드숨]
toc: true
toc_label: "목차"
toc_sticky: true
---
# 코드숨 - 스프링 1주차 회고
임베디드 개발자로 취업하여 일하다가 적성에 맞지 않음을 깨닫게 되어 퇴사하게 되었습니다.<br>
이전에는 C와 C++를 공부하고 데스크톱 어플리케이션을 주로 만들어봤었는데<br>
이를 부분적으로 java로 옮기면서 혼자 자바 공부를 하다가<br>
스스로 잘하고 있는건지 불안한 마음도 들고, 나태해지기도 하여<br>
혼자서는 쉽지 않겠다는 생각을 하게 되었습니다.<br>
그래서 어떤 강의를 들을까 찾아보다가 이전에 코드숨을 신청하신 분의 블로그 글을 보고<br>
코드숨을 알게되어 신청해야겠다는 마음을 먹었습니다.<br>
## 처음 강의를 들을 때 느낌
오래 전에 생활코딩을 보고 HTML, CSS, Javascript를 이용하여 간단한 정적 홈페이지를 하나 만들어본 경험이 있었습니다.<br>
그 이후로 처음으로 웹 관련 프로젝트를 해보는데 모든 것이 다 생소했습니다.<br>
처음으로 cmd를 통해 gradle로 java파일을 생성했는데 그렇게 하니까 test랑 main 두 개 파일이 생긴 것이 신기했습니다.<br>
또한 HTTPPie를 이용해서 따라 하는데 그냥 아샬님 따라 설치만 하고 빨리 강의를 들으면 따라 듣다  보니 이게 정확하게 뭘하는건지 기억이 안나요;;<br>
검색해봐야겠네요...<br>
물론 REST API는 워낙 유명해서 대략적으로 뭔지는 알 것 같았으나 실제로 프로젝트를 해본 적은 없었습니다.<br>
그래서 강의를 들을 때마다 모르는 게 너무 많아서 처음에는 하나 하나 찾아보다보니 강의 진도가 너무 느려 일단은 넘어가고, 아샬님이 하시는 대로 그대로 따라 했습니다.<br>

## 처음 과제를 할 때 느낌
강의를 다 듣고 과제(ava를 이용해서 ToDo REST API)를 보는데 처음에는 어떻게 구현할지 너무 막막했습니다.<br>
일단은 아샬님이 힌트를 주신 것으로 최대한 해보자는 생각으로 했습니다.<br>
일단은 강의를 듣고 따라 하다보니 첫날은 시간이 너무 모자라서 강의 내용 말고는 구현한 게 없었습니다.<br>

## 난생 처음으로 깃허브 Pull Request
이걸 어떻게 하는지 설명을 친절하게 적어 놓으셨으나 처음해보니까 무슨 말인지 모르겠고<br>
코드리뷰 제한 시간까지 빨리 제출해야한다는 급한 마음에 터미널에 명령어 순서를 잘못 입력하여<br> 작성해놓은 코드가 모두 초기화되어 멘붕이 왔습니다.<br>
다음 날 차분하게 다시 설명을 읽으면서 천천히 하니까 성공하여 그 날 드디어 코드리뷰를 받을 수 있게 되었습니다.<br>

## 과제를 하면서 느낀 점
처음해보는 것이라 굉장히 막막하고 못할 거 같았는데 막상 해보니까 과제의 결과처럼 일단 돌아가도록 구현하는 것은 생각보다 쉬웠습니다.<br>
그러나 코드리뷰를 받고 스스로 리팩토링해보면서 부족한 점을 많이 느껴 코드를 굉장히 많이 수정했습니다.<br>
그 과정에서 배운 것이 많았고, 이래서 코드리뷰와 리팩토링이 중요하다는 것을 깨닫게 되었습니다.<br>

## 이번 주 과정에서 아쉬운 점
월요일에 강의만 듣다가 시간을 너무 소요하고, 당황하여 Pull Request를 제대로 못하여 코드리뷰를 받지 못한 점이 아쉽습니다.<br>
주말에 약속이 있어서 토요일에 코드리뷰를 받지 못한 점이 아쉽습니다.<br>

## 다음 주 각오
다음 주 과제도 분명히 저는 모르는 내용일테지만 당황하지 말고<br>
차분하게 집중하여 잘 해결해보도록 하겠습니다.<br>
다음주는 꼭 월요일부터 코드리뷰를 받을  수 있도록 해야겠습니다.<br><br>

# 이번 주 코드리뷰를 받고 리팩토링하면서 수정한 과정들
긴 글 주의;;
<br>

# 화요일
## 어제 받았던 코드 리뷰를 반영하여 코드 수정함
### 1. App.java에 있던 8000을 LOCAL_HOST 상수로 변경함.
### 2.  ObjectMapper를 이용하지 않고 구현함.
그 과정에서 ObjectMapper사용하는 다른 메소드들은 쉽게 처리하였는데<br>
 toTask 메소드에서 String객체 body를 Task객체로 바꾸는 과정에서<br>
String객체가 한글일 경우 유니코드로 들어오는데 이를 그대로 입력하면 유니코드 그대로 출력이 되는 문제가 발생함.<br>
이를 한글로 변환하는 방법을 몰라서 방법을 찾느라 시간 소요를 많이 함.
### 3.  200, 201번이 중복되어서 여러 선택문에서 중복되는 코드가 발생함.
중복코드를 없애기 위해 int형 statusCode 변수를 생성하고 초기값을 200으로 설정함.<br>
POST에서만 statusCode값을 201로 변경해줌<br>
마지막에 httpExchange.sendResponseHeaders(statusCode, content.getBytes().length) 한줄만 작성하면 되도록 변경함.
### 4.  GET 메소드에서 현재 /tasks가 들어왔을 때만 전체 task를 출력하는 문제 발생
/tasks/가 들어와도 똑같은 결과가 나오도록 수정함.<br>
이를 위해 선택문에서 "|| path.equals("/tasks/") "을 추가함.
### 5. /tasks/a 가 들어오면 GET이나 DELETE에서  400 Bad Request가 발생함
이게 발생하는 이유는 Integer.valueof을 할 때 문자가 들어와서 NumberFormatException이 발생하기 때문임.<br>
이를 막기 위해 try catch문을 사용함.<br>
NumberFormatException이 발생하면 숫자를 입력하라는 안내 문구를 출력하도록 개선함.

## 기존에 작동이 안되었던 DELETE기능 돌아가도록 변경함
### 1. List의 remove메소드를 사용할 때 매개변수로 int형 대신 Integer를 넣으니까 작동하지 않았음.
이를 int형으로 바꿔서 매개변수로 넣어주니까 List의 remove메소드가 정상적으로 작동함.
### 2. DELETE한 후 Hello World!가 출력됨.
정상적으로 지워졌을 경우 content에 ""을 대입함으로써 Transfer-encoding: chunked 가 출력 되도록 수정함.
 
### 현재 과제에서 제시한 출력 결과가 나오도록 구현함

### 미흡한 점
하지만 아직 예외 처리가 완벽하지 않고, 코드 정리 또는 메소드 분리가 필요함.

# 수요일
## 어제 받았던 코드 리뷰를 반영하여 코드 수정함
### 1. App.java에 있던 8000을 LOCAL_HOST 상수로 변경함.
### 2.  ObjectMapper를 이용하지 않고 구현함.
그 과정에서 ObjectMapper사용하는 다른 메소드들은 쉽게 처리하였는데<br>
 toTask 메소드에서 String객체 body를 Task객체로 바꾸는 과정에서<br>
String객체가 한글일 경우 유니코드로 들어오는데 이를 그대로 입력하면 유니코드 그대로 출력이 되는 문제가 발생함.<br>
이를 한글로 변환하는 방법을 몰라서 방법을 찾느라 시간 소요를 많이 함.
### 3.  200, 201번이 중복되어서 여러 선택문에서 중복되는 코드가 발생함.
중복코드를 없애기 위해 int형 statusCode 변수를 생성하고 초기값을 200으로 설정함.<br>
POST에서만 statusCode값을 201로 변경해줌<br>
마지막에 httpExchange.sendResponseHeaders(statusCode, content.getBytes().length) 한줄만 작성하면 되도록 변경함.
### 4.  GET 메소드에서 현재 /tasks가 들어왔을 때만 전체 task를 출력하는 문제 발생
/tasks/가 들어와도 똑같은 결과가 나오도록 수정함.<br>
이를 위해 선택문에서 "|| path.equals("/tasks/") "을 추가함.
### 5. /tasks/a 가 들어오면 GET이나 DELETE에서  400 Bad Request가 발생함
이게 발생하는 이유는 Integer.valueof을 할 때 문자가 들어와서 NumberFormatException이 발생하기 때문임.<br>
이를 막기 위해 try catch문을 사용함.<br>
NumberFormatException이 발생하면 숫자를 입력하라는 안내 문구를 출력하도록 개선함.

## 미흡한 점
하지만 아직 예외 처리가 완벽하지 않고, 코드 정리 또는 메소드 분리가 필요함.

## 미흡한 점 개선
아까 올린 코드에서 BodyContent 에 미흡한 점을 어떻게 개선할 지 고민하다가 클래스보다는 DemoHttpHandler의 makeBodyContent 메소드를 가지는 것이 예외처리에 더 수월할 것 같다고 생각함.<br>
그래서 BodyContent 클래스를 제거하고, DemoHttpHandler의 makeBodyContent 메소드를 새로 정의함.<br>
그리고 DemoHttpHandler의 멤버로 BodyContent 클래스의 객체대신 String 객체로 변경함.

## 아쉬운 점
이로 인해 이제 title에 한글이나 영문이나 숫자가 들어와도 title에 저장하고 출력하는데 문제는 없지만<br>
현재 DemoHttpHandler에만 메소드가 너무 많이 과중된 느낌입니다.<br>
그리고 DemoHttpHandler가 가지고 있는 메소드가 과연 DemoHttpHandler가 가지고 있어야 하는 것이 맞는지 의문이 듭니다.<br>
따로 객체를 생성하여 메소드를 분리해서 가져갈 수 없는지 고민해봐야 할 거 같습니다.  
<br>

# 목요일

## 개선한 점
### 1. LOCAL_HOST 상수를 PORT로 변경
### 2. HTTP 메소드를 enum(HttpMethod)으로 정의해서 사용
멤버로 String method와 BiFunction\<Resource, Tasks, String\> operation을 설정함.<br>
httpExchange.getRequestMethod()에서 반환되는 문자열을 매개변수로 하여<br>
HttpMethod의 정적메소드인 findByHttpMethod를 이용해 HttpMethod 상수를 생성함.<br>
각 상수(POST, GET, PUT, DELETE)는 자기만의 식을 가짐.<br>
공통함수 operate를 설정하여 각 상수가 자신의 식을 호출하도록 정의함.<br>
### 3. Resource 클래스 생성
멤버로 String객체인 path와 content를 가짐<br>
어제 DemoHttpHandler가 기지고 있던 메소드를 Resource의 정적메소드인 makeBodyContent로 옮김.
### 4. 일급콜렉션 Tasks 생성
Tasks는 멤버로 List\<Task\> 하나를 가짐.<br>
DemoHttpHandler에서 Tasks와 관련된 모든 메소드를 Tasks 클래스의 메소드로 옮김.
### 5.  200과 201을 enum 정의해서 사용
StatusCode enum을 생성하여 상수로 OK와 Created을 두고 각각 200번과 201번을 부여함.<br>
처음에는 어제와 같은 방식으로 HttpMethod 상수가 가지고 있는 문자열이 POST이면<br>
StatusCode 상수를 201로 바꾸는 방식을 이용했지만 뭔가 마음에 들지 않아서<br>
HttpMethod enum클래스에 findByStatusCode메소드를 추가하여<br>
HttpMethod 상수가 POST면 StatusCode.Created를 반환하고<br>
나머지의 경우 StatusCode.OK를 반환하도록 변경함.
### 6.  안내 문구를 리턴대신 Optional클래스 사용 또는 예외를 던지기
```java
    public String getTaskToJSON(int index)  {
        return Optional.ofNullable(tasks.get(index))
                .map(Task::toString)
                .orElseThrow(() -> new NoSuchElementException("해당 id는 tasks에 없습니다."));

        /*
        if(index >= tasks.size()) {
            throw new NoSuchElementException("해당 id는 tasks에 없습니다.")
        }
        return tasks.get(index).toString();
         */
    }
```
Optinonal을 이용하여 위에 코드처럼 구현하거나 예외처리를 위해 아래 코드처럼 구현해보았습니다.<br>
그러나 Terminal에 없는 tasks를 GET하는 명령을 보내니 둘다 똑같은 결과<br>
**http: error: ConnectionError: ('Connection aborted.', RemoteDisconnected('Remote end closed connection without response')) while doing a GET request to URL: http://localhost:8000/tasks/1** <br>
가 발생하고 있는데 이것이 제대로 예외처리가 맞는지 의문이 듭니다.<br>
디버그를 해보니 일단 Optional의 경우 ofNullable(tasks.get(index))에서<br>
 List의 get메소드를 호출할 때 없는 인덱스를 입력하면 null이 나올 것이라고 예상했지만<br>
그게 아니라 바로 IndexOutOfBoundsException이 발생하기 때문에 위의 Optional코드는<br>
정상적인 index가 들어온 경우에만 동작하고 비정상적인 index가 들어오면 예외처리를 못하는 것으로 생각됩니다.<br>
그래서 아직은 Optional을 이용하여 어떻게 예외처리를 한 줄로 처리할지는 방법을 찾지 못했습니다.<br>
하지만 의문이 드는 것은 아래의 예외처리 코드에서는 정상적으로<br>
throw new NoSuchElementException("해당 id는 tasks에 없습니다.")를 하고 있는데 결과는 왜 Optional과 같은지는<br>
아직 원인을 찾지 못하였습니다. 

### 7.  import java.io에서 일정개수를 넘어가면 자동으로 \*처리되는 것 개선
DemoHttpHandler에서 io관련 import가 꽤 있었는데 어느 순간에 보니<br>
io의 모든 클래스를 import하는 것으로 자동으로 변경되어서<br>
안바뀌는 방법이 없나 찾아보다가 종립님의 Intellij 글을 보고 import개수를 5개에서 99개로 변경함.<br>
그 이후에는 5개가 넘어가도 자동으로 모든 클래스를 import하지 않음.

## 개선효과
이로써 DemoHttpHandler에는 멤버로 Tasks 일급컬렉션 멤버 하나와 handle 메소드 하나만 남게되고<br>
나머지 기능들은 전부 다른 클래스로 이전하게 되어 간소해짐.<br>

## 아쉬운 점
HttpHandler의 메소드 operate에서 매개변수로 이용하기 위해 Resource클래스를 생성하였는데<br>
Resource클래스 생성이 맞는지는 아직 확신이 들지 않습니다. <br>
또한 Optional과 예외처리에 대해서도 아직 처리가 되지 않아서 어떻게 해야 할지 좀 더 고민해봐야겠습니다.<br>
그리고 마지막으로 시간이 모자라기도 하고 영문이라 잘 읽히지 않아서  추천해주신 RFC 7231 영문서를 아직 읽지 못했습니다ㅠ<br>
영문으로 읽는 연습을 해야 하는데 아직 쉽지 않은 것 같습니다ㅠ

# 금요일

# 개선한 점
## 1. Tasks는 Tasks와 관련된 일만 하도록 변경함
기존 코드에서는 Tasks에서 json형태로 변경하는 것까지 하고 있었는데 <br>
어제 코드리뷰를 통해  변경할 필요성을 깨달음<br>
Task의 toString코드도 기존에는 json형태로 출력되도록 정의하였는데 이것도 개선함.<br>
### 1.1 Task의 toString코드
" "을 delimeter 상수로 설정하여 id와 title을 출력하도록 변경함
### 1.2 Tasks에 있는 JSON과 관련된 메소드를 모두 제거함.
### 1.3 Tasks에서 getTask(int index)메소드 새로 정의
index를 통해 Task를 구할 수 있도록 정의함.
### 1.4 Tasks 메소드 IndexOutOfBoundsException예외처리
set, remove, getTask 모두 index를 매개변수로 입력 받는데 이 때, index가 List\<Task\>의 index를 벗어나면<br>
IndexOutOfBoundsException예외가 발생하는데 이에 대한 예외처리를 해줌.<br><br>

## 2. Task를 JSON형태로 변경하기 위해 JsonPrinter 클래스 생성
JsonPrinter는 유틸리티클래스로 멤버는 하나도 없고, 2개의 정적 메소드를 가짐.<br>
### 2.1 printTask메소드
Task객체를 매개변수로 입력 받아서 하나의 Task만 JSON형태로 출력함.<br>
### 2.2 printAllTasks메소드
Tasks 객체를 매개변수로 입력 받아서 Tasks의 모든 Task들을 JSON에서 배열 형태로 출력함.

## 3. 예외처리
어제 "왜 throw를 던졌는데 예외처리가 안되지?"라는 바보같은 생각을 함;;<br>
당연히 throw만 던지고 그에 대한 처리(catch)를 안해주면 당연히 main이 마지막에 예외처리를 하기 때문에<br>
예외처리를 안한 것과 마찬가지인데 왜 혼자서 throw를 분명했는데 예외처리문구가 출력이 안되지라는 바보같은 의문을 가짐<br>
그래서 오늘은  **try catch**를 이용하여 **예외처리**해줌<br>

### 3.1 enum클래스인 StatusCode에 상수를 추가해줌.
StatusCode에 상수를 추가하지 않으면 **사용자가 잘못 입력하여 예외가 발생했는데도 200, OK가 뜨는 불상사가 발생**하기 때문<br>
그래서 BAD_REQUEST(400),  NOT_FOUND(404), METHOD_NOT_ALLOWED(405) 상수를 추가해줌.
### 3.2  enum클래스인 HttpMethod에 상수를 추가해줌.
현재 HttpMethod에는 4개의 상수(POST, GET, PUT, DELETE)가 있는데 사용자가 오타로 HTTP메소드를 잘못입력하거나<br>
정의하지 않은 다른 메소드를 입력할 경우에 대한 예외처리가 없었음.<br>
그래서 enum클래스인 HttpMethod에 WRONG_METHOD라는 상수를 추가해주고, operate 식으로는 안내 문구 문자열을 리턴함.<br>

### 3.3 Tasks 메소드에서 IndexOutOfBoundsException 예외처리
set, get, remove 메소드에서 IndexOutOfBoundsException이 발생할 수 있기 때문에 각각 다른 안내문구를 입력하여<br> 
예외가 발생하는 경우 IndexOutOfBoundsException예외를 throw함.<br>
set, get, remove 메소드는 enum클래스인 HttpMethod의 operate에서 호출하는데 여기서 예외처리를 하지 않고<br>
throws IndexOutOfBoundsException을 통해  operate 메소드를 호출한 DemoHttpHandler의 handle 메소드로 예외처리를 넘김<br>
DemoHttpHandler의 handle 메소드에서 try catch를 통해 IndexOutOfBoundsException이 발생한 경우<br>
IndexOutOfBoundsException의 객체에서 getMessage를 하여 content에 저장하고,<br>
StatusCode는 NOT_FOUND로 변경해줌.<br>
### 3.4 Resource 클래스의 makeBodyContent메소드에서 예외처리
POST를 할 때 body에 title을 입력하지 않고, body 내용을 기재하거나,<br>
title로 body 내용을 입력하고, 추가로 예를 들어 head등 다른 body 내용을 기재한 경우<br>
모드 IllegalArgumentException 예외를 발생시키고<br>
makeBodyContent를 호출한 DemoHttpHandler의 handle 메소드로 예외처리를 해줌.<br>
catch에서 IllegalArgumentException의 메세지를 구하고, statusCode는 BAD_REQUEST로 변경해줌.

### 3.5 title을 입력하고 =대신에 ==을 2번 입력할 경우 예외처리
이 때 Http메소드는 POST인데 body는 비어 있는 것으로 인식을 하여 예외처리를 안하면<br>
비어있는 상태로 Tasks에 추가한 다음 Create되었다는 오류가 발생함.<br>
이를 이해 DemoHttpHandler의 handle에서 statusCode가 POST인데 content가 비어있으면<br>
IllegalArgumentException 예외를 발생시키고 catch에서 IllegalArgumentException의 메세지를 구하고,<br>
statusCode는 BAD_REQUEST로 변경해줌.

### 3.6 사용자가 HTTP 메소드를 잘못 입력한 경우
이때는 HttpMethod의 정적 메소드인 findByHttpMethod에서 아무것도 찾지 못한 경우 WRONG_METHOD를 반환함<br>
WRONG_METHOD의 operate메소드를 호출하면 오류 안내 문구가 content에 반환될 것이고<br>
HttpMethod의 findByStatusCode를 통해 METHOD_NOT_ALLOWED(405)가 호출되면 이를 이용해<br>
화면에 출력하면 됩니다.

# 아쉬운 점
결국 Optional로는 예외처리를 해결하지 못함ㅠ<br>
예외처리를 많이 하다보니 코드에 try catch가 남발이 되어<br>
코드가 길어졌는데 이거 이렇게 하는 것이 맞나 싶은 의문이 듬;;