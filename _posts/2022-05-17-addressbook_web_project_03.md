---
layout: single
title: "헥사고날-아키텍쳐로-구현하는-작은-스프링-부트-토이-프로젝트-주소록-adapter-패키지"
categories: Java
tag: [Java, 우아한스터디, 만들면서 배우는 클린 아키텍쳐, 스프링 입문, 스프링 핵심 원리 기본편, 스프링 부트와 JPA 활용, 김영한]
toc: true
toc_label: "목차"
toc_sticky: true
---
personal 3가지 패키지(adapter, application, domain) 중에서<a href="https://injae7034.github.io/java/addressbook_web_project_01/" target="_blank">domain 패키지</a>와 <a href="https://injae7034.github.io/java/addressbook_web_project_02/" target="_blank">application 패키지</a>에 대한 설명을 하였고, 이제 adapter 패키지에 대한 설명을 하도록 하겠습니다.  

이 글의 제일 처음은 domain 패키지이고 다음이 application 패키지입니다.  

# 패키지 구성
```java
addressbook
|____common
|    |____PersonalCommandValidating
|
|____personal
|    |____adapter
|    |    |____in
|    |    |    |____web
|    |    |         |____correct
|    |    |         |    |____CorrectPersonalController
|    |    |         |    |____CorrectPersonalForm
|    |    |         |
|    |    |         |____erase
|    |    |         |    |____ErasePersonalController
|    |    |         |
|    |    |         |____find
|    |    |         |    |____FindPersonalController
|    |    |         |    |____FindPersonalForm
|    |    |         |
|    |    |         |____record
|    |    |         |    |____RecordPersonalController
|    |    |         |    |____RecordPersonalForm
|    |    |         |
|    |    |         |____HomeController
|    |    |
|    |    |____out
|    |         |____persistence
|    |              |____JpaCorrectPersonalRepository
|    |              |____JpaErasePersonalRepository
|    |              |____JpaFindPersonalRepositroy
|    |              |____JpaGetPersonalRepositroy
|    |              |____JpaGetPersonalsRepository
|    |              |____JpaRecordPersonalRepository
|    |
|    |____application
|    |    |____port
|    |    |    |____in
|    |    |    |    |____correct
|    |    |    |    |    |____CorrectPersonalCommand
|    |    |    |    |    |____CorrectPersonalUsecase
|    |    |    |    |
|    |    |    |    |____erase
|    |    |    |    |    |____ErasePersonalUseCase
|    |    |    |    |
|    |    |    |    |____find
|    |    |    |    |    |____FindPersonalCommand
|    |    |    |    |    |____FindPersonalUseCase
|    |    |    |    |
|    |    |    |    |____get
|    |    |    |    |    |____GetPersonalCommand
|    |    |    |    |    |____GetPersonalQuery
|    |    |    |    |    |____GetPersonalsQuery
|    |    |    |    |
|    |    |    |    |____record
|    |    |    |         |____RecordPersonalCommand
|    |    |    |         |____RecordPersonalUseCase
|    |    |    |
|    |    |    |____out
|    |    |         |____CorrectPersoanlRepository
|    |    |         |____ErasePersonalRepository
|    |    |         |____FindPersonalRepository
|    |    |         |____GetPersonalRepository
|    |    |         |____GetPersonalsRepository
|    |    |         |____RecordPersonalRepository
|    |    |
|    |    |____service
|    |         |____CorrectPersonalService
|    |         |____ErasePersonalService
|    |         |____FindPersonalService
|    |         |____GetPersonalService
|    |         |____GetPersonalsService
|    |         |____RecordPersonalService
|    |
|    |____domain
|         |____Personal
|    
|____AddressBookApplication
```

# adapter 패키지
adapter 패키지에는 in과 out 패키지가 있습니다.  

분량과 글의 흐름상 out 패키지를 먼저 설명하도록 하겠습니다.  

# out 패키지
out 패키지 아래에는 persistence 패키지가 있습니다.

# persistence 패키지
application 패키지의 port.out 패키지에 위치한 각각의 유스케이스에 맞게 세분화된 repository의 인터페이스들을 구현한 클래스들이 persistence 패키지 아래에 위치합니다.  

## JpaRecordPersonalRepository 클래스
```java
package injae.AddressBook.personal.adapter.out.persistence;

import injae.AddressBook.personal.application.port.out.RecordPersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;

@Repository
@RequiredArgsConstructor
public class JpaRecordPersonalRepository implements RecordPersonalRepository {

    private final EntityManager em;

    @Override
    public void save(Personal personal) {
        em.persist(personal);
    }

}
```

### 애너테이션 설명
Repository 애너테이션은 스프링 데이터 접근 계층에서 사용하며 이를 통해 기본적으로 Component 애너테이션처럼 스프링의 컴포넌트 스캔 대상이 되어 스프링 빈에 등록이 됩니다.  

롬복의 RequiredArgsConstructor 애너테이션을 통해 필드멤버에 final이 붙거나 @NotNull 이 붙은 필드의 생성자를 자동 생성해줍니다.  

이를 통해 필드 멤버인 EntityManager에 자동으로 의존성을 주입해 객체를 생성합니다.  

### RecordPersonalRepository 인터페이스 구현
RecordPersonalRepository의 save 메소드를 오버라이딩하여 메소드 내부에서 매개변수로 입력 받은 Personal 객체를 EntityManager의 객체의 메소드인 persist에 전달합니다.  

이를 통해 Personal 객체의 정보를 매핑하여 데이터베이스의 Personal 테이블에 저장합니다.  

## JpaGetPersonalRepository 클래스
```java
package injae.AddressBook.personal.adapter.out.persistence;

import injae.AddressBook.personal.application.port.out.GetPersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;

@Repository
@RequiredArgsConstructor
public class JpaGetPersonalRepository implements GetPersonalRepository {

    private final EntityManager em;

    @Override
    public Personal findOne(Long id) {
        return em.find(Personal.class, id);
    }

}
```

애너테이션에 대한 설명은 위의 JpaRecordPersonalRepository 클래스에서 설명한 내용과 같습니다.  

### GetPersonalRepository 인터페이스 구현
GetPersonalRepository의 find 메소드를 오버라이딩하여 메소드 내부에서 매개변수로 입력 받은 id와 Personal 클래스 타입을 EntityManager의 객체의 메소드인 find에 전달합니다.  

이를 통해 데이터베이스의 Personal 테이블에서 입력 받은 id에 해당하는 데이터를 구하여 Personal 객체로 매핑하여 반환합니다.  

## JpaGetPersonalsRepository 클래스
```java
package injae.AddressBook.personal.adapter.out.persistence;

import injae.AddressBook.personal.application.port.out.GetPersonalsRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import java.util.List;

@Repository
@RequiredArgsConstructor
public class JpaGetPersonalsRepository implements GetPersonalsRepository {

    private final EntityManager em;

    @Override
    public List<Personal> findAll() {
        return em.createQuery("select p from Personal p", Personal.class)
                .getResultList();
    }

}
```

애너테이션에 대한 설명은 위의 JpaRecordPersonalRepository 클래스에서 설명한 내용과 같습니다.  

### GetPersonalsRepository 인터페이스 구현
GetPersonalsRepository의 findAll 메서드를 오버라이딩하여 메서드 내부에서 EntityManager의 객체의 메소드인 createQuery를 호출하고 매개변수로 JPQL문과 Personal 클래스 타입을 넘겨 준 다음 이 쿼리문의 검색 결과를 List로 바꿔주는 getResultList 메서드를 호출합니다.  

이를 통해 데이터베이스의 Personal 테이블에서 있는 모든 데이터를 구하여 List 매핑하여 반환합니다.  

## JpaFindPersonalRepository 클래스
```java
package injae.AddressBook.personal.adapter.out.persistence;

import injae.AddressBook.personal.application.port.out.FindPersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import java.util.List;

@Repository
@RequiredArgsConstructor
public class JpaFindPersonalRepository implements FindPersonalRepository {

    private final EntityManager em;

    @Override
    public List<Personal> findByName(String name) {
        return em.createQuery(
                        "select p from Personal p where p.name = :name",
                        Personal.class)
                .setParameter("name", name)
                .getResultList();
    }

}
```

애너테이션에 대한 설명은 위의 JpaRecordPersonalRepository 클래스에서 설명한 내용과 같습니다.  

### FindPersonalRepository 인터페이스 구현
FindPersonalRepository의 findByName 메서드를 오버라이딩하여 메서드 내부에서 EntityManager의 객체의 메소드인 createQuery를 호출하고 매개변수로 JPQL문과 Personal 클래스 타입을 넘겨 준 다음 이 쿼리문의 파라미터를 name으로 설정해주는 setParameter를 호출한 다음 이 쿼리문의 검색 결과를 List로 바꿔주는 getResultList 메서드를 호출합니다.  

이를 통해 데이터베이스의 Personal 테이블에서 있는 데이터 중에 입력 받은 name에 해당하는 데이터를 구하여 List 매핑하여 반환합니다.(해당 name을 가진 모든 데이터를 반환함.)  

## JpaCorrectPersonalRepository 클래스
```java
package injae.AddressBook.personal.adapter.out.persistence;

import injae.AddressBook.personal.application.port.out.CorrectPersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;

@Repository
@RequiredArgsConstructor
public class JpaCorrectPersonalRepository implements CorrectPersonalRepository {

    private final EntityManager em;

    @Override
    public void update(Personal personalParam) {
        Personal findPersonal = em.find(Personal.class, personalParam.getId());

        findPersonal.changePersonalInfo(personalParam);
    }
}
```

애너테이션에 대한 설명은 위의 JpaRecordPersonalRepository 클래스에서 설명한 내용과 같습니다.  

### CorrectPersonalRepository 인터페이스 구현
CorrectPersonalRepository의 update 메서드를 오버라이딩하여 메서드 내부에서 EntityManager의 객체의 메소드인 find를 호출하고 매개변수로 Personal 클래스 타입과 매개변수로 입력 받은 Personal 객체의 id를  넘겨 줍니다.  

이를 통해 데이터베이스의 Personal 테이블에서 있는 데이터 중에 입력 받은 id에 해당하는 데이터를 구하여 Personal 객체에 매핑하여 반환합니다.(id는 유일하기 때문에 데이터베이스에서 해당 id를 가진 데이터는 하나 밖에 없음.)  

이 반환 받은 Personal객체의 참조값을 findPersonal에 저장하고, findPersonal에서 <a href="https://injae7034.github.io/java/addressbook_web_project_01/#personal-%ED%81%B4%EB%9E%98%EC%8A%A4" target="_blank">Personal 객체의 메소드인 changePersonalInfo를 호출</a>하고 personalParam을 넘겨주면 여기서 변경된 정보를 findPersonal의 객체에 저장하게 됩니다.  

그러면 끝입니다.  

그러면 dirty-checking이 되어서 JPA에서 변경사항을 감지하고, 변경된 부분에 대해서 자동으로 데이터베이스에 반영하여 바뀐 부분을 데이터베이스에 저장합니다.  

이 때 dirty-checking의 경우 merge와는 다르게 바꾼 부분만 변경하여 저장하고, 바뀌지 않은 부분은 그대로 둡니다.  

(merge의 경우 EntityManager 객체의 save 메소드를 통해 매개변수로 입력 받은 personalParam자체를 넘김으로써 새로 등록하는데 이 때 변경된 부분은 물론이고, 변경되지 않은 부분까지 모조리 다 바뀝니다.)

그래서 merge의 경우 데이터의 많은 이동이 필요하고, merge하기 전까지 객체의 모든 데이터가 있어야 데이터베이스 제대로 저장하기 때문에 정보 노출의 위험 등 여러 부작용이 있을 수 있기 때문에 실무에서는 dirty-checking을 주로 사용한다고 합니다.  

## JpaErasePersonalRepository 클래스
```java
package injae.AddressBook.personal.adapter.out.persistence;

import injae.AddressBook.personal.application.port.out.ErasePersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;

@Repository
@RequiredArgsConstructor
public class JpaErasePersonalRepository implements ErasePersonalRepository {

    private final EntityManager em;

    @Override
    public void delete(Personal personal) {
        em.remove(personal);
    }
}
```

애너테이션에 대한 설명은 위의 JpaRecordPersonalRepository 클래스에서 설명한 내용과 같습니다.  

### ErasePersonalRepository 인터페이스 구현
ErasePersonalRepository의 delete 메서드를 오버라이딩하여 메서드 내부에서 EntityManager의 객체의 메소드인 remove를 호출하고 매개변수로 Personal 클래스의 객체를 넘겨 줍니다.  

이를 통해 데이터베이스의 Personal 테이블에서 있는 데이터 중에 입력 받은 Personal 객체에 해당하는 데이터를 구하여 해당 데이터를 지웁니다.  

# in 패키지
in 패키지 아래에는 web패키지가 있습니다.  

# web 패키지
web 패키지 아래에는 correct, erase, find, record 패키지지와 HomeController 클래스가 있습니다.  

# HomeController 클래스
```java
package injae.AddressBook.personal.adapter.in.web;

import injae.AddressBook.personal.application.port.in.get.GetPersonalsQuery;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequiredArgsConstructor
@Slf4j
public class HomeController {

    private final GetPersonalsQuery useCase;

    @RequestMapping("/")
    public String home(Model model) {
        log.info("home controller");
        model.addAttribute("personals", useCase.getPersonals());
        return "home";
    }

}
```
HomeController클래스는 스프링 MVC 컨트롤러에서 사용하는 Controller 애너테이션을 사용하여 기본적으로 컴포넌트 스캔의 대상이 됩니다.  

Slf4j 애너테이션을 통해 로깅을 할 수 있고, 여기서는 home controller라는 문자를 로깅하고 있습니다.  

RequestMapping 애너테이션을 통해 localHost8080으로 접속하면 home 메소드를 호출하게 됩니다.  

RequestMapping의 경우 경로만 입력하고, 뒤에 http 메소드를 따로 적어주지 않으면 get으로 인식합니다.  

저는 홈 화면에서 주소록에 저장된 모든 개인의 정보를 표 형식으로 출력하도록 설계하였습니다.  

그래서 HomeController는 필드멤버로 GetPersonalsQuery 인터페이스를 멤버로 가집니다.  

스프링에 의해 이 인터페이스를 구현하는 구현체가 자동으로 주입되기 때문에 HomeController는 GetPersonalsQuery의 구현체가 무엇인지 알 필요없이 GetPersonalsQuery의 메소드인 getPersonals를 호출하면 됩니다.  

매개변수로 입력 받은 Model 객체의 메소드인 addAttribute를 통해 웹화면에서 사용할 정보를 전달합니다.  

이 때 웹화면에서 사용할 이름을 personals로 정하고 거기에 getPersonals를 통해 주소록의 모든 개인 정보를 저장합니다.  

그리고 home 문자열을 반환하면 스프링에서 resources 패키지에 있는 templetes의 패키지 아래에 있는 home.html로 연결시켜줍니다.  

저는 이 home.html을 타임리프를 활용해 구현하였습니다.  

## home.html 코드
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header">
    <meta http-equiv="Content-Type" content="text/html"; charset="UTF-8" />
    <title>Title</title>
</head>
<body>
<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader" />
    <div class="jumbotron">
        <p>
            <a class="btn btn-lg btn-secondary" href="/record">기재하기</a>
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            <a class="btn btn-lg btn-dark" href="/find">이름으로 찾기</a>
        </p>
        <br>
    </div>
    <div>
        <table class="table table-striped">
            <thead>
            <tr>
                <th>이름</th>
                <th>주소</th>
                <th>전화번호</th>
                <th>이메일주소</th>
            </tr>
            </thead>
            <tbody>
            <tr th:each="personal : ${personals}">
                <td th:text="${personal.name}"></td>
                <td th:text="${personal.address}"></td>
                <td th:text="${personal.telephoneNumber}"></td>
                <td th:text="${personal.emailAddress}"></td>
                <td>
                    <a href="#" th:href="@{/correct/{id} (id=${personal.id})}"
                       class="btn btn-primary" role="button">수정</a>
                </td>
                <td>
                    <a href="#" th:href="'javascript:erase('+${personal.id}+')'"
                       class="btn btn-danger" role="button">지우기</a>
                </td>
            </tr>
            </tbody>
        </table>
    </div>
    <div th:replace="fragments/footer :: footer" />
</div> <!-- /container -->
</body>
<script>
function erase(id){
    if (confirm("정말 삭제하시겠습니까??") == true){    //확인
        var form = document.createElement("form");
        form.setAttribute("method", "get");
        form.setAttribute("action", "/erase/" + id);
        document.body.appendChild(form);
        form.submit();
    }else{   //취소
        return;
    }
}
</script>
</html>
```
타임리프의 th:each를 통해 반복을 하고, th:text를 통해 개인의 정보를 모두 출력하도록 설정하였습니다.  

기재하기 찾기 버튼을 만들었고, 기재하기 버튼을 누르면 기재하기 화면으로, 찾기 버튼을 누르면 찾기 화면으로 이동하도록 설정하였습니다.  

또한 수정버튼과 지우기 버튼은 각 개인의 출력된 데이터 옆에 각각 위치하도록 설정하였는데 수정버튼을 누르면 수정하기 화면으로 이동하고, 지우기 버튼을 누를 경우 아래 자바스크립의 함수를 호출하여 팝업창이 뜨도록 하였습니다.  

## home.html 화면
![홈화면](../../images/2022-05-17-addressbook_web_project_03/홈화면.JPG)

# record 패키지
record 패키지 아래에는 RecordPersonalForm과 RecordPersonalForm 클래스가 있습니다.  

## RecordPersonalForm 클래스

```java
package injae.AddressBook.personal.adapter.in.web.record;

import lombok.Getter;
import lombok.Setter;

import javax.validation.constraints.Email;
import javax.validation.constraints.NotEmpty;

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

RecordPersonalForm 클래스는 유스케이스의 입력 유효성을 검증하는 모델인 RecordPersonalCommand와는 별개로 웹 어댑터의 입력 모델, 즉, 웹 계층에서 입력 유효성을 검증하는 역할을 합니다.  

클라이언트가 웹 화면, 뷰에서 기재할 개인 정보(이름, 주소, 전화번호, 이메일)를 입력하면 RecordPersonalForm 클래스는 입력된 정보에 대한 유효성을 검증하는데 이 떄 이름, 주소, 전화번호는 비어있을 경우 그리고 이메일의 경우 비어있는 것은 상관없지만 이메일의 형식을 지키지 않을 경우 예외 메세지를 출력하여 개인 정보를 저장할 수 없도록 막았습니다.  

롬복의 Getter와 Setter를 붙인 이유는 웹계층에서 클라이언트가 입력한 데이터를 RecordPersonalForm 객체에 저장(set)하거나 RecordPersonalForm 객체가 가지고 있는 정보를 구해서(get) 웹 계층에 출력할 때를 위해서 Getter와 Setter를 정의해야 하기 때문입니다.  

이것이 웹 계층에서 입력 모델에 대한 유효성을 검증하는 방법이고, 자연스럽게 이 과정에서 웹 계층에서 클라이언트가 입력한 정보를 유스케이스로 전달하는, 즉, 데이터 전달 역할도 합니다.  

## RecordPersonalController 클래스
```java
package injae.AddressBook.personal.adapter.in.web.record;

import injae.AddressBook.personal.application.port.in.record.RecordPersonalCommand;
import injae.AddressBook.personal.application.port.in.record.RecordPersonalUseCase;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;

import javax.validation.Valid;

@Controller
@RequiredArgsConstructor
public class RecordPersonalController {

    private final RecordPersonalUseCase useCase;

    @GetMapping("/record")
    public String createForm(Model model) {
        model.addAttribute("recordPersonalForm", new RecordPersonalForm());
        return "recordPersonalForm";
    }

    @PostMapping("/record")
    public String recordPersonal(@Valid RecordPersonalForm form, BindingResult result) {

        if (result.hasErrors()) {
            return "recordPersonalForm";
        }

        RecordPersonalCommand command = new RecordPersonalCommand(
                form.getName(),
                form.getAddress(),
                form.getTelephoneNumber(),
                form.getEmailAddress()
        );

        useCase.recordPersonal(command);

        return "redirect:/";
    }
}
```

### createForm 메서드
홈화면(home.html)에서 기재하기 버튼을 클릭했을 떄 경로를 /record로 이동하도록 하였고, http메서드 지정은 없었기 때문에 Get으로 인식됩니다.  

그러면 GetMapping 애너테이션으로 인해 createForm 메서드가 실행됩니다.  

메서드 내부에서 Model 객체의 addAttribute를 호출하여 웹 계층 데이터 전달 및 유효성 검증을 하는 RecordPersonalForm의 객체를 새로 생성하여 recordPersonalForm으로 매핑하여 웹 화면으로 보냅니다.  

그리고 문자열 recordPersonalForm을 반환하는데 그러면 스프링에서 resources 패키지에 있는 templetes의 패키지 아래에 있는 recordPersonalForm.html로 연결시켜줍니다.  

### recordPersonal 메서드
recordPersonalForm.html 화면에서 기재하기 버튼을 클릭했을 때 경로는 그대로이고, http 메서드의 post가 호출되기 때문에 PostMapping이 붙은 recordPersonal로 연결됩니다.  

BindingResult의 객체를 통해 에러가 발생하면 개인 정보를 저장하지 않고, 다시 recordPersonalForm.html 화면으로 돌아가도록 하였습니다.  

예외가 없다면 recordPersonalForm.html에서 사용자가 입력한 정보를 저장하고 있는 RecordPersonalForm의 객체를 이용하여 RecordPersonalCommand의 생성자를 호출합니다.  

그리고 이를 UseCase의 recordPersonal 메서드에 전달하면 서비스 계층을 거쳐서 영속성 계층까지 가게 되고 개인 정보가 성공적으로 데이터베이스에 저장되게 됩니다.  

이 후에 문자열 redirect:/를 반환하여 홈화면으로 다시 돌아가도록 합니다.  

이제 홈화면에는 새로 추가된 개인의 정보가 반영되어 출력됩니다.  

## recordPersonalForm.html
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header" />
<style>
 .fieldError {
 border-color: #bd2130;
 }
</style>
<body>
<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    <form role="form" action="/record" th:object="${recordPersonalForm}"
          method="post">
        <div class="form-group">
            <label th:for="name">이름</label>
            <input type="text" th:field="*{name}" class="form-control"
                   placeholder="이름을 입력하세요"
                   th:class="${#fields.hasErrors('name')}? 'form-control
fieldError' : 'form-control'">
            <p th:if="${#fields.hasErrors('name')}"
               th:errors="*{name}">Incorrect date</p>
        </div>
        <div class="form-group">
            <label th:for="address">주소</label>
            <input type="text" th:field="*{address}" class="form-control"
                   placeholder="주소를 입력하세요"
                   th:class="${#fields.hasErrors('address')}? 'form-control
fieldError' : 'form-control'">
            <p th:if="${#fields.hasErrors('address')}"
               th:errors="*{address}">Incorrect date</p>
        </div>
        <div class="form-group">
            <label th:for="telephoneNumber">전화번호</label>
            <input type="text" th:field="*{telephoneNumber}" class="form-control"
                   placeholder="전화번호를 입력하세요"
                   th:class="${#fields.hasErrors('telephoneNumber')}? 'form-control
fieldError' : 'form-control'">
            <p th:if="${#fields.hasErrors('telephoneNumber')}"
               th:errors="*{telephoneNumber}">Incorrect date</p>
        </div>
        <div class="form-group">
            <label th:for="emailAddress">이메일주소</label>
            <input type="text" th:field="*{emailAddress}" class="form-control"
                   placeholder="이메일주소를 입력하세요"
                   th:class="${#fields.hasErrors('emailAddress')}? 'form-control
fieldError' : 'form-control'">
            <p th:if="${#fields.hasErrors('emailAddress')}"
               th:errors="*{emailAddress}">Incorrect date</p>
        </div>
        <br>
        <button type="submit" class="btn btn-primary">기재하기</button>
        &nbsp;&nbsp;
        <a href="/"class="btn btn-info">돌아가기</a>
    </form>
    <br/>
    <div th:replace="fragments/footer :: footer" />
</div> <!-- /container -->
</body>
</html>
```

th:object에 아까 createForm에서 전달한 recordPersonalForm을 저장합니다.  
각 input의 th:field에는 recordPersonalForm의 멤버인 name, address, telephoneNumber, emailAddress를 대입하여 클라이언트가 웹 화면에서 입력하는 정보를 recordPersonalForm의 각 필드에 저장할 수 있도록 합니다.  

또한 RecordPersonalForm 클래스에서 입력 유효성 검증을 하기 위한 예외 메세지 처리를 출력할 수 있도록 th:if와 th:errors를 이용합니다.  

사용자가 기재하기 버튼을 클릭하면 입력 유효성 검증을 통과하였다면 form에서 post 메소드를 설정하였기 때문에 경로는 그대로이고 http 메서드가 post인 곳으로 이동합니다.  

이 때 recordPersonalForm도 함께 넘어가는데 처음 createForm에서 넘어온 recordPersonalForm에는 아무 정보도 없었지만 기재하기 버튼을 누를 때는 recordPersonalForm에는 사용자가 입력한 정보를 담고 있고, recordPersonalForm이 PostMapping이 붙어 있는 recordPersonal 메서드에 함께 전달됩니다.  

즉, 여기서 기재하기 버튼을 누르면 RecordPersonalController에서 PostMapping이 붙어 있는 recordPersonal 메서드로 연결되게 됩니다.  

돌아가기 버튼을 누르면 홈화면으로 되돌아갑니다.  

## recordPersonalForm.html 기본 화면
![기재하기화면](../../images/2022-05-17-addressbook_web_project_03/기재하기_기본_화면.JPG)

## recordPersonalForm.html 공백 예외 화면
![기재하기공백예외화면](../../images/2022-05-17-addressbook_web_project_03/기재하기_예외_화면_1.JPG)

## recordPersonalForm.html 이메일 형식 예외 화면
![기재하기이메일형식예외화면](../../images/2022-05-17-addressbook_web_project_03/기재하기_예외_화면_2.JPG)

# find 패키지
find 패키지에는 FindPersonalForm과 FindPersonalController 클래스가 있습니다.  

## FindPersonalForm 클래스
```java
package injae.AddressBook.personal.adapter.in.web.find;

import lombok.Getter;
import lombok.Setter;

import javax.validation.constraints.NotEmpty;

@Getter @Setter
public class FindPersonalForm {

    @NotEmpty(message = "이름은 필수입니다.")
    private String name;

}
```

RecordPersonalForm 클래스와 설명이 동일하기 때문에 RecordPersonalForm 클래스 설명을 참고하면 됩니다.  

## FindPersonalController 클래스
```java
package injae.AddressBook.personal.adapter.in.web.find;

import injae.AddressBook.personal.application.port.in.find.FindPersonalCommand;
import injae.AddressBook.personal.application.port.in.find.FindPersonalUseCase;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import javax.validation.Valid;
import java.util.List;

@Controller
@RequiredArgsConstructor
public class FindPersonalController {

    private final FindPersonalUseCase useCase;

    private List<Personal> personals;

    @GetMapping("/find")
    public String createForm(Model model) {
        model.addAttribute("findPersonalForm", new FindPersonalForm());

        if(personals == null) {
            model.addAttribute("personals", null);
        } else {
            model.addAttribute("personals", personals);
        }

        personals = null;

        return "findPersonalForm";
    }

    @PostMapping("/find")
    public String findPersonal(@Valid FindPersonalForm form,
                               BindingResult result) {

        if (result.hasErrors()) {
            return "findPersonalForm";
        }

        personals = useCase.findPersonalByName(
                new FindPersonalCommand(form.getName())
        );

        return "redirect:/find";
    }

}
```
FindPersonalUseCase의 객체와 이름으로 찾은(동명이인이 있을 수 있기 때문에 이름으로 찾으면 개인이 여러명이 있을 수도 있음)개인을 저장할 List\<Personal\>를 멤버로 저장합니다.  

### createForm 메서드
홈화면(home.html)에서 이름으로 찾기 버튼을 클릭했을 떄 경로를 /find로 이동하도록 하였고, http메서드 지정은 없었기 때문에 Get으로 인식됩니다.  

그러면 GetMapping 애너테이션으로 인해 createForm 메서드가 실행됩니다.  

메서드 내부에서 Model 객체의 addAttribute를 호출하여 웹 계층 데이터 전달 및 유효성 검증을 하는 FindPersonalForm의 객체를 새로 생성하여 findPersonalForm으로 매핑하여 웹 화면으로 보냅니다.  

이 때 당연히 FindPersonalController 멤버인 List\<Personal\>은 null이기 때문에 웹화면에서 사용할 personals에 null을 저장하여 보내줍니다.  

나중에 이름으로 찾은 개인들이 있으면 List\<Personal\>은 더이상 null이 아니기 때문에 웹화면에서 사용할 personals에 List\<Personal\>객체 정보를 넘겨주면 찾기 화면에서 이 정보를 바탕으로 하여 이름으로 찾은 개인들의 정보를 찾기 화면에 출력할 것입니다.  

그리고 List\<Personal\>의 정보를 다시 null로 초기화해줍니다.  

(그렇게 하지 않으면 다른 화면으로 갔다가 다시 찾기 화면으로 왔을 때 아까 찾은 이름의 목록이 그대로 다시 출력되기 때문입니다.  

웹 상에서 해결할 수 있는 방법이 분명히 있을 거 같은데 이 것을 해결하지 못해서 부득이하게 FindPersonalController의 멤버에 List\<Personal\>를 두고 이를 활용하여 웹화면에 데이터를 출력하도록 하였습니다.)  

그리고 문자열 findPersonalForm을 반환하는데 그러면 스프링에서 resources 패키지에 있는 templetes의 패키지 아래에 있는 findPersonalForm.html로 연결시켜줍니다.  

### findPersonal 메서드
findPersonalForm.html 화면에서 찾기 버튼을 클릭했을 때 경로는 그대로이고, http 메서드의 post가 호출되기 때문에 PostMapping이 붙은 findPersonal로 연결됩니다.  

BindingResult의 객체를 통해 에러가 발생하면 이름으로 개인 정보를 찾지 않고, 다시 findPersonalForm.html 화면으로 돌아가도록 하였습니다.  

예외가 없다면 findPersonalForm.html에서 사용자가 입력한 이름을 저장하고 있는 FindPersonalForm의 객체를 이용하여 FindPersonalCommand의 생성자를 호출합니다.  

그리고 이를 UseCase의 findPersonal 메서드에 전달하면 서비스 계층을 거쳐서 영속성 계층까지 가게 되고 이름이 일치하는 개인들의 정보를 성공적으로 데이터베이스에서 가져와서 FindPersonalController의 멤버인 List\<Personal\>에 저장하게 됩니다.  

이 후에 문자열 redirect:/find를 반환하여 찾기화면으로 다시 돌아가도록 합니다.  

홈화면에서 이름으로 찾기 버튼을 클릭했을 때는 즉, 처음 찾기 화면이 시작될 때는 List\<Personal\>가 null이었기 때문에 어떠한 개인의 정보도 출력되지 않았습니다.  

찾기 버튼을 클릭한 후에는(물론 일치하는 이름이 없다면  List\<Personal\>가 null이기 때문에 그대로 아무것도 출력되지 않음) 이름으로 찾은 개인의 정보들이 찾기 화면에 출력됩니다.  

## findPersonalForm.html
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header"/>
<body>
<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    <div>
        <div>
            <form role="form" action="/find"
                  th:object="${findPersonalForm}" method="post">
                <div class="form-group">
                    <label th:for="name">이름</label>
                    <input type="text" th:field="*{name}" class="form-control"
                           placeholder="이름을 입력하세요"
                           th:class="${#fields.hasErrors('name')}? 'form-control
fieldError' : 'form-control'">
                    <p th:if="${#fields.hasErrors('name')}"
                       th:errors="*{name}">Incorrect date</p>
                </div>
                <br>
                <button type="submit" class="btn btn-primary">찾기</button>
                &nbsp;&nbsp;
                <a href="/"class="btn btn-info">돌아가기</a>
                <br>
            </form>
        </div>
        <table class="table table-striped">
            <thead>
            <tr>
<!--                <th>id</th>-->
                <th>이름</th>
                <th>주소</th>
                <th>전화번호</th>
                <th>이메일주소</th>
            </tr>
            </thead>
            <tbody th:if="${personals != null}">
            <tr th:each="personal : ${personals}">
<!--                <td th:text="${personal.id}"></td>-->
                <td th:text="${personal.name}"></td>
                <td th:text="${personal.address}"></td>
                <td th:text="${personal.telephoneNumber}"></td>
                <td th:text="${personal.emailAddress}"></td>
                <td>
                    <a href="#" th:href="@{/correct/{id} (id=${personal.id})}"
                       class="btn btn-primary" role="button">수정</a>
                </td>
                <td>
                    <a href="#" th:href="'javascript:erase('+${personal.id}+')'"
                       class="btn btn-danger" role="button">지우기</a>
                </td>
            </tr>
            </tbody>
        </table>
    </div>
    <div th:replace="fragments/footer :: footer"/>
</div> <!-- /container -->
</body>
<script>
function erase(id){
    if (confirm("정말 삭제하시겠습니까??") == true){    //확인
        var form = document.createElement("form");
        form.setAttribute("method", "get");
        form.setAttribute("action", "/erase/" + id);
        document.body.appendChild(form);
        form.submit();
    }else{   //취소
        return;
    }
}
</script>
</html>
```

th:object에 createForm에서 전달받은 findPersonalForm을 저장하고, input 창을 통해 name을 입렫 받아 findPersonalForm의 멤버인 name에 저장합니다.  

사용자가 찾기 버튼을 클릭했을 때 findPersonalForm은 이 name 정보를 가지고 findPersonal로 전달됩니다.  

찾기 버튼을 클릭하면 post 메서드가 호출되기 때문에 PostMapping이 붙은 findPersonal로 이동합니다.  

th:if 에서 personals가 null인 경우 출력을 하지 않도록 하고 있고 personals가 null이 아니면 여기에 개인들의 정보가 저장되어 있기 때문에 이를 출력하도록 하였습니다.  

그리고 찾기 화면에서도 홈화면과 마찬가지로 찾은 개인들의 정보를 수정하거나 지울 수 있도록 하였습니다.  

## findPersonalForm.html 기본 화면
![찾기화면](../../images/2022-05-17-addressbook_web_project_03/찾기_기본_화면.JPG)

## findPersonalForm.html 공백 예외 화면
![찾기공백예외화면](../../images/2022-05-17-addressbook_web_project_03/찾기_예외_화면.JPG)

## findPersonalForm.html 이름으로 찾은 화면
![찾은화면](../../images/2022-05-17-addressbook_web_project_03/찾은_화면.JPG)

# correct 패키지
correct 패키지에는 CorrectPersonalForm, CorrectPersonalController 클래스가 있습니다.  

## CorrectPersonalForm 클래스
```java
package injae.AddressBook.personal.adapter.in.web.correct;

import lombok.Getter;
import lombok.Setter;

import javax.validation.constraints.Email;
import javax.validation.constraints.NotEmpty;

@Getter @Setter
public class CorrectPersonalForm {

    private Long id;
    private String name;

    @NotEmpty(message = "주소는 필수입니다.")
    private String address;
    @NotEmpty(message = "전화번호는 필수입니다.")
    private String telephoneNumber;
    @Email(message = "이메일 형식을 지켜주세요.")
    private String emailAddress;

    public CorrectPersonalForm(Long id, String name, String address,
                               String telephoneNumber, String emailAddress) {
        this.id = id;
        this.name = name;
        this.address = address;
        this.telephoneNumber = telephoneNumber;
        this.emailAddress = emailAddress;
    }

}
```

CorrectPersonalForm에는 다른 웹계층 입력 모델과는 다르게 Personal처럼 5가지 필드(id, name, address, telephoneNumber, emailAddress)를 가지고 있고 5가지 필드멤버를 초기화하는 생성자도 가지고 있습니다.  

그 이유는 CorrectPersonalForm의 객체에 저장된 정보를 바탕으로 Personal 객체를 생성하기 때문입니다.  

그리고 이 Personal 객체의 정보를 나중에 데이터베이스에서 데이터 수정에 사용합니다.  

## CorrectPersonalController 클래스
```java
package injae.AddressBook.personal.adapter.in.web.correct;

import injae.AddressBook.personal.application.port.in.correct.CorrectPersonalCommand;
import injae.AddressBook.personal.application.port.in.correct.CorrectPersonalUseCase;
import injae.AddressBook.personal.application.port.in.get.GetPersonalCommand;
import injae.AddressBook.personal.application.port.in.get.GetPersonalQuery;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;

import javax.validation.Valid;

@Controller
@RequiredArgsConstructor
public class CorrectPersonalController {

    private final GetPersonalQuery query;
    private final CorrectPersonalUseCase useCase;

    @GetMapping("/correct/{id}")
    public String createForm(@PathVariable("id") Long id, Model model) {
        Personal personal = query.getPersonal(new GetPersonalCommand(id));

        model.addAttribute("correctPersonalForm",
                new CorrectPersonalForm(
                        personal.getId(),
                        personal.getName(),
                        personal.getAddress(),
                        personal.getTelephoneNumber(),
                        personal.getEmailAddress()));

        return "correctPersonalForm";
    }

    @PostMapping("/correct/{id}")
    public String correctPersonal(@Valid CorrectPersonalForm correctPersonalForm,
                                  BindingResult result) {

        if (result.hasErrors()) {
            return "correctPersonalForm";
        }

        CorrectPersonalCommand command = new CorrectPersonalCommand(
                correctPersonalForm.getId(),
                correctPersonalForm.getName(),
                correctPersonalForm.getAddress(),
                correctPersonalForm.getTelephoneNumber(),
                correctPersonalForm.getEmailAddress());

        useCase.correctPersonal(command);

        return "redirect:/";
    }
}
```

CorrectPersonalController 클래스는 필드멤버로 GetPersonalQuery와 CorrectPersonalUseCase를 가지는데 GetPersonalQuery는 수정할 개인 정보를 구하기 위해 사용되고, CorrectPersonalUseCase는 찾은 개인 정보를 데이터베이스에서 수정하기위해 사용됩니다.  

### createForm 메서드
홈화면(home.html)또는 찾기화면(findPersonalForm.html)에서 개인 정보 옆에 있는 수정 버튼을 클릭했을 떄 경로를 /correct/{id}로 이동하도록 하였고, http메서드 지정은 없었기 때문에 Get으로 인식됩니다.  

그러면 GetMapping 애너테이션으로 인해 createForm 메서드가 실행됩니다.  

이 때 경로인 correct/{id}에 개인의 id 정보가 같이 전달됩니다.  

이 id 정보를 바탕으로 메서드 내부에서 GetPersonalQuery의 query 메서드에 전달하여 id에 해당하는 개인의 정보를 얻어 Personal 객체에 저장합니다.  

이 Personal 객체를 모든 정보(모든 필드)를 활용하여 CorrectPersonalForm 객체를 생성합니다.  

CorrectPersonalForm 객체는 id를 바탕으로 데이터베이스에서 찾은 개인의 모든 정보를 담고 있습니다.  

CorrectPersonalForm 객체의 정보를 문자열 correctPersonalForm에 매핑하여 Model으 addAttribute를 통해 웹화면으로 개인의 데이터를 전달합니다.  

(수정하기 화면이 페이지에 뜰 때, 이 전달받은 개인 정보를 화면에 출력해줍니다.  

사용자는 이 개인 정보에서 아이디와 이름은 수정할 수 없고, 주소, 전화번호, 이메일주소만 변경할 수 있습니다.)  

그리고 문자열 correctPersonalForm 반환하는데 그러면 스프링에서 resources 패키지에 있는 templetes의 패키지 아래에 있는 correctPersonalForm.html로 연결시켜줍니다.  

### correctPersonal 메서드
correctPersonalForm.html 화면에서 수정하기 버튼을 클릭했을 때 경로는 그대로이고, http 메서드의 post가 호출되기 때문에 PostMapping이 붙은 correctPersonal 연결됩니다.  

BindingResult의 객체를 통해 에러가 발생하면 개인 정보를 저장하지 않고, 다시 correctPersonalForm.html 화면으로 돌아가도록 하였습니다.  

예외가 없다면 correctPersonalForm.html에서 사용자가 입력한 변경된 정보(주소, 전화번호, 이메일주소)를 저장하고 있는 CorrectPersonalForm 객체를 이용하여 CorrectPersonalCommand의 생성자를 호출합니다.  

그리고 이를 UseCase의 correctPersonal 메서드에 전달하면 서비스 계층을 거쳐서 영속성 계층까지 가게 되고 개인 정보가 성공적으로 데이터베이스에 변경되게 됩니다.  

이 후에 문자열 redirect:/를 반환하여 홈화면으로 다시 돌아가도록 합니다.  

이제 홈화면으로 돌아가면 id에 해당하는 개인의 정보가 바뀌어서 출력되어 있을 것입니다.  

## correctPersonalForm.html
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header" />
<body>
<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    <form role="form" th:object="${correctPersonalForm}"
          method="post">
        <!-- id -->
        <input type="hidden" th:field="*{id}" />
        <div class="form-group">
            <h4 th:for="name">이름: <span th:text="*{name}">message</span> </h4>
            <input type="hidden" th:field="*{name}" />
        </div>
        <div class="form-group">
            <label th:for="address">주소</label>
            <input type="text" th:field="*{address}" class="form-control"
                   placeholder="주소를 입력하세요"
                   th:class="${#fields.hasErrors('address')}? 'form-control
fieldError' : 'form-control'">
            <p th:if="${#fields.hasErrors('address')}"
               th:errors="*{address}">Incorrect date</p>
        </div>
        <div class="form-group">
            <label th:for="telephoneNumber">전화번호</label>
            <input type="text" th:field="*{telephoneNumber}" class="form-control"
                   placeholder="전화번호를 입력하세요"
                   th:class="${#fields.hasErrors('telephoneNumber')}? 'form-control
fieldError' : 'form-control'">
            <p th:if="${#fields.hasErrors('telephoneNumber')}"
               th:errors="*{telephoneNumber}">Incorrect date</p>
        </div>
        <div class="form-group">
            <label th:for="emailAddress">이메일주소</label>
            <input type="text" th:field="*{emailAddress}" class="form-control"
                   placeholder="이메일을 입력하세요"
                   th:class="${#fields.hasErrors('emailAddress')}? 'form-control
fieldError' : 'form-control'">
            <p th:if="${#fields.hasErrors('emailAddress')}"
               th:errors="*{emailAddress}">Incorrect date</p>
        </div>
        <br>
        <button type="submit" class="btn btn-primary">수정하기</button>
        &nbsp;&nbsp;
        <a href="/"class="btn btn-info">돌아가기</a>
    </form>
    <div th:replace="fragments/footer :: footer" />
</div> <!-- /container -->
</body>
</html>
```
createForm에서 전달받은 correctPersonalForm을 th:object에 저장하는데, 이 때 correctPersonalForm은 id를 통해 찾은 개인의 정보를 가지고 있습니다.  

input에 hidden을 통해 id와 name의 정보를 가지고 있되 웹화면에는 보이지 않게 합니다.  

(이 과정을 거치지 않으면 나중에 수정하기 버튼을 클릭했을 때 correctPersonalForm이 correctPersonal 메서드로 전달될 때 id와 name의 값은 null이 되기 때문에 이를 막기 위해서 필요합니다.)

주소, 전화번호, 이메일 주소의 input창에 correctPersonalForm의 정보를 바탕으로 개인 정보를 출력합니다.  

이로써 사용자는 주소, 전화번호, 이메일 주소 중에 자신이 원하는 정보만 취사하여 바꾸면 됩니다.  

correctPersonalForm에는 id와 name은 그대로이고, 사용자의 선택에 따라 address, telephoneNumber, emailAddress가 변경되어 저장됩니다.  

그리고 사용자가 수정하기 버튼을 클릭하면 correctPersonalForm에는 변경된 정보와 함께 correctPersonal 메서드로 전달됩니다.  


## correctPersonalForm.html 기본 화면
![수정하기화면](../../images/2022-05-17-addressbook_web_project_03/수정하기_기본_화면.JPG)

## correctPersonalForm.html 공백 예외 화면
![수정하기공백예외화면](../../images/2022-05-17-addressbook_web_project_03/수정하기_예외_화면_1.JPG)

## correctPersonalForm.html 이메일 형식 예외 화면
![수정하기이메일형식예외화면](../../images/2022-05-17-addressbook_web_project_03/수정하기_예외_화면_2.JPG)

# erase 패키지
erase 패키지에는 ErasePersonalController 클래스가 하나 있습니다.  

## ErasePersonalController 클래스
```java
package injae.AddressBook.personal.adapter.in.web.erase;

import injae.AddressBook.personal.application.port.in.erase.ErasePersonalUseCase;
import injae.AddressBook.personal.application.port.in.get.GetPersonalCommand;
import injae.AddressBook.personal.application.port.in.get.GetPersonalQuery;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@Controller
@RequiredArgsConstructor
public class ErasePersonalController {

    private final GetPersonalQuery query;
    private final ErasePersonalUseCase useCase;

    @GetMapping("/erase/{id}")
    public String erasePersonal(@PathVariable("id") Long id) {
        Personal personal = query.getPersonal(new GetPersonalCommand(id));

        useCase.erasePersonal(personal);

        return "redirect:/";
    }

}
```

ErasePersonalController 클래스는 필드멤버로 GetPersonalQuery와 ErasePersonalUseCase를 가지는데 GetPersonalQuery는 지울 개인 정보를 구하기 위해 사용되고, ErasePersonalUseCase는 찾은 개인 정보를 데이터베이스에서 지우기 위해 사용됩니다.  

ErasePersonalController 클래스는 지우기만 하면 되기 때문에 별도위 지우기 화면은 없습니다.  

대신 지우기 버튼을 클릭하면 팝업창을 띄어서 지우기 전에 다시 한번 정말 개인 정보를 지울 것인지 확인합니다.  

그래서 createForm 메서드는 별도로 없고, erasePersonal 메서드만 존재합니다.  

### 홈화면에서 지우기 버튼을 클릭했을 때 뜨는 팝업창

![지우기팝업창1](../../images/2022-05-17-addressbook_web_project_03/지우기_팝업창_1.JPG)

### 찾기화면에서 지우기 버튼을 클릭했을 때 뜨는 팝업창

![지우기팝업창2](../../images/2022-05-17-addressbook_web_project_03/지우기_팝업창_2.JPG)

### 자바스크립트 erase 메서드
```javascript
function erase(id){
    if (confirm("정말 삭제하시겠습니까??") == true){    //확인
        var form = document.createElement("form");
        form.setAttribute("method", "get");
        form.setAttribute("action", "/erase/" + id);
        document.body.appendChild(form);
        form.submit();
    }else{   //취소
        return;
    }
```

### erasePersonal 메서드
home.html 또는 findPersonalForm.html 화면에서 지우기 버튼을 클릭했을 때 팝업창이 뜨도록 설정했습니다.  

이제 이 팝업창("정말 지우시겠습니까?")에서 취소버튼을 누르면 다시 home.html 또는 findPersonalForm.html 화면으로 돌아가고, 확인버튼을 누르면 자바스크립트 함수를 호출하는데 자바스크립트 함수에서 경로를 "/erase/{id}"로 http메서드는 get으로 하도록 설정합니다.  

그러면 erasePersonal 메서드가 호출되는데 이 때 경로를 통해 지울 개인에 해당하는 id를 전달받습니다.  

메서드 내부에서 GetPersonalQuery의 getPersonal메소드에 id를 전달해 지울 개인의 정보를 구해 Personal 객체에 저장합니다.  

이 Personal 객체의 정보를 ErasePersonalUseCase의 erasePersonal메소드에 전달하여 서비스 계층을 거쳐서 영속성 계층까지 가서 해당 개인의 정보를 성공적으로 데이터베이스에서 지우게 됩니다.  

이 후에 문자열 redirect:/를 반환하여 홈화면으로 다시 돌아가도록 합니다.  

이제 홈화면으로 돌아가면 id에 해당하는 개인의 정보가 바뀌 삭제되어 있을 것입니다.  

# 마치며
이로써 헥사고날-아키텍쳐로-구현하는-작은-스프링-부트-토이-프로젝트-주소록을 마치게 되었습니다.  

이 프로젝트를 통해 강의를 보면서 다 이해했다고 생각했던 부분이 사실은 그렇지 않았음을 알게 되어 다시 한번 공부할 수 있었고, 책에서 나오는 내용을 직접 적용해보니 책 내용을 더 잘 이해할 수 있었습니다.  

앞으로도 input만 하지 않고, input을 했으면 꼭 output의 일환으로 작은 토이 프로젝트를 구현해보려고 합니다.  

지금까지 긴 글을 읽어주셔서 감사하며, 틀린 내용이나 질문사항은 언제든지 남겨 주시면 확인 후에 답변드리도록 하겠습니다^^
