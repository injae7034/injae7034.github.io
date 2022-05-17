---
layout: single
title: "스프링부트와-타임리프를-활용한-간단한-주소록-토이-프로젝트-육각형(포트와-어댑터)아키텍처-적용"
categories: Java
tag: [Java, 우아한스터디, 만들면서 배우는 클린 아키텍쳐 적용(1~6장), 실전! 스프링 부트와 JPA 활용1_웹 애플리케이션 개발, 타임리프, 어떻게 공부할 것인가?]
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
