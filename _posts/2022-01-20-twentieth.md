---
layout: single
title:  "Java프로젝트하면서 추상클래스, 추상메소드, 부모와 자식간의 상속 개념 배우기"
categories: Java
tag: [Java, 프로젝트, 가계부, AccountBook, 오버로딩, ]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 시작하며
이번 시간에는 저번 시간에 만든 Date클래스(LocalDate를 활용하여 커스터마이징한 Date)를 활용하여 가계부(AccountBook)프로그램을 구현하려고 합니다.<br>
<a href="https://injae7034.github.io/java/nineteenth/"
target="_blank">Java의 LocalDate를 활용해서 나만의 Date클래스 만들기</a>
가계부(AccountBook)는 파일이자 컨트롤이기 때문에 데이터를 저장하고 있는 entity 클래스를 먼저 구현하려고 합니다.<br><br>
**entity를 구현하면서 추상클래스, 추상메소드, 부모와 자식간의 상속관계에 대해서 공부**해도록 합시다.<br><br>
entity를 구현하기 위해서 가계부에 어떠한 데이터가 필요한지 알아봅시다.<br><br>
가계부에서 일단 **수입**인지 **지출**인지를 먼저 판단해야 합니다.<br><br>
그 다음으로 돈을 받거나 쓴 **일자**가 필요합니다.<br><br>
그리고 돈을 무슨 목적으로 받았는지 또는 썼는지를 명시할 **적요**가 필요합니다.<br><br>
돈을 얼마나 받았거나 썼는지를 나타내는 **금액**도 필요하고, 이 금액을 바탕으로 현재 **잔액**이 얼마있는지 필요할 것입니다.<br><br>
추가적으로 비고사항을 적기 위한 **비고**란도 필요할 것입니다.<br><br>
그래서 **Income**클래스 생성하여 **수입**에 관한 처리를 하도록 하고, **Outgo**클래스를 생성하여 **지출**에 관한 처리를 하도록 합니다.<br><br>
저번 시간에 만들었던 

# Income() 클래스
 