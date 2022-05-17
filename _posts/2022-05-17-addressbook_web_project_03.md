---
layout: single
title: "헥사고날-아키텍쳐로-구현하는-작은-스프링-부트-토이-프로젝트-주소록-application-패키지"
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

