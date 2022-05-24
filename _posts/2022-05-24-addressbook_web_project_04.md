---
layout: single
title: "헥사고날-아키텍쳐로-구현하는-작은-스프링-부트-토이-프로젝트-주소록-정리하기-기능추가-체크박스로-정리하기-화면구현"
categories: Java
tag: [Java, 우아한스터디, 만들면서 배우는 클린 아키텍쳐, 스프링 입문, 스프링 핵심 원리 기본편, 스프링 부트와 JPA 활용, 김영한]
toc: true
toc_label: "목차"
toc_sticky: true
---
personal 3가지 패키지(adapter, application, domain) 중에서<a href="https://injae7034.github.io/java/addressbook_web_project_01/" target="_blank">domain 패키지</a>와 <a href="https://injae7034.github.io/java/addressbook_web_project_02/" target="_blank">application 패키지</a>, 그리고 <a href="https://injae7034.github.io/java/addressbook_web_project_03/" target="_blank">adapter 패키지</a>에 대한 설명을 하였습니다.  

여기에 정리하기 기능을 추가하였습니다.  

이전 패키지 내용이 궁굼하신 분들은 위의 링크를 타고 가서 먼저 읽어주시면 됩니다.  

# 있었는데요 없었습니다
말그대로 원래 정리하기 기능은 예전에 데스크톱 애플리케이션인 주소록에는 있었는데 지금 구현한 웹프로젝트에서는 없습니다.  

# 정리하기 기능을 없앤 이유
정리하기 기능을 없앤 결정적인 이유는 제가 데스크톱 애플리케이션인 <a href="https://injae7034.github.io/java/thirteenth/#repalce-%EC%BD%94%EB%93%9C" target="_blank">주소록에서 데이터베이스(MySQL)와 정리하기 기능을 연동하기 위해 삽질을 많이 하고, 결국에 끔찍한(?) 방법으로 문제를 해결</a>하였는데 데이터베이스를 점점 더 공부하다보니 이 끔찍한(?) 방법을 사용하면 안되겠다 싶어서 애초에 정리하기 기능을 없애버렸습니다.  

## 내가 만든 정리하기 기능이 최악인 이유
저는 정리하기 기능을 사용하면 데이터베이스에 저장된 순서도 정렬된 순서로 바뀌어야 한다고 생각했습니다.  

그래서 정리하기 기능을 추가하면 결국에는 데이터베이스에 있는 정보를 다 지운 다음에 정렬된 상태로 바꿔서 다시 데이터베이스에 저장해줘야 한다고 생각했습니다.  

그리고 이 것이 굉장히 끔찍한 생각이라는 것을 나중에 알게 되었습니다.  

만약에 실무에서 정렬을 한답시고 데이터베이스에 있는 엄청나게 많은 정보에 이런 식으로 손대면 끔찍한 재앙이 일어날 것입니다.  

그래서 사람이 아는 만큼 보인다고 예전에 몰랐을 때는 데이터베이스에서 모든 데이터를 다 지우고 다시 정렬해서 저장한다든지 이런 것들에 아무런 꺼리낌이 없었는데 지금 와서 생각 해보니 이것이 얼마나 안좋은 방법인지 알 게 되었습니다.  

그래서 이렇게 하기가 싫어서 그냥 정렬하기 기능을 없애버렸습니다.  

## 다른 개발자분들로부터 깨달음

그런데 이게 굉장히 큰 착각이었습니다.  

제가 구현을 다한 다음 다른 개발자분들에게 시연을 하면서 정렬하기 기능은 이러 이러해서 뺐다고 이야기했습니다.  

그리고 다른 개발자분들이 제 생각이 잘못되었음을 지적해줬습니다.  

데이터베이스는 데이터가 들어온 순서대로 코드를 부여하고, 계속해서 그 순서를 유지하되, 정리하기 기능은 정리해서 보여줄 때 화면에서 정렬해서 보여주면 되지 그 것을 데이터베이스에까지 반영을 해주면 안된다는 것이었습니다.  

즉, **정리하기는 그냥 데이터를 가져오는 쿼리의 일종**(그냥 가져오는 게 아니라 이름을 기준으로 오름차순으로 정렬해서)이라고 생각하면 되는데 전 이 때까지 정리하기를 하면 전체적으로 데이터베이스의 모든 정보를 다 정렬해서 다시 저장해야한다고 생각했습니다.  

# 정리하기 기능을 다시 부활
그래서 정리하기는 쿼리라는 개념으로 다시 정리하기 기능을 구현하기로 했습니다.  

그래서 일단 유스케이스(인커밍 포트 인터페이스)를 생성했습니다.  

## ArrangePersonalQuery 인터페이스
```java
package injae.AddressBook.personal.application.port.in.arrange;

import injae.AddressBook.personal.domain.Personal;

import java.util.List;

public interface ArrangePersonalQuery {

    List<Personal> arrangePersonal();

}

```

코드를 보시면 아시다시피 정리하기는 정리된 List\<Personal\>을 정리해서 반환하는 역할을 하는 쿼리입니다.  

즉, 데이터베이스에 저장된 전체 개인(Personal)의 정보를 쿼리하는 것과 똑같고, 다만, 정렬된 방식으로 보여주는 것에만 차이가 있습니다.  

## ArrangePersonalRepository 인터페이스
```java
package injae.AddressBook.personal.application.port.out;

import injae.AddressBook.personal.domain.Personal;

import java.util.List;

public interface ArrangePersonalRepository {

    List<Personal> arrange();

}
```

## ArrangePersonalByNameService 클래스
```java
package injae.AddressBook.personal.application.service;

import injae.AddressBook.personal.application.port.in.arrange.ArrangePersonalQuery;
import injae.AddressBook.personal.application.port.out.ArrangePersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@RequiredArgsConstructor
@Transactional
@Service
public class ArrangePersonalByNameService implements ArrangePersonalQuery {

    private final ArrangePersonalRepository repository;

    @Override
    public List<Personal> arrangePersonal() {
        return repository.arrange();
    }
}

```

ArrangePersonalQuery를 구현한 ArrangePersonalByNameService는 내부에서 멤버인 ArrangePersonalRepository의 구현체의 arrange메서드를 호출하여 정렬된 개인리스트를 반환합니다.  

## JpaArrangePersonalByNameRepository 클래스
```java
package injae.AddressBook.personal.adapter.out.persistence;

import injae.AddressBook.personal.application.port.out.ArrangePersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import java.util.List;

@Repository
@RequiredArgsConstructor
public class JpaArrangePersonalByNameRepository implements ArrangePersonalRepository {

    private final EntityManager em;

    @Override
    public List<Personal> arrange() {
        return em.createQuery("select p from Personal p order by p.name",
                        Personal.class)
                .getResultList();
    }
}
```

ArrangePersonalRepository를 구현한 JpaArrangePersonalByNameRepository는 내부에서 JPQL을 이용해 이름으로 오름차순으로 정렬한 리스트를 구해서 이를 반환해줍니다.  

# 정리하기 화면 구성
이제 정리하기 기능 구현은 끝났습니다.  

다음으로 할 일이 화면 구성인데 사실 여기서 고민을 많이 하고 시간이 오래 걸렸습니다ㅠ  

별도로 정리하기 화면을 구성해야 할 지 아니면 홈화면에서 정리하기 버튼을 클릭하면 정리하기가 된 채로 보여줘야할지 여러모로 많이 고민했습니다.  

고민의 결과 어차피 정리하기는 데이터베이스에는 어떠한 영향도 끼치지 않는 쿼리의 일종이라는 것에 집중하여 따로 화면을 만들지 않기로 하였습니다.  

그리고 홈 화면에서 정리하기 버튼을 클릭하면 정리된 채로 보여주기만 하기로 했습니다.  

근데 여기서 두 가지 고민이 생겼습니다.  

## 정리하기 화면 구성에서 두 가지 고민사항
첫째, 정리하기 버튼을 눌렀을 때 정리한 화면이 유지될지 아니면 1회성으로 끝날지에 대한 고민이 있었습니다.  

이게 중요한 이유는 정리하기 버튼을 눌러서 현재 화면에는 이름으로 오름차순으로 정렬된 상태로 출력되어 있는데 새로운 개인 정보를 기재할 경우 새로 기재된 이름은 어디로 가야 하는지에 대한 문제때문입니다.  

즉, 정리하기가 1회성이면 이름을 기재하는 순간 원래 데이터베이스에 정리된 상태대로 다시 화면에 출력해서 보여주기 때문에 정리하기는 끝이 나고 새로 기재한 개인 정보는 제일 마지막에 출력될 것입니다.  

그게 아니라 따로 정리된 상태를 풀어주지 않는 이상 정리하기 기능을 유지하고 싶다면 새로운 개인 정보를 기재한다고 해도 정리된 상태가 유지되어 새로 기재된 개인 정보는 본인의 이름 오름차순에 맞게 리스트 사이에 끼어서 출력이 될 것입니다.  

저는 후자처럼 따로 사용자가 정리된 상태를 풀어주지 않는다면 정리된 상태가 계속 유지되어 새로 개인정보를 기재하든 지우든 정렬된 상태로 출력되는 것이 더 좋다고 생각했습니다.  

둘째, 사용자가 정리하기 버튼을 클릭했을 때 정리한 상태를 보여주기로 했을 경우에 문제가 있습니다.  

그 문제는 바로 정리하기 버튼만으로는 현재 화면에서 정리하기 상태가 유지되고 있는지 아닌지를 확인할 수 없다는 것이었습니다.(유일한 방법은 지금 출력된 개인들의 리스트 성함을 보면서 이름순으로 정렬되어 있는지 확인하는 것입니다.)  

저는 정리하기를 누르면 그 상태가 유지되는 것을 사용자에게 시각적으로 보여주고 싶었습니다.  

## 체크박스를 이용해 홈화면에서 정리하기 화면 구성
그래서 생각한 것이 바로 체크박스였습니다.  

홈화면에 정리하기 체크박스를 새로 만들어서 체크박스를 클릭하여 체크박스에 체크가 생기면 개인들의 정보가 이름을 기준으로 오름차순으로 출력하고, 체크박스를 다시 클릭하여 체크박스에 체크가 사라지면 개인들의 정보가 다시 원래대로 데이터베이스에 저장된 순서대로 출력하는 방식으로 결정하였습니다.  

## 정리하기 체크박스가 추가된 홈화면
![정리하기기능추가된홈화면](../../images/2022-05-24-addressbook_web_project_04/홈화면_정리하기_기능_추가.JPG)  

### HomeForm 클래스
```java
package injae.AddressBook.personal.adapter.in.web.home;

import injae.AddressBook.personal.domain.Personal;
import lombok.Getter;
import lombok.Setter;

import java.util.List;

@Getter
@Setter
public class HomeForm {

    private boolean isArrangeChecked;
    private List<Personal> personals;

}
```
HomeForm의 멤버는 홈화면에서 지금 정렬하기 체크박스가 체크되었는지 안되었는지 상태를 저장할 boolean 자료형과 홈화면에서 개인들의 데이터를 출력할 때 사용할 List 입니다.  

### HomeController 클래스
```java
ackage injae.AddressBook.personal.adapter.in.web.home;

import injae.AddressBook.personal.application.port.in.arrange.ArrangePersonalQuery;
import injae.AddressBook.personal.application.port.in.get.GetPersonalsQuery;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequiredArgsConstructor
@Slf4j
public class HomeController {

    private final GetPersonalsQuery getQuery;

    private final ArrangePersonalQuery arrangeQuery;

    private final HomeForm homeForm = new HomeForm();

    @RequestMapping("/")
    public String home(Model model) {
        log.info("home controller");

        if(homeForm.isArrangeChecked() == false) {
            homeForm.setPersonals(getQuery.getPersonals());
        } else {
            homeForm.setPersonals(arrangeQuery.arrangePersonal());
        }

        model.addAttribute("personals", homeForm.getPersonals());
        model.addAttribute("isArrangeChecked",
                homeForm.isArrangeChecked());

        return "home";
    }

    @PostMapping("/")
    public String arrangePersonal(Model model) {
        if(homeForm.isArrangeChecked() == false) {
            homeForm.setArrangeChecked(true);
            homeForm.setPersonals(arrangeQuery.arrangePersonal());
        } else {
            homeForm.setArrangeChecked(false);
            homeForm.setPersonals(getQuery.getPersonals());
        }
        model.addAttribute("personals", homeForm.getPersonals());
        model.addAttribute("isArrangeChecked", homeForm.isArrangeChecked());

        return "home";
    }

}
```

### 정리하기 체크박스가 추가된 home.html 코드
```html
<tr>
    <th>이름</th>
    <th>주소</th>
    <th>전화번호</th>
    <th>이메일주소</th>
    <th th:if="${isArrangeChecked == true}">
        <input type="checkbox" onclick="check()"
            checked>정렬하기</th>
    <th th:if="${isArrangeChecked == false}">
        <input type="checkbox" onclick="check()"
            unchecked>정렬하기</th>
</tr>

<script>
function check(){
    var form = document.createElement("form");
    form.setAttribute("method", "post");
    document.body.appendChild(form);
    form.submit();
}
</script>
```
## 홈화면에 정리하기 체크박스 작동 원리
### 1. 처음 홈화면이 켜질 때 작동 원리
HomeController 클래스가 생성될 때 HomeForm 멤버를 필드 초기화시킵니다.  

그렇게 되면 일단 HomeForm의 객체는 boolean은 false와 List는 null로 초기화됩니다.  

처음 홈화면에 들어왔을 때 isArrangeChecked가 false이기 때문에 데이터베이스에 저장된 순서대로 개인들의 정보를 호출하여 homeForm의 List 멤버에 저장합니다.  

그리고 Model 객체의 addAttribute 메서드를 통해 home.html에서 사용할 personals와 isArrangeChecked에 현재 값을 넘겨줍니다.  

그리고 home 문자열을 반환하여 home.html로 이동합니다.  

home.html에서는 넘겨받은 isArrangeChecked의 값이 false이기 떄문에 체크박스가 unchecked된 상태를 유지하도록 합니다.  

### 2. 홈화면에서 정리하기 체크박스를 클릭할 때 작동 원리
정리하기 체크박스를 클릭하면 자바스크립트의 check함수가 실행됩니다.  

check함수에서는 HTTP메서드를 post로 선택한다음 다시 HomeController로 이동시킵니다.  

HomeController에서 주소는 그대로이고 post 메소드와 매핑되는 arrangePersonal가 실행됩니다.  

그럼 현재 isArrangeChecked 상태가 false이기 때문에 isArrangeChecked를 true로 바꿔줍니다.  

그리고 ArrangeQuery의 arrangePersonal를 호출하여 홈화면에 출력될 개인의 정보를 정렬된 개인들의 정보로 바꿔줍니다.  

다음으로 addAttribute를 통해 홈화면에서 사용할 personals에는 정렬된 개인들의 정보를, isArrangeChecked는 true 값으로 설정해줍니다.  

그리고 home 문자열을 통해 home.html로 다시 이동합니다.  

다시 홈화면으로 돌아가면 isArrangeChecked는 true이기 때문에 체크박스가 checked 상태로 만들어줍니다.(이렇게 상태를 checked해주지 않으면 체크박스의 check가 유지되지 않습니다.)  

### 3. 홈화면에서 정리하기 체크박스를 다시 클릭할 때 작동 원리
정리하기 체크박스를 다시 클릭하면 자바스크립트의 check함수가 실행됩니다.  

check함수에서는 HTTP메서드를 post로 선택한다음 다시 HomeController로 이동시킵니다.  

HomeController에서 주소는 그대로이고 post 메서드와 매핑되는 arrangePersonal가 실행됩니다.  

그럼 현재 isArrangeChecked 상태가 true이기 때문에 isArrangeChecked를 false로 바꿔줍니다.  

그리고 getQuery의 getPersonals 호출하여 홈화면에 출력될 개인의 정보를 데이터베이스에 저장된 순서대로 바꿔줍니다.  

다음으로 addAttribute를 통해 홈화면에서 사용할 personals에는 데이터베이스 순서의 개인들 정보를, isArrangeChecked는 false 값으로 설정해줍니다.  

그리고 home 문자열을 통해 home.html로 다시 이동합니다.  

다시 홈화면으로 돌아가면 isArrangeChecked는 false이기 때문에 체크박스가 unChecked 상태로 만들어줍니다.  

## 화면 이동해도 isArrangeChecked와 personals의 값은 변하지 않는다
home.html 화면에 isArrangeChecked와 personals의 값은 체크박스를 체크하지 않는 한 바뀌지 않습니다.(HomeController에서 postMapping이 되어야만 값이 바뀌기 때문에)  

## 정렬한 상태에서 새로운 개인 정보를 기재했을 때
그래서 만약에 현재 홈화면이 정렬된 상태, 즉, 정렬하기 체크박스가 체크되어 있는 상태라면 기재하기 버튼을 클릭했을 때 기재하기 폼 화면으로 이동하고 여기서 기재한 정보를 기재하기 버튼을 클릭하든 아니면 다시 돌아가기버튼을 하여도 마찬가지로 홈화면에는 정렬된 상태가 유지됩니다.  

정렬된 상태에서 기재하기 화면으로 가서 새로운 개인 정보를 입력하고 기재하기 버튼을 클릭하면 데이터베이스에 새로운 정보가 저장되고 다시 이를 정렬된 순서로 구하여 홈화면에 출력하기 때문에, 즉, 현재 체크박스에 정렬하기가 체크된 상태는 그대로이기 때문에 새로운 개인 정보를 기재하면 이름순으로 홈화면에 출력됩니다.  

### 홈화면에서 정리하기 체크박스가 체크된 상태
![정리하기홈화면](../../images/2022-05-24-addressbook_web_project_04/홈화면에서_정리된상태.JPG)  

### 홈화면에서 정리하기 체크박스가 체크된 상태에서 새로운 개인정보 기재하기
![기재하기화면](../../images/2022-05-24-addressbook_web_project_04/정렬하기된_상태에서_새로운_개인정보_기재하기.JPG)  

### 홈화면에서 정리하기 체크박스가 체크된 상태에서 새로운 개인정보 기재한 후에 홈화면
![다시홈화면](../../images/2022-05-24-addressbook_web_project_04/정렬하기된_상태에서_새로운_개인정보_기재한후_홈화면.JPG)  

확인해보시면 나길동이 테이블의 마지막에 추가되는 것이 아니라 이름 기준으로 오름차순으로 정렬된 상태로 추가되는 것을 알 수 있습니다.(물론 실제로 데이터에비스에서는 마지막에 추가되었기 때문에 제일 마지막에 저장되어 있습니다)  

다만 정렬하기 쿼리상 현재 화면에서는 이름을 기준으로 오름차순으로 출력되기 때문에 마지막에 출력되지 않고 개인들의 정보 사이에 출력됩니다.  

## 삭제하기, 수정하기 및 홈으로 돌아가기
삭제하기, 수정하기 및 홈으로 돌아가기 모두에서 기재하가와 마찬가지로 정렬한 상태 또는 정렬하지 않은 상태는 유지됩니다.  

만약 체크박스에 체크가 되어 있다면 게속 해서 정렬된 상태는 유지되고, 체크박스에 체크가 안되어 있다면 계속 해서 정렬이 안된 상태가 유지됩니다.  

즉, 정렬하기 상태를 유지할 지 해지할 지를 바꿀 수 있는 유일한 조건이 바로 정리하기 체크박스를 클릭하는 것입니다.  

# 마치며
이상으로 체크박스를 통해 정리하기 기능이 성공적으로 유지되도록 구현해봤습니다.  

자바스크립트나 html, 타임리프를 잘몰라서 어떻게든 자바 boolean으로 해결했는데 더 좋은 방법이 있으면 알려주시면 감사하겠습니다^^
