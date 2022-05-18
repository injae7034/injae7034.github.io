---
layout: single
title: "헥사고날-아키텍쳐로-구현하는-작은-스프링-부트-토이-프로젝트-주소록-application-패키지"
categories: Java
tag: [Java, 우아한스터디, 만들면서 배우는 클린 아키텍쳐, 스프링 입문, 스프링 핵심 원리 기본편, 스프링 부트와 JPA 활용, 김영한]
toc: true
toc_label: "목차"
toc_sticky: true
---
personal 3가지 패키지(adapter, application, domain) 중에서<a href="https://injae7034.github.io/java/addressbook_web_project_01/" target="_blank">domain 패키지</a>를 먼저 설명하였고, 다음으로는 application 패키지에 대한 설명을 하도록 하겠습니다.  

이 글의 제일 처음은 domain 패키지입니다.  

그래서 먼저 domain 패키지 글을 읽는 것을 추천드립니다.  

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

# application 패키지
application 패키지 아래에는 port와 service 패키지가 있습니다.  

먼저 port 패키지를 살펴보도록 하겠습니다.  

# port 패키지
port 패키지 아래에는 in과 out 패키지가 있습니다.  

# in 패키지
in 패키지 아래에는 correct, erase, find, get, record 패키지가 있습니다.  

## record 패키지
record 패키지 아래에는 RecordPersonalCommand 클래스와 RecordPersonalUseCase 인터페이스가 있습니다.  

### RecordPersonalCommand 클래스
RecordPersonalCommand 클래스는 RecordPersonalUseCase의 입력 모델입니다.  

즉, RecordPersonalUseCase에서 메소드를 호출할 때 매개변수로 활용됩니다.  

그래서 이 클래스는 생성자를 통해 RecordPersonalUseCase의 입력 유효성을 검증합니다.  

```java
package injae.AddressBook.personal.application.port.in.record;

import injae.AddressBook.common.PersonalCommandValidating;
import lombok.EqualsAndHashCode;
import lombok.Value;

import javax.validation.constraints.NotNull;

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

#### 애너테이션 설명
롬복의 Value애너테이션을 사용하는데 이를 통해 이 클래스는 불변하는 클래스임을 명시하고 있습니다.  
그래서 이 클래스의 모든 멤버들은 private final을 붙여 주게 됩니다.  

EqualsAndHashCode를 통해 동등비교를 할 수 있는데 이 때 부모의 멤버는 제와하고 자기 자신만 비교합니다.  

#### PersonalCommandValidating 클래스
RecordPersonalCommand는 PersonalCommandValidating 클래스를 상속받고 있습니다.  

PersonalCommandValidating는 입력 유효성 검증을 통해 직접 구현한 클래스로 common 패키지 아래에 위치합니다.  

```java
package injae.AddressBook.common;

import javax.validation.ConstraintViolation;
import javax.validation.ConstraintViolationException;
import javax.validation.Validation;
import javax.validation.Validator;
import javax.validation.ValidatorFactory;
import java.util.Set;

public abstract class PersonalCommandValidating<T> {

    private Validator validator;

    public PersonalCommandValidating() {
        ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
        validator = factory.getValidator();
    }

    protected void validatePersonalCommand() {
        Set<ConstraintViolation<T>> violations = validator.validate((T) this);
        if (!violations.isEmpty()) {
            throw new ConstraintViolationException(violations);
        }
    }

}
```
PersonalCommandValidating은 Bean Validation API를 이용하여 입력 유효성을 검증합니다.  

일단 멤버로 Validator의 객체를 가지고 이를 통해 입력 유효성을 검증합니다.  

이제 이 PersonalCommandValidating를 상속받은 클래스에 입력 유효성 검증을 하기 위해 필요한 애너테이션을 붙여 주고 RecordPersonalCommand의 생성자에서 PersonalCommandValidating의 validatePersonalCommand 메소드를 호출하여 객체가 생성되기 전에 유효성 검증을 합니다.  

RecordPersonalCommand에서 name, address, telephoneNumber에는 모두 NotNull 애너테이션을 붙였는데 이 애너테이션을 통해 세 필드는 모두 입력값이 null이면 예외가 발생하여 객체를 생성할 수 없도록 막았습니다.  

즉, 주소록에 기재를 할 때, 이메일은 선택이지만 이름, 주소, 전화번호는 필수 입력사항으로 설정하였습니다.  

RecordPersonalCommand가 생성될 때 이렇게 객체에 대한 유효성 검증을 통해 올바른 입력 값일 경우만 객체가 생성될 수 있도록 하였고, 모든 멤버가 final priavate으로 설정되었으며 setter 메소드가 없기 때문에 한 번 생성된 객체를 외부에서 값을 바꿀 수 없습니다.  

즉, 일단 RecordPersonalUseCase의 객체가 생성되면 이 객체의 모든 필드멤버들은 입력 유효성 검증이 된 것으로 보장됩니다.  

### RecordPersonalUseCase 인터페이스
RecordPersonalUseCase는 넓은 서비스에서 record라는 메소드, 즉, 주소록에 새로운 개인을 기재한다는 유스케이스를 세분화시킨 인터페이스입니다.  

```java
package injae.AddressBook.personal.application.port.in.record;

public interface RecordPersonalUseCase {

    Long recordPersonal(RecordPersonalCommand command);

}
```

내부에는 recordPersonal이라는 메소드 하나만 선언하고 있습니다.  

입력 유효성 검증 역할을 하는 RecordPersonalCommand를 매개변수로 받습니다.  

service 클래스에서 이 메소드를 정의하면서 RecordPersonalUseCase 인터페이스를 구현해줍니다.

## get 패키지
get 패키지 아래에는 GetPersonalCommand 클래스와 GetPersonalQuery, GetPersonalsQuery 인터페이스가 위치합니다.  

### GetPersonalCommand 클래스
GetPersonalCommand 클래스는 GetPersonalQuery의 입력 모델입니다.  

즉, GetPersonalQuery에서 메소드를 호출할 때 매개변수로 활용됩니다.  

그래서 이 클래스는 생성자를 통해 GetPersonalQuery의 입력 유효성을 검증합니다.  

```java
package injae.AddressBook.personal.application.port.in.get;

import injae.AddressBook.common.PersonalCommandValidating;
import lombok.EqualsAndHashCode;
import lombok.Value;

import javax.validation.constraints.NotNull;

@Value
@EqualsAndHashCode(callSuper = false)
public class GetPersonalCommand
        extends PersonalCommandValidating<GetPersonalCommand> {

    @NotNull
    private final Long id;

    public GetPersonalCommand(Long id) {
        this.id = id;
        this.validatePersonalCommand();
    }
}
```

롬복 애너테이션과 상속 받은 PersonalCommandValidating에 대한 설명은 아까 위에서 설명한 RecordPersonalCommand와 동일합니다.  

GetPersonalUseCase에서 이 id를 통해 데이터베이스에 저장된 개인의 정보를 가져올 것이기 때문에 GetPersonalCommand는 id가 null인지 아닌지만 체크하면 됩니다.  

### GetPersonalQuery 인터페이스
GetPersonalQuery는 넓은 서비스에서 get(id)라는 메소드, 즉, 주소록에서 해당 id를 가지는 개인의 정보를 가져오는 유스케이스를 세분화시킨 인터페이스입니다.  

여기서 UseCase라고 하지 않고 Query라고 명시한 것은 책에서는 특별한 이유가 있지 않는 한 읽기 전용 유스케이스(Query)와 쓰기가 가능한 유스케이스(Command)를 구분하는 것을 추천하고 있기 때문입니다.  

이런 방식은 CQS(Command-Query Separation)나 CQRS(Command-Query Responsibility Segregation) 같은 개념에 부합합니다.  

그래서 저도 쓰기 기능이 추가되는 유스케이스만 UseCase로 표현하고, 읽기 전용 유스케이스는 Query로 표현하였습니다.  

```java
package injae.AddressBook.personal.application.port.in.get;

import injae.AddressBook.personal.domain.Personal;

public interface GetPersonalQuery {

    Personal getPersonal(GetPersonalCommand command);

}
```

내부에는 getPersonal 메소드 하나만 선언하고 있습니다.  

입력 유효성 검증 역할을 하는 GetPersonalCommand를 매개변수로 받습니다.  

service 클래스에서 이 메소드를 정의하면서 GetPersonalQuery 인터페이스를 구현해줍니다.

### GetPersonalsQuery 인터페이스
GetPersonalsQuery는 주소록에 기재된 모든 개인을 가져오기 때문에 별도의 매개변수가 필요없습니다.  

그래서 입력 유효성을 검증할 필요도 없습니다.  

```java
package injae.AddressBook.personal.application.port.in.get;

import injae.AddressBook.personal.domain.Personal;

import java.util.List;

public interface GetPersonalsQuery {

    List<Personal> getPersonals();

}
```

내부에는 getPersonals 메소드 하나만 선언하고 있습니다.  

service 클래스에서 이 메소드를 정의하면서 GetPersonalsQuery 인터페이스를 구현해줍니다.

## find 패키지
find 패키지 아래에는 FindPersonalCommand 클래스와 FindPersonalUseCase 인터페이스가 위치합니다.  

### FindPersonalCommand 클래스
FindPersonalCommand 클래스는 FindPersonalUseCase의 입력 모델입니다.  

즉, FindPersonalUseCase에서 메소드를 호출할 때 매개변수로 활용됩니다.  

그래서 이 클래스는 생성자를 통해 FindPersonalUseCase의 입력 유효성을 검증합니다.  

```java
package injae.AddressBook.personal.application.port.in.find;

import injae.AddressBook.common.PersonalCommandValidating;
import lombok.EqualsAndHashCode;
import lombok.Value;

import javax.validation.constraints.NotNull;

@Value
@EqualsAndHashCode(callSuper = false)
public class FindPersonalCommand
        extends PersonalCommandValidating<FindPersonalCommand> {

    @NotNull
    private final String name;

    public FindPersonalCommand(String name) {
        this.name = name;
        this.validatePersonalCommand();
    }
}

```

롬복 애너테이션과 상속 받은 PersonalCommandValidating에 대한 설명은 아까 위에서 설명한 RecordPersonalCommand와 동일합니다.  

CorrectPersonalCommand에서 필드멤버가 name만 있는 이유는 FindPersonalUseCase가 이름으로 개인을 찾는 유스케이스이기 때문입니다.  

### FindPersonalUseCase 인터페이스
FindPersonalUseCase는 넓은 서비스에서 findByName이라는 메소드, 즉, 주소록에서 이름으로 개인을 찾는 유스케이스를 세분화시킨 인터페이스입니다.  

```java
package injae.AddressBook.personal.application.port.in.find;

import injae.AddressBook.personal.domain.Personal;

import java.util.List;

public interface FindPersonalUseCase {

    List<Personal> findPersonalByName(FindPersonalCommand command);

}
```

내부에는 findPersonalByName 메소드 하나만 선언하고 있습니다.  

입력 유효성 검증 역할을 하는 FindPersonalCommand를 매개변수로 받습니다.  

service 클래스에서 이 메소드를 정의하면서 FindPersonalUseCase 인터페이스를 구현해줍니다.

## correct 패키지
correct 패키지 아래에는 CorrectPersonalCommand 클래스와 CorrectPersonalUseCase 인터페이스가 위치합니다.  

### CorrectPersonalCommand 클래스
CorrectPersonalCommand 클래스는 CorrectPersonalUseCase의 입력 모델입니다.  

즉, CorrectPersonalUseCase에서 메소드를 호출할 때 매개변수로 활용됩니다.  

그래서 이 클래스는 생성자를 통해 CorrectPersonalUseCase의 입력 유효성을 검증합니다.  

```java
package injae.AddressBook.personal.application.port.in.correct;


import injae.AddressBook.common.PersonalCommandValidating;
import lombok.EqualsAndHashCode;
import lombok.Value;

import javax.validation.constraints.NotNull;

@Value
@EqualsAndHashCode(callSuper = false)
public class CorrectPersonalCommand
        extends PersonalCommandValidating<CorrectPersonalCommand> {

    @NotNull
    private final Long id;
    @NotNull
    private final String name;
    @NotNull
    private final String address;
    @NotNull
    private final String telephoneNumber;

    private final String emailAddress;

    public CorrectPersonalCommand(Long id, String name, String address,
                                  String telephoneNumber, String emailAddress) {
        this.id = id;
        this.name = name;
        this.address = address;
        this.telephoneNumber = telephoneNumber;
        this.emailAddress = emailAddress;
        this.validatePersonalCommand();
    }
}
```

롬복 애너테이션과 상속 받은 PersonalCommandValidating에 대한 설명은 아까 위에서 설명한 RecordPersonalCommand와 동일합니다.  

CorrectPersonalCommand에서 필드멤버가 Personal 클래스처럼 5개인 이유는 나중에 서비스에서 correct 기능을 수행할 때 CorrectPersonalCommand를 통해서 새로운 Personal 객체를 생성하기 위해서입니다.  

이는 나중에 <a href="https://injae7034.github.io/java/addressbook_web_project_02/#correctpersonalservice-%ED%81%B4%EB%9E%98%EC%8A%A4" target="_blank">service 구현 코드</a>에서 더 자세하게 설명드리도록 하겠습니다.  

### CorrectPersonalUseCase 인터페이스
CorrectPersonalUseCase는 넓은 서비스에서 correct라는 메소드, 즉, 주소록에서 이름을 제외한 개인의 정보를 수정하는 유스케이스를 세분화시킨 인터페이스입니다.  

```java
package injae.AddressBook.personal.application.port.in.correct;

public interface CorrectPersonalUseCase {

    Long correctPersonal(CorrectPersonalCommand command);

}
```

내부에는 correctPersonal 메소드 하나만 선언하고 있습니다.  

입력 유효성 검증 역할을 하는 CorrectPersonalCommand를 매개변수로 받습니다.  

service 클래스에서 이 메소드를 정의하면서 CorrectPersonalUseCase 인터페이스를 구현해줍니다.

## erase 패키지
erase 패키지 아래에는 ErasePersonalUseCase 인터페이스 하나가 위치합니다.  

### ErasePersonalUseCase 인터페이스
ErasePersonalUseCase는 넓은 서비스에서 erase라는 메소드, 즉, 주소록에서 개인의 정보를 지우는 유스케이스를 세분화시킨 인터페이스입니다.  

```java
package injae.AddressBook.personal.application.port.in.erase;

import injae.AddressBook.personal.domain.Personal;

public interface ErasePersonalUseCase {

    Personal erasePersonal(Personal personal);

}
```

다른 UseCase의 경우 매개변수로 입력모델 객체를 받고 있지만 ErasePersonalUseCase의 경우 Personal을 그대로 매개변수로 받고 있습니다.  

이는 나중애 Controller에서 GetPersonalQuery를 통해 찾은 Personal 객체를 그대로 매개변수로 사용하기 때문에 따로 입력 모델에 대한 검증이 필요가 없기 때문입니다.  

이 역시 뒤에서 Controller 코드를 설명할 때 좀 더 구체적으로 설명하도록 하겠습니다.  

# out 패키지
out 패키지 아래에는 각 세분화된 서비스에 맞게 세분화된 repository 인터페이스들이 있습니다.  
원래는 넓은 repository가 데이터베이스에 새로운 데이터를 저장하는 save나 데이터를 수정하는 update, 데이터를 하나 찾는 findOne, 데이터를 다 찾는 findAll, 데이터를 지우는 delete 등을 가지고 있는데 이를 모두 세분화하여서 각각의 메소드마다 모두 인터페이스화하여 분리하였습니다.  

아웃고잉 어댑터(패키지에서 out.persistence 패키지 아래에 있는)의 영속성 계층의 구현체들 중에 하나가 이 세분화된 repository 인터페이스를 구현할 것입니다.  

## RecordPersonalRepository 인터페이스

```java
package injae.AddressBook.personal.application.port.out;

import injae.AddressBook.personal.domain.Personal;

public interface RecordPersonalRepository {

    void save(Personal personal);

}
```

RecordPersonalRepository 인터페이스는 Personal 객체를 매개변수로 받는 save 메소드를 선언하고 있습니다.  

## GetPersonalsRepository 인터페이스

```java
package injae.AddressBook.personal.application.port.out;

import injae.AddressBook.personal.domain.Personal;

import java.util.List;

public interface GetPersonalsRepository {

    List<Personal> findAll();

}
```
GetPersonalsRepository 인터페이스는 데이터베이스에 저장된 모든 Personal 테이블의 데이터를 얻어오는 findAll 메소드를 선언하고 있습니다.  

## GetPersonalRepository 인터페이스

```java
package injae.AddressBook.personal.application.port.out;

import injae.AddressBook.personal.domain.Personal;

public interface GetPersonalRepository {

    Personal findOne(Long id);

}
```

GetPersonalRepository 인터페이스는 데이터베이스에 저장된 Personal 테이블의 데이터 중 매개변수로 입력 받은 id에 해당하는 개인의 정보를 얻어 오는 findOne 메소드를 선언하고 있습니다.

## FindPersonalRepository 인터페이스

```java
package injae.AddressBook.personal.application.port.out;

import injae.AddressBook.personal.domain.Personal;

import java.util.List;

public interface FindPersonalRepository {

    List<Personal> findByName(String name);

}
```

FindPersonalRepository 인터페이스는 데이터베이스에 저장된 Personal 테이블의 데이터 중에서 매개변수로 입력 받은 name에 해당하는 개인들의 정보를 얻어 오는 findByName메소드를 선언하고 있습니다.  

## CorrectPersonalRepository 인터페이스

```java
package injae.AddressBook.personal.application.port.out;

import injae.AddressBook.personal.domain.Personal;

public interface CorrectPersonalRepository {

    void update(Personal personal);

}
```

CorrectPersonalRepository 인터페이스는 매개변수로 입력 받은 Personal 객체에 저장된 데이터(변경된 데이터)를 통해 데이터베이스에 저장된 Personal 테이블의 데이터 중에서 해당하는 개인의 정보를 수정하는 update메소드를 선언하고 있습니다.  

## ErasePersonalRepository 인터페이스

```java
package injae.AddressBook.personal.application.port.out;

import injae.AddressBook.personal.domain.Personal;

public interface ErasePersonalRepository {

    void delete(Personal personal);

}
```

ErasePersonalRepository 인터페이스는 데이터베이스에 저장된 Personal 테이블의 데이터 중에서  매개변수로 입력 받은 Personal 객체에 해당하는 개인의 정보를 지우는 delete메소드를 선언하고 있습니다.  

# 서비스 패키지
서비스 패키지 아래에는 아까 세분화한 UseCase와 Query에 대한 인터페이스들을 구현한 클래스들이 위치합니다.  

## RecordPersonalService 클래스

```java
package injae.AddressBook.personal.application.service;

import injae.AddressBook.personal.application.port.in.record.RecordPersonalCommand;
import injae.AddressBook.personal.application.port.in.record.RecordPersonalUseCase;
import injae.AddressBook.personal.application.port.out.RecordPersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@RequiredArgsConstructor
@Transactional
@Service
public class RecordPersonalService implements RecordPersonalUseCase {

    private final RecordPersonalRepository repository;

    @Override
    public Long recordPersonal(RecordPersonalCommand command) {
        Personal personal = new Personal(command.getName(),
                command.getAddress(), command.getTelephoneNumber(),
                command.getEmailAddress());

        repository.save(personal);

        return personal.getId();
    }
}
```

### 애너테이션 설명
롬복의 RequiredArgsConstructor 애너테이션을 통해 필드멤버에 final이 붙거나 @NotNull 이 붙은 필드의 생성자를 자동 생성해줍니다.  

스프링은 Transactional 애너테이션이 붙은 클래스의 메서드를 실행할 때 트랜잭셕을 시작하고, 메서드가 정상적으로 종료되면 트랙잭션을 커밋합니다.  

즉, 데이터베이스에 성공적으로 새로운 데이터가 저장됩니다.  

도중에 런타임 예외가 발생한다면 롤백합니다.  

데이터베이스에 새로운 데이터가 저장되지 않습니다.  

Service 애너테이션의 경우 스프링 컨테이너의 Component 애너테이션을 내부적으로 담고 있어서 컴포넌트 스캔의 검색 대상으로 등록됩니다.  

사실 Service는 Componenet 애너테이션과 큰 차이는 없지만 개발자들에게 명시적으로 Service라는 것을 보여주기 위해 서비스 클래스에 Component대신에 Service 애너테이션을 사용합니다.  

### RecordPersonalUseCase 인터페이스 구현 및 Personal 객체 id 부여
RecordPersonalService 클래스는 RecordPersonalUseCase를 구현하기 위해 recordPersonal메소드를 정의합니다.  

매개변수 RecordPersonalCommand을 활용하여 새로운 Personal 객체를 생성합니다.  

이 때 id를 제외한 매개변수를 가지는 생성자로 Personal 객체를 생성합니다.  

그러면 이 **Personal 객체의 id는 null**일 것 입니다.  

그리고 RecordPersonalService는 자신의 필드 멤버인 repository의 save 메소드를 호출하여 새롭게 생성된 Personal 객체를 데이터베이스 형식에 맞게 매핑하여 저장합니다.  

이 때 **데이터베이스에 저장되면서 Personal의 id 필드멤버에 id가 자동으로 부여**됩니다.  

그 이유는 아까 **Personal 클래스에서 id 필드멤버에 Id 애너테이션을 통해 id가 primary key**라고 설정하였고, **GeneratedValue 애너테이션을 통해 자동으로 id를 생성하도록 설정**하였습니다.  

그래서 **repository의 save 메소드 호출 전에는 Personal 객체의 id가 null**인데 **save 호출 후에는 아이디가 부여**됩니다.  

이제 personal.getId()를 호출하면 null대신에 부여된 아이디가 반환됩니다.  

## GetPersonalsService 클래스

```java
package injae.AddressBook.personal.application.service;

import injae.AddressBook.personal.application.port.in.get.GetPersonalsQuery;
import injae.AddressBook.personal.application.port.out.GetPersonalsRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@RequiredArgsConstructor
@Transactional
@Service
public class GetPersonalsService implements GetPersonalsQuery {

    private final GetPersonalsRepository repository;

    @Override
    public List<Personal> getPersonals() {
        return repository.findAll();
    }
}
```

애너테이션과 구현에 대한 설명은 위의 RecordPersonalService와 동일합니다.  

repository의 findAll메소드를 호출하고 데이터베이스에서 모든 Personal 테이블의 개인 정보를 List로 반환합니다.  

## GetPersonalService 클래스

```java
package injae.AddressBook.personal.application.service;

import injae.AddressBook.personal.application.port.in.find.FindPersonalCommand;
import injae.AddressBook.personal.application.port.in.get.GetPersonalCommand;
import injae.AddressBook.personal.application.port.in.get.GetPersonalQuery;
import injae.AddressBook.personal.application.port.out.GetPersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@RequiredArgsConstructor
@Transactional
@Service
public class GetPersonalService implements GetPersonalQuery {

    private final GetPersonalRepository repository;

    @Override
    public Personal getPersonal(GetPersonalCommand command) {
        return repository.findOne(command.getId());
    }

}
```

애너테이션과 구현에 대한 설명은 위의 RecordPersonalService와 동일합니다.  

repository의 findOne메소드를 호출하고 데이터베이스에서 입력 받은 id에 해당하는 Personal 테이블의 개인 정보를 Personal 객체로 반환합니다.

## FindPersonalService 클래스

```java
package injae.AddressBook.personal.application.service;

import injae.AddressBook.personal.application.port.in.find.FindPersonalCommand;
import injae.AddressBook.personal.application.port.in.find.FindPersonalUseCase;
import injae.AddressBook.personal.application.port.out.FindPersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@RequiredArgsConstructor
@Transactional
@Service
public class FindPersonalService implements FindPersonalUseCase {

    private final FindPersonalRepository repository;

    @Override
    public List<Personal> findPersonalByName(FindPersonalCommand command) {
        return repository.findByName(command.getName());
    }
}
```

애너테이션과 구현에 대한 설명은 위의 RecordPersonalService와 동일합니다.  

repository의 findByName메소드를 호출하고 데이터베이스에서 입력 받은 이름에 해당하는 Personal 테이블의 개인 정보를 List로 반환합니다.(동명이인이 있을 수 있기 때문에 Personal 객체가 아니라 List로 반환함)

## CorrectPersonalService 클래스

```java
package injae.AddressBook.personal.application.service;

import injae.AddressBook.personal.application.port.in.correct.CorrectPersonalCommand;
import injae.AddressBook.personal.application.port.in.correct.CorrectPersonalUseCase;
import injae.AddressBook.personal.application.port.out.CorrectPersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@RequiredArgsConstructor
@Transactional
@Service
public class CorrectPersonalService implements CorrectPersonalUseCase {

    private final CorrectPersonalRepository repository;

    @Override
    public Long correctPersonal(CorrectPersonalCommand command) {
        Personal personal = new Personal(
                command.getId(),
                command.getName(),
                command.getAddress(),
                command.getTelephoneNumber(),
                command.getEmailAddress());

        repository.update(personal);

        return personal.getId();
    }

}
```

애너테이션과 구현에 대한 설명은 위의 RecordPersonalService와 동일합니다.  

여기서 이제 CorrectPersonalCommand에 왜 Personal 객체와 동일하게 id, name, address, telephoneNumber, emailAddress가 모두 필요했는지 설명드리도록 하갰습니다.  

이 서비스의 메소드는 Controller에서 호출될 것인데 이 때 웹에서 받은 정보를 그대로 입력받으면 안되고 유스케이스의 입력유효성을 거친 command를 매개변수로 입력 받아야 합니다.  

id와 name은 웹에서 변경할 수 없지만 address나 telephone, emailAddress의 경우 웹에서 사용자가 수정할 수 있습니다.  

그래서 사용자가 잘못 입력할 경우 데이터베이스에 유효하지 않은 정보가 저장될 수 있기 때문에 Service에서 로직을 실행하기 전에 입력 모델인 CorrectPersonalCommand에서 유효성을 검증해야합니다.  

CorrectPersonalCommand가 입력유효성을 검증할 때 id와 name에 대한 검증을 할 필요는 없지만 만약에 id와 name에 대한 정보가 없으면 Personal객체를 새로 생성할 수 없습니다.  그래서 이를 위해 매개변수로 별도로 받거나 아니면 CorrectPersonalCommand의 멤버에 id와 name을 추가하여 정보를 저장하는 방법이 있는데 저는 매개변수를 여러 개 받는 전자의 방식보다는 하나의 매개변수만 받으면 되는 후자의 방식을 선택하였습니다.  

즉, CorrectPersonalService는 correctPersonal 메소드 내부에서 매개변수로 입력받은 CorrectPersonalCommand 객체를 통해 새로운 Personal 객체를 생성합니다.  

(참고로 이 새로 생성된 Personal 객체의 id와 name은 데이터베이스의 Personal 테이블에 같은 id와 name 데이터가 저장될 것입니다.  

여기서 이 데이터베이스에 이미 저장된 데이터를 수정하기 위해 merge와 dirty-checking 2가지 방법이 있는데 둘 다 일단 새로운 Personal 객체를 생성할 필요가 있습니다.  

저는 dirty-checking을 사용하여 데이터베이스에 정보를 수정할 것이고 이는 뒤에서 repository 구현체에서 더 자세히 설명하도록 하겠습니다.)  

새롭게 생성된 Personal 객체를 repository의 update 메소드의 매개변수로 넘기고 Personal 객체의 id를 반환합니다.  

## ErasePersonalService 메소드

```java
package injae.AddressBook.personal.application.service;

import injae.AddressBook.personal.application.port.in.erase.ErasePersonalUseCase;
import injae.AddressBook.personal.application.port.out.ErasePersonalRepository;
import injae.AddressBook.personal.domain.Personal;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@RequiredArgsConstructor
@Transactional
@Service
public class ErasePersonalService implements ErasePersonalUseCase {

    private final ErasePersonalRepository repository;

    @Override
    public Personal erasePersonal(Personal personal) {
        repository.delete(personal);

        return personal;
    }
}
```

애너테이션과 구현에 대한 설명은 위의 RecordPersonalService와 동일합니다.  

repository의 delete메소드를 호출하고 데이터베이스에서 입력 받은 Personal 객체에 해당하는 Personal 테이블의 개인 정보를 지웁니다.  

그리고 지운 Personal 객체를 반환합니다.  

# 글을 마치며
이로써 personal 패키지 아래의 3개 패키지(adapter, application, domain) 중에서 application 패키지에 대한 설명을 마치도록 하겠습니다.  
다음 글에서 <a href="https://injae7034.github.io/java/addressbook_web_project_03/" target="_blank">adapter</a>에 대한 설명을 하도록 하겠습니다.  
