---
layout: single
title: "헥사고날-아키텍쳐로-구현하는-작은-스프링-부트-토이-프로젝트-주소록-도메인패키지"
categories: Java
tag: [Java, 우아한스터디, 만들면서 배우는 클린 아키텍쳐, 스프링 입문, 스프링 핵심 원리 기본편, 스프링 부트와 JPA 활용, 김영한]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 토이 프로젝트를 시작 하게 된 이유
스프링에 대한 지식이 너무 없다보니 일단 지식을 쌓자는 생각으로 김영한님의 '스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술'(무료)와 '스프링 핵심 원리 기본편', '실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발'을 완강하였습니다.  

완강하고 복습도 한 번 하고 나니 왠지 다 아는 것 같은 기분이 들었습니다.  

그러나 최근에 읽기 시작한 '어떻게 공부할 것인가?'라는 책을 읽으면서 이 방법에 대한 회의감을 느끼게 되었습니다.  

인출하는 노력없이 계속 주입을 해봤자 단기기억에만 저장되며 장기기억으로 저장되기는 힘들며, 가장 쉽지만 효율이 안좋은 방법이 반복해서 공부한 내용을 읽는 것이라는 내용이 있었습니다.  

그래서 나는 과연 얼마나 공부한 내용을 적용할 수 있을까를 시험해보기 위해 그리고 이 인출과정을 통해 효율적으로 공부하기 위해 기존에 데스크톱 애플리케이션으로 만들어진 주소록 프로젝트를 웹애플리케이션으로 바꿔보기로 마음먹었습니다.  

게다가 지금 우아한 스터디에서 진행하고 있는  만들면서 배우는 클린 아키텍쳐의 내용 역시 적용해보면 책의 내용도 더 잘 이해할 수 있을 것이라는 생각도 들었습니다.  

그래서 스프링 인강을 통해 쌓은 지식의 인출과 책에서 배운 클린 아키텍처를 직접 적용해보기 위해 작은 토이프로젝트를 시작하게 되었습니다.  

# 시작할 때 어려웠던 점
책의 3장에서 나온 아키텍처적으로 표현력 있는 패키지 구조와 책의 예시 코드를 바탕으로 패키지 구성을 하려다 보니 힘들었습니다.  

이때까지 프로젝트를 하면서 패키지 구성에 이렇게 고민을 해 본적이 없었는데, 책의 내용과 예시가 있음에도 불구하고, 이를 처음으로 제 주소록 프로젝트에 적용해보려고 하니 생각보다 힘들었습니다.  

책의 예시와 제가 만드는 프로젝트에서 다른 부분에 대해 약간의 커스터마이징을 하고, 책의 부족한 내용을 찾아서 더 공부하면서 패키지 구성을 하였습니다.  

이 과정이 생각보다 오래 걸렸지만 책의 내용을 그냥 읽을때는 미처 고민하지 못했던 것을 생각할 수 있게 되었고, 책의 내용을 더 잘 이해할 수 있어서 좋았습니다.  

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
만들면서 배우는 클린 아키텍처 책을 읽으면서 이 내용을 적용하여 토이프로젝트를 진행하다 보니 이 간단한 프로젝트에도 수많은 클래스와 패키지가 생겼습니다.  

## 패키지 구성에 대한 간략한 설명
원래는 AddressBook 또는 PersonalService라는 넓은 서비스에서 주소록에 새로운 개인을 기재하거나(record) 주소록에 저장된 한 명의 개인 정보를 구하거나 모든 개인의 정보를 구하거나(get) 주소록에서 이름으로 찾는다거나(findByName) 주소록에서 개인의 정보를 지우는 것 모두 다뤘습니다.  

그러나 책에서는 이렇게 넓은 서비스보다는 서비스의 각 메소드 즉, 각 유스케이스마다 인터페이스를 만들고 이를 구현하는 세분화된 클래스를 생성하는 것을 추천하여 이를 반영하여 주소록에서도 세분화된 유스케이스 인터페이스와 이를 구현한 서비스 클래스를 생성하였습니다.  

여기서 더 나아가 세분화된 서비스에 맞게 repository와 controller 모두 세분화시켰습니다.  

# personal 패키지의 domain 패키지
domain 패키지 아래에는 Personal 클래스 하나가 있습니다.  

## Personal 클래스

책에서는 domain에 엔티티 클래스와 별도로 데이터베이스와 연동되는 영속성 전용 엔티티를 구성할 것을 이야기하고 있지만 그렇다면 도메인의 엔티티와 영속성 엔티티와의 맵핑과정이 필요한데 아직 이에 대한 구현을 위한 이해가 부족하여 저는 엔티티 클래스에서 바로 데이터베이스와 연동되도록 설정하였습니다.  

그래서 어쩔 수 없이 어느정도 도메인 엔티티 영역이 영속성 계층에 영향을 받게 되었습니다.  

```java
package injae.AddressBook.personal.domain;

import lombok.EqualsAndHashCode;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@Getter
@NoArgsConstructor
@EqualsAndHashCode
@ToString(exclude = "id")
public class Personal {

    @Id @GeneratedValue
    @Column(name = "personal_id")
    private Long id;

    private String name;
    private String address;
    private String telephoneNumber;
    private String emailAddress;

    public Personal(Long id, String name, String address,
                    String telephoneNumber, String emailAddress) {
        this.id = id;
        this.name = name;
        this.address = address;
        this.telephoneNumber = telephoneNumber;
        this.emailAddress = emailAddress;
    }

    public Personal(String name, String address,
                    String telephoneNumber, String emailAddress) {
        this(null, name, address, telephoneNumber, emailAddress);
    }

    public void changePersonalInfo(Personal personal) {
        String address = personal.getAddress();
        if (this.address.compareTo(address) != 0) {
            this.address = address;
        }

        String telephoneNumber = personal.getTelephoneNumber();
        if (this.telephoneNumber.compareTo(telephoneNumber) != 0) {
            this.telephoneNumber = telephoneNumber;
        }

        String emailAddress = personal.getEmailAddress();
        if (this.emailAddress.compareTo(emailAddress) != 0) {
            this.emailAddress = emailAddress;
        }
    }

}
```

### 사용한 애너테이션 설명
코드에서 보시다시피 Entity 애너테이션을 명시함으로써 이미 도메인의 엔티티 클래스는 영속성 계층에 영향을 받게 되었고, 그로 인해 롬복의 NoArgsConstructor 애너테이션을 이용해 매개변수가 없는 기본 생성자를 별도로 정의해줘야 합니다.  

롬복의 Getter 애너테이션을 활용해 getter 메소드를 이용할 수 있게 하고, 롬복의 EqualsAndHashCode를 이용해 동등비교, ToString을 활용해 toString메소드를 사용할 수 있도록 하였습니다.  

id 필드는 Column애노테이션을 통해 데이터베이스 테이블에서 personal_id로 명시하고, Id애너테이션을 통해  primary key로 명시합니다.  

GeneratedValue 애너테이션을 통해 primary key를 자동으로 생성해줍니다.  

### 생성자 및 메소드
생성자는 id를 제외하고 나머지 필드를 매개변수로 가지는 생성자와 모든 필드를 매개변수로 가지는 생성자가 있습니다.  

모든 필드를 매개변수로 가지는 생성자와 changePersonalInfo 메소드는 나중에 update를 할 때 사용하기 위해 나중에 만들었습니다.  

개인의 이름은 바꿀 수 없도록 막았고, 그 외의 필드는 모두 변경할 수 있도록 설정하였습니다.  



