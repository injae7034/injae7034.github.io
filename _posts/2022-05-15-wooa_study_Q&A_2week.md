---
layout: single
title: "우아한스터디-2주차-스터디-Q&A-유스케이스의-입력-모델과-웹-어댑터의-입력-모델-사이의-차이는-무엇인가?"
categories: Java
tag: [Java, 우아한스터디, 만들면서 배우는 클린 아키텍쳐, 4장, 5장, 6장]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 만들면서 배우는 클린 아키텍쳐 스터디 2주차 Q&A
# 유스케이스의 입력 모델과 웹 어댑터의 입력 모델 사이의 차이는 무엇인가?

4장에서 유스케이스의 입력 모델에 대한 설명이 나오고, 5장에서는 유스케이스와는 별도인 웹 어댑터의 입력 모델에 대한 설명이 나옵니다.  

책에서는 유스케이스의 입력 모델에 대해서는 자세한 설명과 예시를 들고 있지만, 웹 어댑터의 입력 모델에 대해서는 간단한 설명만 제시하고, 따로 예시를 주지 않습니다.  

그래서 책을 읽으면서 이 점이 궁굼하였습니다.  

안그래도 제가 듣고 있는 김영한님의 강의에서 객체form이라는 클래스를 통해 뷰로부터 데이터를 주고 받고 있는데 이것이 웹 어댑터의 입력 모델인가라는 생각이 들었습니다.  

다음은 form 모델에 대한 코드입니다.  

아래 코드는 현재 읽고 있는 이 책과 듣고 있는 김영한님의 강의를 바탕으로 진행하고 있는 주소록 프로그램 웹 프로젝트의 코드 중 일부입니다.  

각 유스케이스마다 세분화된 클래스를 만들고, 유스케이스의 입력 모델도 세분화하고, 웹 어댑터도 세분화하고 웹 어댑터 입력 모델인 form도 세분화하였습니다.  

## 웹 어댑터의 입력 모델

```java
@Getter @Setter
public class RecordPersonalForm {

    @NotEmpty(message = "이름은 필수입니다.")
    private String name;
    @NotEmpty(message = "주소는 필수입니다.")
    private String address;
    @NotEmpty(message = "전화번호는 필수입니다.")
    private String telephoneNumber;
    @Email(message = "이메일 형식을 지켜주세요.")
    private String emailAddress;

}
```

뷰와 컨트롤러 사이에서 데이터를 주고 받고 위해 getter와 setter가 필수이기 때문에 롬복의 Getter와 Setter 애노테이이션을 붙였습니다.  

그리고 이름과 주소, 전화번호의 경우 NotEmpty 애노테이션을 통해 뷰에서 클라이언트가 해당 필드를 빈 칸인채로 등록을 하면 에러가 발생하여 다음 단계로 넘어가지 않고 안내 message를 뷰에 출력하여 안내 message에 따르도록 하였습니다.  

이메일의 경우 비어있는 것은 상관없지만 이메일 형식을 지키지 않을 경우 에러가 발생하여 다음 단계로 넘어가지 않고 안내 message를 뷰에 출력하여 안내 message를 따르도록 하였습니다.  

## 유스케이스의 입력 모델

```java
@Value
@EqualsAndHashCode(callSuper = false)
public class RecordPersonalCommand
        extends PersonalCommandValidating<RecordPersonalCommand> {

    @NotNull @NotEmpty
    private final String name;
    @NotNull @NotEmpty
    private final String address;
    @NotNull @NotEmpty
    private final String telephoneNumber;
    @Email
    private final String emailAddress;

    public RecordPersonalCommand(String name, String address,
                                 String telephoneNumber, String emailAddress) {
        this.name = name;
        this.address = address;
        this.telephoneNumber = telephoneNumber;
        this.emailAddress = emailAddress;
        this.validatePersonalCommand();
    }
}
```
롬복의 Value 애노테이션을 클래스 상단에 명시함으로써 이 클래스는 필드를 변경할 수 없는 '불변 객체'가 됩니다.  

유스케이스의 입력 모델의 경우 생성자에서 입력에 대한 유효성 검증을 하여 한 번 생성이 되면 입력 유효성에 대한 검증이 끝난 객체가 생성됩니다.  

그러나 생성 후에 값 변경을 가능하게 해버리면 이 객체가 생성 이후 값이 변경되었는지 아닌지 따로 구분을 해야하기 때문에 생성자에서 했던 유효성 검증이 의미가 없어집니다.  

그래서 생성자에서 유효성 검증을 하면서 값을 초기화한 다음에는 더이상 값을 변경할 수 없게 막아서 이 객체에 있는 값은 항상 유효한 값이라는 것을 보장할 필요가 있기 때문에 클래스 상단에 lombok의 애노테이션을 붙이고 필드멤버들은 모드 private final로 설정합니다.  

이 후 매개변수를 가지는 생성자에서 필드멤버를 저장할 값들을 입력 받아 이 값들을 각 필드멤버에 저장하고, 미리 정의해둔 validatePersonalCommand를 호출하여 이 값들이 유효한 값인지 체크하여 값이 유효하면 정상적으로 객체가 생성되고, 값이 유효하지 않으면 예외를 발생시켜 객체가 생성되지 못하게 막습니다.  

## 웹 어댑터의 입력 모델과 유스케이스의 입력 모델의 코드 비교 후 생긴 의문점
위 두 입력 모델의 코드(제가 책과 예제를 바탕으로 스스로 작성한 코드이기 떄문에 완벽한 코드는 아닙니다.)를 보면 필드멤버에서 입력 유효조건에서 큰 차이가 없음을 알 수 있습니다.  

그래서 유스케이스의 입력 모델과 웹 어댑터의 입력 모델의 역할 차이를 정확하게 다시 한번 확인하고 싶었습니다.  

그래서 우아한 스터디 시간에 구체적인 설명없이 단순히 위와 같은 form클래스에 대한 웹 어댑터 대한 질문을 하였고, 좋은 대답을 얻을 수 있었습니다.(이것이야말로 우문현답...)  

별개로 다음부터 질문을 글로 미리 정리해놔야할 거 같습니다.  

(질문을 글로 정리해놓지 않으니 이야기하려고 하는데 막상 긴장되서 제대로 질문을 못했습니다ㅠㅠ 그래도 제 질문을 찰떡같이 알아들으시고, 좋은 대답을 주셔서 정말 감사합니다.)  

## 웹 어댑터 입력 모델과 유스케이스 입력 모델의 차이점
우선 웹 어댑터 입력 모델의 경우 데이터베이스에 접근할 필요없이 웹 화면 상에서 바로 처리할 수 있는 유효성만 검증합니다.  

즉, 이메일의 형식이 잘못되었을 경우 이는 웹 화면상에서 바로 알 수 있기 때문에 바로 예외처리할 수 있기 때문에 이는 웹 어댑터의 입력 모델에서 검증합니다.  

또한 전화번호 형식, 아이디 형식, 아이디 글자 수 제한, 비밀번호 글자 수 제한 등 이러한 표면적이고 형식적인 것들은 모두 바로 확인이 가능하기 때문에 웹 어댑터의 입력 모델에서 유효성 검증을 통해 처리합니다.  

반면에 유스케이스의 입력 모델에서 하는 유효성 접근은 데이터베이스 접근할 필요가 있는 경우입니다.  

예를 들어 아이디 중복을 체크하기 위해서는 웹 화면 상에서만 유효성 검증을 할 수 없습니다.  

왜냐하면 현재 클라이언트가 입력한 아이디가 이전에 누가 이미 등록한 아이디인지를 웹 화면 상에서는 바로 알 수 없기 때문입니다.  

이를 확인하기 위해서는 유스케이스의 입력 모델에서 유효성 검증을 해야하는데 이 때 데이터베이스에 접근하여 클라이언트가 현재 입력한 아이디가 데이터베이스에 이미 있는지 없는지를 확인하여야 합니다.  

그래서 클라이언트가 입력한 아이디가 데이터베이스에 없으면 아이디 중복 검사를 통과하여 클라이언트는 해당 아이디를 사용하여 새로운 계정을 만들 수 있고, 만약에 데이터베이스에 이미 그 아이디가 있다면 중복 예외 처리를 하여 클라이언트로 하여금 다른 아이디를 입력하도록 만들어야 합니다.  

즉, 웹 화면 상에서 바로 처리할 수 있으면 웹 어댑터의 입력 모델이 검증해야 할 몫이고, 그게 아니고 데이터베이스에 접근할 필요가 있는 경우는 유스케이스의 입력 모델이 검증해야 할 몫입니다.  

## 웹 어댑터 입력 모델과 유스케이스 입력 모델의 차이점을 바탕으로 코드 리팩토링
현재 위의 두 입력 모델에서는 입력 유효성을 검증하는 것에서 중복이 있습니다.  

즉, 웹 어댑터의 입력 모델에서 이미 입력 유효 검증을 마친 것을 유스케이스 입력 모델에서 불필요하게 중복으로 다시 입력 유효성을 검증하고 있습니다.  

이제 위에서 언급한 차이점을 바탕으로 리팩토링하여 각 코드들의 중복을 제거해보려고 합니다.  

현재 제가 구현하고 있는 주소록 프로그램에서는 아이디가 없기 때문에 아이디를 검증하지 못하는 것이 아쉽기는 합니다만 일단 주어진 조건에 대해서 웹 어댑터와 유스케이스의 입력 모델에 대한 차이를 바탕으로 코드를 리팩토링합니다.  

## 리팩토링한 유스케이스의 입력 모델
```java
@Value
@EqualsAndHashCode(callSuper = false)
public class RecordPersonalCommand
        extends PersonalCommandValidating<RecordPersonalCommand> {

    @NotNull
    private final String name;
    @NotNull
    private final String address;
    @NotNull
    private final String telephoneNumber;

    private final String emailAddress;

    public RecordPersonalCommand(String name, String address,
                                 String telephoneNumber, String emailAddress) {
        this.name = name;
        this.address = address;
        this.telephoneNumber = telephoneNumber;
        this.emailAddress = emailAddress;
        this.validatePersonalCommand();
    }
}
```
이름과 주소, 전화번호에 NotEmpty 애너테이션을 제거하였습니다.  

그 이유는 웹 어댑터의 입력 모델에서 이미 비어있는 경우 예외를 발생시키기 때문에 유스케이스에서는 별도로 이에 대한 처리를 해 줄 필요가 없기 때문입니다.  

또한 이메일의 이메일 애너테이션도 제거하였는데 이것도 마찬가지로 웹 어댑터의 입력 모델에서 이메일 형식을 지키도록 강제하고 있기 때문에 유스케이스에서는 더이상 이메일 형식을 불필요하게 다시 체크할 필요가 없습니다.  

웹 어댑터의 경우 코드가 기존과 똑같기 때문에 별도로 수정할 필요가 없습니다.  

이상으로 제가 질문한 1가지에 대한 Q&A를 마칩니다.  

이 답변은 스터디시간에 실무자분들이 이야기해준 것을 그대로 적은 것이 아닙니다.(제가 완전히 모든 대화 내용을 기억하지 못합니다ㅠ)  

그 때 대화를 회상하면서 기억이 나는 대화 내용과 제가 저 나름대로 그분들의 말씀을 이해하기 위해 제 생각을 덧붙였기 때문에 정확히 그 분들의 의견이라고 말하기는 어렵습니다.  

말씀하셨던 것 중에 모르는 내용은 건너뛰고, 최대한 아는 내용 중에서 더 깊게 이해하기 위해 제 배경지식을 같이 적었습니다.  

긴 글을 읽어주셔서 감사합니다^^
