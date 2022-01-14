---
layout: single
title:  "Java프로젝트하면서 데이터베이스 관계(Foreign Key, INNER JOIN) 설명 및 MySQL과 연동"
categories: Java
tag: [Java, 명함철, 프로젝트, VisitingCardBinder,  MySQL, 관계형데이터베이스, CUI, Try-with-resocures, 데이터베이스 기초 이론, 데이터베이스 응용프로그래밍, INNER JOIN]
toc: true
toc_label: "목차"
toc_sticky: true
---
# 데이터베이스 개념 설명
데이터베이스에 간단한 개념 설명은 예전에 진행한 주소록 프로그램에서 MySQL과 연동할 때 설명하였습니다.<br>
<a href="https://injae7034.github.io/java/thirteenth/#%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80" target="_blank">데이터베이스 기초 설명</a>

<br><br>
데이터베이스의 기본적인 설명은 위의 링크를 참고하시면 됩니다.<br><br>
이번 시간에는 데이터베이스에서 관계에 대해 좀 더 공부해보려고 합니다.<br><br>

# 데이터베이스 응용프로그래밍 설계
명함철 프로그램과 연관하여 데이터베이스의 관계에 대해 설명하도록 하겠습니다.<br><br>
명함철(VisitingCardBinder)은 control이고 명함(VisitingCard)을 관리합니다.<br><br>
명함은 entity이지만 스스로 데이터를 가지고 있지 않고, 회사(Company)와 개인(Personal)을 entity를 멤버로 가지고 있습니다.<br><br>
Company와 Personal이 실질적인 데이터를 가지고 있기 때문에 Company와 Personal 테이블을 데이터베이스에 만둘어 줍니다.<br><br>
그래서 명함철에서 개체유형이름(Entity Type Name)은 Company(회사)와 Personal(개인)입니다.<br><br>
Company의 속성(Attribute)으로는 name(상호명), address(주소), telephoneNumber(전화번호), faxNumber(팩스번호), url(홈페이지주소), 그리고 각각의 Company 개체인스턴스(Entity Instance)가 가지고 있는 기본키(Primary Key)역할을 하는 companyCode(회사코드)가 있습니다.<br><br>
Personal의 속성(Attribute)으로는 name(이름), position(직책), cellularPhonenumber(휴대폰번호), emailAddress(이메일주소), 그리고 각각의 Personal 개체인스턴스(Entity Instance)가 가지고 있는 기본키(Primary Key)역할을 하는 code(개인코드)가 있습니다.<br><br>
모든 속성들의 자료유형은 문자열로 설정하고, 길이는 각 속성의 특성에 맞게 최대값으로 정해줍니다.<br><br>
출력형식의 경우 나머지는 딱히 정한 것이 없고, 코드의 경우 **회사코드는 대문자C(Company를 가리킴) + 숫자 4자리**, **개인코드는 대문자P(Personal을 가리킴) + 숫자 4자리**로 규칙을 정합니다.<br><br>
예를 들어, 처음 데이터베이스에 입력이 되면 회사코드는 "C0001", 개인코드는 "P0001"이 되고 다음부터는 숫자를 증가시켜서 "C0002", "P0002"가 되는 방식입니다.<br><br>
이 방식으로는 9999명의 개인만 저장할 수 있기 때문에 더 많은 객체를 저장할 필요가 있다면 처음부터 규약을 정할 때 숫자 자리를 더 넉넉하게 잡아야 합니다.<br><br>
필수입력여부를 정할 수 있는데 필수 입력일 경우 NOT NULL, 필수 입력이 아닐 경우 NULL로 설정하지만 명함철에서는 모든 속성들을 필수 입력하도록 설정합니다.<br><br>
중복여부는 중복이 없는 경우 UNIQUE, 중복이 있는 경우는 NOT UNIQUE로 설정합니다.<br><br>
범위는 나머지 속성에는 해당되지 않지만 아까 언급했듯이 코드의 경우 1~9999까지의 범위를 가집니다.<br><br>
또한 나머지 속성들은 기본값이 없지만 코드의 경우 기본값은 처음 시작 코드인 ‘P0001’입니다.<br><br>
유도 알고리즘의 경우 나머지 속성은 해당하지 않고, 코드의 경우 무조건 CXXXX 또는 PXXXX형식으로 순차적으로 숫자가 늘어나면서 작성이 되기 때문에 이렇게 작성이 될 수 있도록 유도하는 알고리즘이 필요합니다.<br><br>

# 데이터베이스 관계 설정
위에서 데이터베이스에서 이용할 Company와 Personal의 테이블을 규칙을 정하여 만들었기 때문에 이제 이를 바탕으로 Company와 Personal의 관계를 설정해야 합니다.<br><br>
관계 설정 전에 데이터베이스 관계 설정에 어떠한 기본 개념이 있는지 알아 보고, 이를 토대로 관계를 설정하도록 하겠습니다.
## 기수성(Cardinality)
현재시점 기준으로 최대 관계횟수를 표현하는 것인데, 개인은 여러 회사를 동시에 다니는 것이 현실적으로 불가능하고 원칙적으로는 한 회사에 소속이 됩니다.<br><br>
그래서 개인의 경우 회사와 최대 관계횟수는 1이지만, 회사에 소속된 개인은 회사의 크기에 따라 다르지만 복수이기 때문에 다수로 표현합니다.

## 선택성(Optionality)
데이터베이스에 저장시점으로 최소 관계횟수를 표현하는 것인데, 명함철에서 회사(Company)와 개인(Personal)은 따로 존재하지 않고 명함(VisitingCard)에 함께 존재하기 때문에 Company와 Personal의 최소값이 1이 됩니다.

## 외래키(Foreign Key)
예전에 제가 자바입출력을 위해 load/save메소드를 구현할 때 Personal.txt와 Company.txt를 서로 연결시키기 위해 Personal.txt에서 회사코드를 이용하였습니다.<br>
<a href="https://injae7034.github.io/java/sixteenth/#%ED%95%B4%EA%B2%B0%EC%B1%85-1" target="_blank">자바입출력에서 회사코드로 개인과 회사파일 연결하기</a>

<br><br>
이처럼 현재 데이터베이스에서도 Personal테이블과 Company테이블이 따로 존재하기 때문에 이를 서로 연결하여야 유의미한 데이터 처리를 할 수 있습니다.<br><br>
그래서 자바입출력에서 사용한 회사코드처럼 Personal테이블에서 회사코드를 추가해줍니다.<br><br>
Personal테이블에 추가한 회사코드는 Company의 회사코드와 동일한 값을 가지는데, 이를 통해 Personal테이블과 Company테이블을 서로 연결시킬 수 있습니다.<br><br>

이러한 역할을 수행하는 Personal테이블의 회사코드를 **Foreign Key(외래키)**라고 합니다.<br><br>

## 업무규칙(Business Rule)
업무 규칙에는 삽입(입력)규칙과 삭제 규칙이 있는데 이를 정의하기 전에 먼저 개인과 회사의 관계를 설정해야 합니다.<br><br>
앞에서도 언급하였듯이 개인은 법적으로 하나의 회사만 다닐 수 있지만, 회사에 소속된 개인은 다수입니다.<br><br>
즉, 하나의 회사는 수많은 개인을 가지고 있기 때문에 회사는 부모가 되고, 개인은 자식의 관계가 설정됩니다.<br><br>
### 삽입(입력)규칙
삽입(입력)규칙은 자식(Personal)에게 적용됩니다.<br>
<ol>
<li>DEPENDENT :<br>
디폴트값으로써, 부모(Company)가 먼저 존재해야 입력이 가능합니다.<br>
왜냐하면 자식(Personal)에 부모(Company)코드가 속성으로 있는데 부모(Company)가 없으면 자식(Personal)의 부모(Company)코드도 있을 수 없기 때문입니다.<br><br>
</li>
<li>NULLIFY :<br>
부모(Company)없이 자식(Personal)을 입력할 경우 부모(Company)코드를 NULL로 처리합니다.<br><br>
</li>
<li>DEFAULT :<br>
부모(Company)코드를 기본값으로 채웁니다.<br><br>
</li>
<li>CUSTOMIZED :<br>
부모(Company)코드를 사용자 개인에 맞게 변경합니다.<br><br> 
</li>
</ol>

### 삭제규칙
삭제규칙은 부모에게 적용됩니다.<br>
<ol>
<li>RESTRICT :<br>
디폴트값으로써, 자식(Personal)이 있는 상태에서 부모(Company)를 먼저 지울 수 없습니다.<br>
부모(Company)를 지우기 위해서는 먼저 자식(Personal)이 다 지워져야 부모(Company)를 지울 수 있습니다.<br><br>
</li>
<li>CARCADE :<br>
부모(Company)를 지우면 그에 딸린 자식(Personal)들도 한꺼번에 다 지워버립니다.<br><br>
</li>
<li>NULLIFY :<br>
부모(Company)만 지우고, 그에 딸린 자식(Personal)의 부모(Company)코드를 모두 NULL로 바꿉니다.<br><br>
</li>
<li>DEFAULT :<br>
부모(Company)만 지우고, 그에 딸린 자식(Personal)의 부모(Company)코드를 기본값으로 변경합니다.<br><br>
</li>
<li>CUSTOMIZED :<br>
부모(Company)만 지우고, 그에 딸린 자식(Personal)의 부모(Company)코드를 사용자가 정한 코드로 변경합니다.<br><br>
</li>
</ol>

# 명함철 데이터베이스 스크립팅(Scripting)
데이터 베이스 생성을 위해서 우선 MySQL 8.0 Command Line Client를 켜서 사용자 설정 비밀번호를 입력합니다.
<br><br>
저는 이미 예전에 C++로 프로그래밍을 할 때, 데이터베이스를 설정해놔서 여기서는 글로 설명하도록 하겠습니다.
<br><br>
이 후에 데이터 정의 언어(Data Definition Language : CREATE(생성), DROP(소멸), ALTER(변경))중에서 데이터베이스 생성을 위해 CREATE을 사용합니다.<br><br>

**CREATE DATABASE visitingcardbinder;**을 입력한 뒤에 데이터 제어 언어(Data Control Language)인 USE를 사용합니다.<br><br>
**USE addressbook;**을 입력합니다.

이 후 Company와 Personal 테이블을 만들기 위해 아래와 같이 커맨드 라인에 입력합니다.<br><br>
CREATE TABLE Company<br>
(<br>
name VARCHAR(32) NOT NULL,<br>
address VARCHAR(64) NOT NULL,<br>
telephoneNumber VARCHAR(12) NOT NULL,<br>
faxNumver VARCHAR(12) NOT NULL,<br>
url VARCHAR(32) NOT NULL,<br>
companyCode CHAR(5) NOT NULL,<br>
CONSTRAINT CompanyPK PRIMARY KEY(companyCode)<br>
);<br><br>

CREATE TABLE Personal<br>
(<br>
name VARCHAR(10) NOT NULL,<br>
position VARCHAR(8) NOT NULL,<br>
cellularPhoneNumber VARCHAR(12) NOT NULL,<br>
emailAddress VARCHAR(32) NOT NULL,<br>
companyCode CHAR(5) NOT NULL,<br>
CONSTRAINT CompanyPersonalFK FOREIGN KEY(companyCode),<br>
code CHAR(5) NOT NULL,<br>
CONSTRAINT PersonlPK PRIMARY KEY(code)<br>
);<br><br>
이렇게 입력해주면 MySQL에 Company와 Personal테이블이 생성됩니다.<br>
VARCHAR은 해당크기만큼 써도 되고 그 아래로 사용해도 될 경우 사용합니다.<br><br>
CHAR은 반드시 정해놓은 크기만큼 사용해야 합니다.<br><br>
코드의 경우 CXXXX 또는 PXXXX형식으로 반드시 5자리이기 때문에 CHAR(5)로 설정하면 됩니다.<br><br>
Company에서 companyCode는 PRIMARY KEY로 설정합니다.<br><br>
Personal에서 code는 PRIMARY KEY로 설정하고, companyCode는 FOREIGN KEY로 설정합니다.<br><br> 
이 커맨드라인에서 데이터 조작 언어(Data Manipulation Language : INSERT(삽입), SELECT(검색, 정렬), DELETE(삭제), UPDATE(갱신))를 통해 직접 데이터를 편집할 수 있습니다.<br><br>
아래에서 예시를 들어 이 데이터 조작 언어를 사용해보겠습니다.

## INSERT(삽입)
INSERT INTO Company(name, address, telephoneNumber, faxNumber, url, companyCode)<br>
VALUES('삼성전자', '서울시 서초구', '024391297', '024391298', 'Samsung.com', 'C0001');<br><br>
이렇게 입력하면 데이터베이스에 입력한 회사 데이터가 저장됩니다.<br><br>
VALUES에 구체적인 값(개체인스턴스)을 입력할 때 반드시 개체유형 순서와 맞게 입력하셔야 합니다.<br><br>
또한 회사데이터가 입력이 되었으면 개인데이터는 반드시 입력이 되어야 합니다<br><br>
왜냐하면 명함(VisitingCard)은 회사 정보와 개인 정보를 함께 가지고 있고, 개인의 정보는 중복이 있을 수 없기 때문에 명함철에 명함을 끼울 때 회사 정보만 기입하는 경우는 없습니다.<br><br>
그래서 회사 정보를 입력했기 때문에 아래와 같이 회사에 소속된 개인의 정보를 반드시 데이터베이스에 추가해줍니다.<br><br> 
INSERT INTO Personal(name, position, cellularPhoneNumber, emailAddress, code, companyCode)<br>
VALUES('홍길동', '대리', 01049817764', Hong@naver.com', 'P0001', 'C0001');<br><br>
이렇게 입력하면 데이터베이스에 입력한 개인 데이터가 저장됩니다.<br><br>
개인 데이터의 경우 개인이 다니는 회사가 이미 데이터베이스에 있는 경우(회사 정보가 중복이 있는 경우)에는 데이터베이스에 개인 데이터만 추가할 수 있습니다.<br><br>
그래서 예를 들어 현재 데이터베이스에는 위에서 추가한 "삼성전자" 회사 데이터가 있고, "홍길동" 개인 데이터가 있습니다.<br><br>
이 때 명함철의 명함에 "삼성전자"를 다니는 또다른 개인인 "김길동"을 끼운다고 가정합시다.<br><br>
이 때 회사정보는 이미 데이터베이스에 중복이 있기 때문에 스크립팅을 할 필요없고, 아래 스크립팅처럼 개인 데이터인 "김길동"의 데이터만 스크립팅하면 됩니다.<br><br> 
INSERT INTO Personal(name, position, cellularPhoneNumber, emailAddress, code, companyCode)<br>
VALUES('김길동', '과장', 01049790464', Kim@naver.com', 'P0002', 'C0001');<br><br>

## SELECT(검색, 정렬)
SELECT Company.*, Personal.* FROM Company, Personal WHERE Company.companyCode = Personal.companyCode AND Company.name = '삼성전자';<br><br>
위의 SELECT문을 입력하면 Company테이블에서 name이 "삼성전자"이고, Company테이블에서 companyCode와 Personal테이블에서 companyCode과 같은 개인의 모든 정보를 회사 정보와 함께 출력합니다.<br><br>
아래 이미지는 실제 제가 만들어놓은 데이터베이스에서 위의 쿼리문을 입력해서 찾은 결과입니다.<br><br>
![SELECT문 결과](../../images/2022-01-13-eighteenth/SELECT문 결과.jpg)
<br><br>

## INNER JOIN을 이용한 SELECT
위의 쿼리문을 **INNER JOIN**을 이용하여 똑같은 결과를 출력하도록 바꿀 수 있습니다.<br><br>
SELECT Company.*, Personal.* FROM Company INNER JOIN Personal ON Company.companyCode = Personal.companyCode WHERE Company.companyCode = 'C0001';
<br><br>
위의 스크립팅처럼 INNER JOIN을 활용한 결과 아래처럼 같은 결과를 검색하는 것을 확인할 수 있습니다.<br><br>
![INNERJOIN이용한SELECT결과](../../images/2022-01-13-eighteenth/INNERJOIN이용한SELECT결과.jpg)
<br><br>
SELECT Company.*, Personal.* FROM Company INNER JOIN Personal ON Company.companyCode = Personal.companyCode;
<br><br>
위의 쿼리문을 입력하면 데이터베이스에 있는 회사와 개인의 데이터를 전부 출력하는데 이 때 회사와 개인이 서로 연결된 상태로 출력됩니다다.<br><br>
![INNERJOIN이용한전체SELECT결과](../../images/2022-01-13-eighteenth/INNERJOIN이용한전체SELECT결과.jpg)

## Delete
DELETE FROM Personal WHERE code = 'P0001';<br><br>
위의 쿼리문을 입력하면 코드가 P0001인 개인의 데이터를 데이터베이스에서 지웁니다.<br><br>
위의 INSERT에서 사용한 예시의 결과를 그대로 사용하면 "홍길동"이 데이터베이스에서 지워집니다.<br><br>
DELETE FROM Personal WHERE code = 'P0002';<br><br>
위의 쿼리문을 입력하면 코드가 P0002인 개인의 데이터를 데이터베이스에서 지웁니다.<br><br>
위의 INSERT에서 사용한 예시의 결과를 그대로 사용하면 "김길동"이 데이터베이스에서 지워집니다.<br><br>
DELETE FROM Company WHERE companyCode = 'C0001';<br><br>
이제 부모(회사)인 "삼성전자"에 다니는 자식(개인)들을 다 지웠기 때문에 회사(부모)데이터인 "삼성전자"를 데이터베이스에서 지울 수 있습니다.<br><br>
부모(회사)에 소속되어 있는 자식(개인)들이 데이터베이스에 남아 있는 경우 부모(회사)를 먼저 지울 수 없습니다.<br><br>

# 명함철과 MySQL을 연동하기 위한 메소드
명함철에 MySQL을 연동하기 위해 Main.java에 우선 import java.sql.*;를 합니다.<br><br>
프로그램이 시작할 때  Main클래스의 main메소드에서 명함철을 먼저 생성한 다음 load메소드를 호출하는데 이를 통해 MySQL에 있는 데이터베이스를 명함철로 가져 옵니다.<br><br>
프로그램이 종료되기 전에 main메소드에서 save메소드를 호출하는데 이를 통해 명함철에 있는 Company와 Personal객체들의 정보를 MySQL에 저장합니다.<br><br>
명함철에서 takeIn(끼우기)메소드 호출 한 후에 반환되는 VisitingCard의 참조주소값을 매개변수로 이용하는 insert를 호출하는데 이를 통해 명함철에 끼운 명함정보에서 Company와 Personal의 데이터를 MySQL에도 실시간으로 추가합니다.<br><br>
insert 메소드는 내부에서 매개변수로 입력 받은 명함(VisitingCard)의 회사정보가 데이터베이스에 중복이 없으면
getCompanyCode메소드를 호출하여 companyCode를 구한 다음 MySQL에 companyCode와 함께 데이터를 insert합니다.<br><br>회사 정보가 이미 데이터베이스에 있는 경우는 getCompanyCode를 호출하지도 않고, 회사정보를 데이터베이스에 insert하지도 않습니다.<br><br>
매개변수로 받은 명함(VisitingCard)의 개인정보는 중복이 있을 수 없기 때문에 무조건 getPersonal메소드를 호출하여 code를 구한 다음 MYSQL에 code와 함께 insert합니다.<br><br>
getCompanyCode나 getPersonalCode메소드는 아까 코드 유도알고리즘을 통해 구현된 메소드로 CXXXX또는 PXXXX형식으로 순차적으로 숫자를 증가시키는 코드를 생성하여 반환하는 메소드입니다.<br><br>
명함철에서 takeOut(꺼내기)메소드를 호출 한 후에 반환받은 명함의 참조변수값을 매개변수로 하는 delete메소드를 호출하는데 이를 통해 명함철에서 꺼낸 명함정보에서 Company와 Personal의 데이터를 MySQL에도 실시간으로 반영합니다.<br><br>
delete내부에서 Personal의 경우 무조건 지우고, Company의 경우 자신에게 소속된 자식(Personal)이 있는지 확인 후에 자식이 있으면 지우지 않고, 자식이 없는 경우에는 Company도 데이터베이스에서 지웁니다.<br><br>
마지막으로 명함철에서 이름을 기준으로 오름차순으로 정렬할 때 arrange메소드 호출 이후에, replace메소드를 호출하는데 이를 통해 MySQL에 있는 데이터도 이름 기준으로 오름차순으로 정렬합니다.<br><br> 
명함철에서는 개념상 명함의 정보를 수정하는 기능을 제공하지 않기 때문에 데이터베이스의 데이터를 수정하는 기능인 update메소드는 정의하지 않습니다.

## load 코드
```java
public class Main
{
    //load
    public static void load()
    {
        String sql = "SELECT Personal.name, Personal.position, Personal.cellularPhoneNumber," +
                " Personal.emailAddress, Company.name, Company.address, Company.telephoneNumber," +
                " Company.faxNumber, Company.url FROM Company INNER JOIN Personal ON " +
                "Company.companyCode = Personal.companyCode ORDER BY Personal.code;";
        try(Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306" +
                    "/VisitingCardBinder?serverTimezone=Asia/Seoul", "root", "1q2w3e");
            Statement stmt = con.createStatement();
            ResultSet rs = stmt.executeQuery(sql))
        {
            //rs에 저장된 데이터가 끝날때까지 반복한다.
            while(rs.next())
            {
                //새로 생성한 명함을 명함철에 끼운다.
                visitingCardBinder.takeIn(new VisitingCard(rs.getString(1),
                        rs.getString(2), rs.getString(3),
                        rs.getString(4), rs.getString(5),
                        rs.getString(6), rs.getString(7),
                        rs.getString(8), rs.getString(9)));
            }
        }
        catch (SQLException e)
        {
            System.out.println("[SQL Error : " + e.getMessage() +"]");
        }
    }
}
```

### load 코드 설명
우선 위의 SELECT 쿼리문은 개인의 회사코드와 회사의 회사코드가 같은 회사 정보를 개인 정보와 연결하여 같이 출력해주는 쿼리문입니다.<br><br>
원래 디스크파일에서는 개인과 개인이 다니는 회사정보를 자동으로 매칭시켜 주지 않기 때문에 개인에 저장된 회사코드를 통해 반복을 돌리면서 맞는 회사코드를 찾아서 매칭시켜주는 과정이 필요했습니다.<br><a href="https://injae7034.github.io/java/sixteenth/#load" target="_blank">자바입출력 load메소드</a>

<br><br>
하지만 데이터베이스에서는 FOREIGN KEY(외래키)를 이용하여 INNER JOIN을 사용하면 자동으로 개인과 회사정보를 매칭해주기 때문에 프로그래머가 매칭을 위해 로직을 따로 세울 필요가 없습니다.<br><br>
마지막에 ORDER BY Personal.code를 붙여주는 이유는 데이터베이스에서 이렇게 따로 명시를 하지 않으면 정렬 순서 1순위가 부모(회사)코드 순이기 때문입니다.<br><br>
예를 들어, "삼성전자"를 다니는 "홍길동"을 처음에 명함철에 끼우면 데이터베이스의 회사 테이블에 "삼성전자"가 저장되면서 회사코드는 "C0001"로 저장되고, 개인 테이블에 "홍길동"이 저장될 때 코드는 "P0001", 회사코드는 "C0001"로 저장됩니다.<br><br>
두번째로 "엘지전자"에 다니는 "김길동"을 명함철에 끼우면 데이터베이스의 회사 테이블에 "엘지전자"가 저장되면서 회사코드는 "C0002"로 저장되고, 개인 테이블에 "김길동"이 저장될 때 코드는 "P0002", 회사코드는 "C0002"로 저장됩니다.<br><br>
세번째로 "삼성전자"에 다니는 "박길동"을 명함철에 끼우면 데이터베이스의 회사 테이블에 회사정보는 이미 있기 떄문에 따로 저장이 되지 않고, 개인 테이블에 "박길동"이 저장되면서 코드는 "P0003", 회사코드는 "C0001"로 저장됩니다.<br><br>
이제 위의 쿼리문에서 ORDER BY Personal.code없이<br><br>
SELECT Personal.name, Personal.position, Personal.cellularPhoneNumber, Personal.emailAddress, Company.name, Company.address, Company.telephoneNumber, Company.faxNumber, Company.url FROM Company INNER JOIN Personal ON Company.companyCode = Personal.companyCode;<br>
데이터베이스에 입력하면 부모 코드인 회사코드가 1순위이기 때문에 출력이 홍길동(C0001, P0001), 박길동(C0001, P0003), 김길동(C0002, P0002)순으로 출력이 됩니다.<br><br>
회사 코드의 경우 중복이 있으면 데이터베이스에 추가하지 않고, 개인의 경우 중복이 없기 때문에 항상 데이터베이스에 추가하기 때문에 개인의 코드 순서가 명함철에서 명함을 끼운 순서를 반영하는데, 이렇게 회사코드 순으로 출력이 된 상태로 데이터베이스에 있는 데이터를 명함철로 옮기게 되면 원래 명함철에 명함을 끼운 순서가 무너지게 됩니다.<br><br>
물론 이 무너진 상태를 일관적으로 유지하면 프로그램은 문제가 없이 돌아가지만 이전에 프로그램이 종료되기 전에 save메소드를 호출하여 명함철에 데이터를 데이터베이스로 옮기는데 이 때 명함철에 명함을 끼운 순서를 보존하기 위해서는 ORDER BY Personal.code를 뒤에 붙여서 명함철에서 명함을 끼운 순서를 보존하도록 합니다.<br><br>
Connection은 데이터베이스와 연결을 위한 클래스인데 DriveManager의 getConnection(연결문자열, DB-ID, DB-PW)메소드를 통해 Connection객체와 데이터베이스를 연동시킵니다.<br><br>
Statement 클래스는 일회성 SQL 문을 데이터베이스에 보내기 위한 클래스인데, Connection객체의 createStatement()메소드를 통해 객체를 생성합니다.<br><br>
ResultSet클래스는 쿼리문을 실행하고 난 결과를 저장하는 테이블형태의 클래스인데 stmt.executeQuery(sql);를 통해 sql 문장을 실행하고 그 결과를 리턴하여 ResultSet에 저장함으써 객체를 생성합니다.<br><br>
참고로 쿼리문이 SELECT일 경우는 Statement객체의 executeQuery메소드를 이용하고, 그 외에는(INSERT, UPDATE, DELETE) Statement메소드의 executeUpdate메소드를 이용합니다.<br><br>
이 3가지 클래스의 객체들 모두 이용을 하고 나면 close메소드를 호출하여 프로그래머가 자원을 반납해야 합니다.<br><br>이 과정이 번거롭기 때문에 Try-with-resources를 통해 try()소괄호 안에서 이 3가지 클래스들을 생성하면 try-catch구문이 끝날 때 자동으로 자원을 반납해줍니다.<br><br>
ResultSet rs = stmt.executeQuery(sql);이후에 rs에는 쿼리문의 실행결과로 데이터베이스에 있는 모든 개인들의 정보가 자신이 소속된 회사 정보와 함께 개인코드기준으로 오름차순으로 출력된 데이터를 테이블형식으로 가지고 있을 것입니다.<br><br>
(name, position, cellularPhoneNumber, emailAddress, companyName, address, telephoneNumber, faxNumber, url 이 순으로 말이죠...)<br><br>
ResultSet의 객체rs의 경우 처음에는 첫 데이터가 저장되어 있는 곳의 이전 위치를 가리키고 있습니다.<br><br>
그래서 next메소드를 처음 실행했을 때 비로소 첫번째 데이터를 가리키게 됩니다.<br><br>
그리고 next를 통해 이동했을 때 더이상 가리키는 데이터가 없을 경우 false를 반환합니다.<br><br>
그래서 while(rs.next())을 통해 rs테이블의 처음부터 데이터가 없을 때까지 반복을 돌립니다.<br><br>
반복구조 내부에서는 rs의 메소드인 getString()을 통해 순차적으로 개인의 정보와 회사의 정보를 구합니다.<br><br>
그리고 이 정보를 바탕으로 명함을 생성하여 명함철에 takeIn해줍니다.<br><br>
파일이 끝날 때까지 반복을 하기 때문에 rs에 10명의 Personal정보가 있다면 10번을 반복하면서 순차적으로 명함철에 명함을 10개 끼우게 됩니다.

## save 코드
```java
public class Main
{
    //save
    public static void save()
    {
        String sql = "SELECT Company.companyCode, Personal.code FROM Company INNER JOIN Personal " +
        "ON Company.companyCode=Personal.companyCode ORDER BY Personal.code;";
        try(Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306" +
                    "/VisitingCardBinder?serverTimezone=Asia/Seoul", "root", "1q2w3e");
            Statement stmt = con.createStatement();
            ResultSet rs = stmt.executeQuery(sql);
            PreparedStatement pstmt = con.prepareStatement("DELETE FROM Personal;"))
        {
            pstmt.executeUpdate();
            pstmt.executeUpdate("DELETE FROM Company;");
            String companyCode;//회사코드를 저장할 임시공간
            String personalCode;//개인코드를 저장할 임시공간
            //for each 구문을 통해 명함철의 처음 명함부터 마지막 명함까지 반복한다.
            for (VisitingCard visitingCard : visitingCardBinder.getVisitingCards())
            {
                //rs를 다음으로 이동시킨 뒤에 데이터가 있으면
                if(rs.next())
                {
                    //rs에서 companyCode를 구한다.
                    companyCode = rs.getString(1);
                    //rs에서 구한 companyCode로 db에서 해당코드가 있는지 찾는 쿼리문 만들기
                    sql = String.format("SELECT Company.name FROM Company WHERE companyCode = '%s';",
                            companyCode);
                    //rs2를 새로 생성하여 companyCode로 찾은 상호명을 저장함.
                    try(ResultSet rs2 = pstmt.executeQuery(sql))
                    {
                        //rs2를 다음으로 이동시킨 뒤에 데이터가 없으면(해당 companyCode가 db에 없으면)
                        if(rs2.next() == false)
                        {
                            //db에 새로운 회사정보를 추가한다.
                            sql = String.format("INSERT INTO Company(name, address," +
                                            " telephoneNumber, faxNumber, url, companyCode) " +
                                            "VALUES('%s', '%s', '%s', '%s', '%s', '%s');",
                                    visitingCard.getCompanyName(), visitingCard.getAddress(),
                                    visitingCard.getTelephoneNumber(), visitingCard.getFaxNumber(),
                                    visitingCard.getUrl(), companyCode);
                            pstmt.executeUpdate(sql);
                        }
                    }
                    //rs에서 personalCode를 구한다.
                    personalCode = rs.getString(2);
                    //db에 새로운 개인 정보를 추가한다.
                    sql = String.format("INSERT INTO Personal(name, position," +
                                    " cellularPhoneNumber, emailAddress, code, companyCode) " +
                                    "VALUES('%s', '%s', '%s', '%s', '%s', '%s');",
                            visitingCard.getPersonalName(), visitingCard.getPosition(),
                            visitingCard.getCellularPhoneNumber(), visitingCard.getEmailAddress(),
                            personalCode, companyCode);
                    pstmt.executeUpdate(sql);
                }
            }
        }
        catch (SQLException e)
        {
            System.out.println("[SQL Error : " + e.getMessage() +"]");
        }
    }
}
```

### save 코드 설명
제일 먼저 위치한 쿼리문은 데이터베이스에 있는 데이터중에 모든 companyCode와 personalCode를 출력하는 쿼리문인데 이때 personalCode기준으로 오름차순하여 검색하는 쿼리문입니다.<br><br>
이를 통해 명함이 끼워진 순서대로(개인코드를 기준으로 오름차순으로)개인코드와 회사코드 데이터를 구할 수 있습니다.<br><br>
Connection객체를 생성하고, Statement객체를 생성하여 ResultSet객체에 위의 쿼리문의 결과를 테이블 형식으로 저장합니다.<br><br>
PreparedStatement은 Statement와는 다르게 생성할 때 쿼리문을 매개변수로 넣어서 생성합니다.<br><br>
Statement은 쿼리문을 일회성으로 사용할 경우, 즉 미정인 상태가 없이 확정이 된 경우 주로 1회용으로 사용이 됩니다.<br><br>
PreparedStatement는 쿼리문이 여러번 반복하여 사용될 경우 사용합니다.<br><br>
또한 쿼리문에서 각 필드들의 구체적인 값이 미정으로 되어 있을 때 이 값을 변경하면서 사용할 수 있습니다.<br><br>
즉, 반복적으로 쿼리문이 사용될 때 계속해서 사용되며, 계속해서 필드값이 바뀔 때 유용하다고 할 수 있습니다.<br><br>
“DELETE FROM Personal;”을 넣어서 PreparedStatement객체를 생성하였습니다.<br><br>
그리고 load와 마찬가지로 Connection, Statement, ResultSet, PreparedStatement는을 try()소괄호 안에서 호출함으로써 자동으로 자원이 반납되도록 합니다.<br><br>
try구문 내에서 executeUpdate를 실행하면 데이터베이스에 있는 개인의 정보를 모두 지웁니다.<br><br>
자식(Personal)의 데이터가 데이터베이스에서 모두 지운 후에야 비로소 부모(Company)의 데이터를 데이터베이스에서 지울 수 있습니다.<br><br>
이제 데이터베이스에서 Company의 모든 데이터를 지우기 위해 **pstmt.executeUpdate("DELETE FROM Company;");**를 실행합니다.<br><br>
이처럼 PreparedStatement는 여러번 재사용이 가능하기 때문에 필요할 때마다 객체를 생성해주거나, 객체를 따로 생성하지 않더라도 하나의 객체를 이용하여, executeUpdate 또는 executeQuery를 할 때 그 매개변수로 자신이 원하는 sql문을 보내주면 데이터베이스에 해당 sql문을 보낼 수 있습니다.<br><br>
이 후 for each 반복문을 통해 명함철의 처음 명함부터 마지막 명함까지 반복합니다.<br><br>
for each 반복문내에서 **if(rs.next())**를 통해 rs(위에서 쿼리문을 통해 데이터베이스의 전체 개인코드와 회사코드를 테이블형식으로 미리 저장해둠)를 다음으로 이동시킨 뒤에 데이터가 있는지 확인합니다.<br><br>
rs에 데이터가 있다면 선택구조로 들어간 다음에 getString을 통해 companyCode를 구합니다.<br><br>
rs에서 구한 companyCode로 데이터베스에서 해당코드가 있는지 찾는 쿼리문을 만듭니다.(**SELECT Company.name FROM Company WHERE companyCode = '%s';", companyCode**)<br><br>
이제 이 데이터베이스에 이 쿼리문을 검색한 결과를 저장할 또다른 ResultSet객체인 rs2를 생성하여, 위 쿼리문의 결과를 저장합니다.<br><br>
이 때 에도 try()소괄호안에 rs2를 생성함으로써 자동으로 자원해제가 될 수 있도록 해줍니다.<br><br>
rs2에는 회사코드로 상호명을 찾은 결과가 저장되어 있을텐데 데이터가 있다면 회사코드가 데이터베이스에 있다는 의미이기 때문에 이미 해당 회사는 데이터베이스에 있다는 말이고, 데이터가 없다면 회사코드가 데이터베이스에 없다는 의미이기 때문에 해당 회사는 데이터베이스에 없다는 말입니다.<br><br>
(참고로 설명드리면 처음에는 당연히 데이터베이스에 회사의 정보와 개인의 정보가 하나도 없을 것입니다.<br><br>
왜냐하면 앞에서 DELETE를 통해 데이터베이스에서 모든 개인의 정보와 회사의 정보를 이미 지웠기 때문입니다.<br><br>하지만 for each 반복문을 통해 명함철의 처음 명함부터 마지막 명함까지 반복을 돌리면서 데이터베이스에 개인의 정보와 회사의 정보를 다시 추가하다보면 회사 데이터의 경우 중복이 생길 수 있기 때문에 이 중복을 없애기 위해 **SELECT Company.name FROM Company WHERE companyCode = '%s';", companyCode**을 통해 rs2에 데이터 존재유무로 데이터베이스에 회사 중복여부를 체크합니다.)<br><br>
그래서 이를 확인하기 위해 rs2를 다음으로 이동시킨 뒤에 데이터가 없는지 확인하는 선택문(**if(rs2.next() == false)**)을 사용합니다.<br><br>
false라면 해당 코드는 데이터베이스에 없기 때문에 companyCode와 함께 해당 회사정보를 데이터베이스에 저장하고, false가 아니라면 해당 코드는 데이터베이스에 이미 있기 때문에 그냥 넘어갑니다.<br><br>
그 다음 다시 rs에서 getString을 통해 개인코드를 구하는데, 개인의 경우 회사와 다르게 중복이 있는지 확인할 필요없이 바로 개인정보와 개인코드를 데이터베이스에 추가합니다.<br><br>
이 사이클이 for each반복구조의 한 사이클이며, 이를 명함철의 명함이 끝날 때까지 반복합니다.<br><br>

## getCompanyCode 코드
```java
public class main
{
    //getCompanyCode
    public static String getCompanyCode()
    {
        String code = "C0000";
        String newCode = null;
        //회사코드를 기준으로 내림차순하여 회사코드를 검색함.
        String sql = "SELECT Company.companyCode FROM Company ORDER BY companyCode DESC;";
        try(Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306" +
                    "/VisitingCardBinder?serverTimezone=Asia/Seoul", "root", "1q2w3e");
            Statement stmt = con.createStatement();
            ResultSet rs = stmt.executeQuery(sql))
        {
            //rs에서 마지막으로 이동한 후에 저장된 데이터가 있으면
            //내림차순 정렬이기떄문에 처음으로 이동하면 마지막 회사코드임.
            if(rs.next())
            {
                //마지막 코드를 저장한다.
                code = rs.getString(1);
            }
            //회사코드에서 숫자부분만 추출함
            code = code.substring(1,5);
            //문자열로 된 숫자부분을 정수화시킴
            int number = Integer.parseInt(code);
            //코드에 값을 1증가시킨 다음 다시 문자열 회사코드 생성
            number++;
            newCode = String.format("C%04d", number);
        }
        catch (SQLException e)
        {
            System.out.println("[SQL Error : " + e.getMessage() +"]");
        }
        return newCode;
    }
}
```

### getCompanyCode 코드 설명
우선 String code = “C0000”;으로 초기화를 합니다.<br><br>
**SELECT Company.companyCode FROM Company ORDER BY companyCode DESC;**은 회사코드를 내림차순으로 정렬하는 쿼리문입니다.<br><br>
즉, 마지막 회사코드문이 제일 먼저 위치하게 됩니다.<br><br>
위에서 했던 것처럼 try()소괄호 안에서 ResultSet객체를 생성하면 이제 resultSet객체에는 회사코드기 내림차순으로 정렬된 채로 저장이 되있을 것입니다.<br><br> 
이 후 try구문 내에서 if(rs.next())를 통해 데이터의 처음으로 이동한 후 데이터가 있으면 데이터(회사코드)를 구합니다.(code = rs.getString(1);)<br><br>
이 회사코드는 위에서 내림차순으로 정렬했기 때문에 마지막 회사코드입니다.<br><br>
if(rs.next())처럼 선택구조를 한 이유는 처음 입력될 경우, 즉, 데이터베이스에 아무 데이터도 저장이 되어 있지 않은 경우에는 가져올 코드가 없기 때문에 에러가 발생하는데 이를 방지하기 위해 최소 1개이상의 데이터가 있을 경우에만 선택구조로 들어가서 마지막 코드를 가져올 수 있도록 하였습니다.<br><br>
이후 code.substring(1,5);를 통해 C를 제외하고 숫자 4자리만 문자열로 얻어옵니다.<br><br>
선택구조로 들어갔다 나왔으면 마지막 코드 4자리가 있을 것이고, 기존 데이터없이 처음 입력하는거라 선택구조를 들어가지 않았다면 0000이 code에 저장될 것입니다.<br><br>
number = Integer.parseInt(code);를 통해 이 숫자문자열을 정수형으로 변경시켜줍니다.<br><br>
그리고 +1(number++;)을 합니다.<br><br>
숫자로 변경될 때 앞의 0들은 사라지기 때문에 String으로 다시 변경할 때는 **C%04d**를 통해 앞에 없앴던 C를 다시 붙여주고, 0도 다시 채워줍니다.<br><br>
예를 들어, 마지막으로 코드가 10이였으면 +1을 하여 11이 되고, 이를 위의 방식대로 문자열로 바꿔주면 “C0011”이 됩니다.<br><br>
이제 이 완성된 코드를 반환하면 insert에서 이 코드를 이용하여 데이터베이스에 새로운 회사 정보를 추가합니다.

## getPersonalCode 코드
```java
public class main
{
    //getPersonalCode
    public static String getPersonalCode()
    {
        String code = "P0000";
        String newCode = null;
        String sql = "SELECT Personal.code FROM Personal ORDER BY code DESC;";
        try(Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306" +
                    "/VisitingCardBinder?serverTimezone=Asia/Seoul", "root", "1q2w3e");
            Statement stmt = con.createStatement();
            ResultSet rs = stmt.executeQuery(sql))
        {
            if(rs.next())
            {
                //마지막 코드를 저장한다.
                code = rs.getString(1);
            }
            //개인 코드에 숫자부분만 추출함
            code = code.substring(1,5);
            //문자열로 된 숫자부분을 정수화시킴
            int number = Integer.parseInt(code);
            //숫자1을 증가시키고 다시 개인문자코드화시킴
            number++;
            newCode = String.format("P%04d", number);
        }
        catch (SQLException e)
        {
            System.out.println("[SQL Error : " + e.getMessage() +"]");
        }
        return newCode;
    }
}
```

### getPersonalCode 설명
모든 코드가 getCompanyCode와 같기 때문에 설명을 생략합니다.<br><br>
다만 회사의 경우 getCompanyCode에서 회사코드를 기준으로 하는 ORDER BY companyCode DESC를 쓰고, 개인의 경우 개인코드를 기준으로 하는 ORDER BY code DESC추가하여 SELECT쿼리문을 실행시켜야 합니다.<br><br>
특히 개인코드의 경우에는 ORDER BY code DESC를 생략하게되면 부모인 회사코드 기준으로 검색하기 때문에 personalCode는 더 앞에 있지만(먼저 추가되었지만) 개인코드기준이 아니면(회사코드기준이면) 나중에 입력된 personalCode보다 뒤에가는경우가 있어서 개인코드 중복이 발생할 수 있음.(새로운 코드를 생성할 때는 항상 개인의 마지막코드 +1 을 하여 생성하는데 회사기준으로 개인코드가 출력되면 마지막에 검색된 개인코드가 마지막이 아닐 수도 있기 때문에!)<br><br>
이는 앞의 load와 save에서 이미 설명한 것과 같은 내용입니다.<br><br> 
즉, 이러한 혼선을 막기 위해 getCompanyCode에서는 뒤에 ORDER BY companyCode DESC를 써주고, getPersonalCode에서는 반드시 뒤에 ORDER BY code DESC를 추가하도록 합니다.

## insert 코드
```java
public class main
{
    //insert
    public static void insert(VisitingCard visitingCard)
    {
        //입력받은 명함의 정보로 DB에서 해당하는 회사코드가 있는지 찾는 쿼리문생성
        String sql = String.format("SELECT Company.companyCode FROM Company WHERE name='%s' AND" +
                        " address='%s' AND telephoneNumber='%s' AND faxNumber='%s' AND url='%s';",
                visitingCard.getCompanyName(), visitingCard.getAddress(),
                visitingCard.getTelephoneNumber(), visitingCard.getFaxNumber(),
                visitingCard.getUrl());
        try(Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306" +
                "/VisitingCardBinder?serverTimezone=Asia/Seoul", "root", "1q2w3e");
            PreparedStatement pstmt = con.prepareStatement(sql);
            //rs에 위의 쿼리문으로 찾은 결과를 저장함.
            ResultSet rs = pstmt.executeQuery())
        {
            String companyCode;
            //쿼리문으로 DB에서 찾은 데이터가 있으면(rs에 저장된 데이터가 있으면)(중복이 있으면)
            if(rs.next() == true)
            {
                //해당데이터의 companyCode를 읽는다.
                companyCode = rs.getString(1);
            }
            //쿼리문으로 DB에서 찾은 데이터가 없으면(중복이 없으면)
            else
            {
                //새로운 companyCode를 생성한다.
                companyCode = getCompanyCode();
                //데이터베이스에 새로운 회사정보를 추가하기 위한 쿼리문 생성하기
                sql = String.format("INSERT INTO Company(name, address," +
                                " telephoneNumber, faxNumber, url, companyCode) " +
                        "VALUES('%s', '%s', '%s', '%s', '%s', '%s');",
                        visitingCard.getCompanyName(), visitingCard.getAddress(),
                        visitingCard.getTelephoneNumber(), visitingCard.getFaxNumber(),
                        visitingCard.getUrl(), companyCode);
                pstmt.executeUpdate(sql);
            }
            //데이터베이스에서 새로운 개인정보를 추가하기 위한 쿼리문 생성하기
            sql = String.format("INSERT INTO Personal(name, position, " +
                            "cellularPhoneNumber, emailAddress, code, companyCode) " +
                    "VALUES('%s', '%s', '%s', '%s', '%s', '%s');",
                    visitingCard.getPersonalName(), visitingCard.getPosition(),
                    visitingCard.getCellularPhoneNumber(), visitingCard.getEmailAddress(),
                    getPersonalCode(), companyCode);
            pstmt.executeUpdate(sql);
        }
        catch (SQLException e)
        {
            System.out.println("[SQL Error : " + e.getMessage() +"]");
        }
    }
}
```

### insert코드 설명
insert메소드는 명함철에서 takeIn을 한 후에 반환받는 명함(VisitingCard)객체의 참조변수값을 매개변수로 전달 받는데 이를 통하여 명함철에 끼운 명함의 정보를 알 수 있습니다.<br><br>
이 명함 정보 중에서도 회사 정보를 통해 **SELECT Company.companyCode FROM Company WHERE name='%s' AND address='%s' AND telephoneNumber='%s' AND faxNumber='%s' AND url='%s';** 데이터베이스에 해당 companyCode가 있는지 없는지 검색(SELECT)을 할 수 있습니다.<br><br>
그 결과 데이터가 있으면(데이터베이스에 매개변수로 전달 받은 명함의 회사정보로 검색을 한 결과 회사코드가 있으면)<br><br>
즉, rs.next()가 true이면<br><br>
getString을 통해 해당 데이터에서 companyCode를 구합니다.<br><br>
데이터가 없으면(데이터베이스에 매개변수로 전달 받은 명함의 회사정보로 검색을 한 결과 회사코드가 없으면)<br><br>
데이터베이스에 해당 회사정보가 없기 떄문에(중복이 없기 때문에)getCompanyCode를 호출하여 새로운 회사코드를 생성한 다음에 매개변수로 전달 받은 명함의 회사정보와 새로 생성한 코드를 데이터베이스에 INSERT해줍니다.<br><br>
개인의 경우 중복성 체크없이 매개변수로 전달 받은 명함의 개인 정보와 getPersonalCode를 호출하여 새로 생성한 개인코드와 companyCode(**위에서 검색 데이터가 있었으면 getString을 통해 얻은 companyCode일 것이고, 검색 데이터가 없었으면 getCompanyCode를 호출해 새로 생성한 companyCode임**)를 데이터베이스에 INSERT해줍니다.

## delete 코드
```java
public class main
{
     //delete
    public static void delete(VisitingCard visitingCard)
    {
        //전체명함의 정보를 통해 정확하게 단 하나의 companyCode와 personalCode를 검색한다.
        String sql = String.format("SELECT Company.companyCode, Personal.code " +
                        "FROM Company INNER JOIN Personal ON " +
                        "Company.companyCode=Personal.companyCode WHERE Company.name='%s' AND" +
                        " Company.address='%s' AND Company.telephoneNumber='%s' AND " +
                        "Company.faxNumber='%s' AND Company.url='%s' AND Personal.name='%s' AND " +
                        "Personal.position = '%s' AND Personal.cellularPhoneNumber='%s' AND " +
                        "Personal.emailAddress='%s';", visitingCard.getCompanyName(),
                visitingCard.getAddress(), visitingCard.getTelephoneNumber(),
                visitingCard.getFaxNumber(), visitingCard.getUrl(), visitingCard.getPersonalName(),
                visitingCard.getPosition(), visitingCard.getCellularPhoneNumber(),
                visitingCard.getEmailAddress());
        try(Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306" +
                "/VisitingCardBinder?serverTimezone=Asia/Seoul", "root", "1q2w3e");
            Statement stmt = con.createStatement();
            ResultSet rs = stmt.executeQuery(sql);)
        {
            //rs의 처음이자 마지막 항목으로 이동하여 데이터가 있으면
            if(rs.next())
            {
                //companyCode를 구한다.
                String companyCode = rs.getString(1);
                //rs에서 personalCode를 구해서 해당개인코드의 개인데이터를 지운다.
                sql = String.format("DELETE FROM Personal WHERE code='%s';",
                        rs.getString(2));
                stmt.executeUpdate(sql);
                //rs에서 구한 companyCode를 통해 회사에 소속된 개인들의 성명을 검색한다.
                sql = String.format("SELECT Personal.name FROM Personal WHERE companyCode='%s';"
                        , companyCode);
                //rs2에는 해당 회사코드에 속하는 개인들의 이름정보가 저장됨
                try(ResultSet rs2 = stmt.executeQuery(sql);)
                {
                    //만약 회사코드에 속하는 개인이 없으면
                    if(rs2.next() == false)
                    {
                        //해당회사코드이 회사데이터를 DB에서 지운다.(반드시 회사에 속하는 개인이 있는지
                        //먼저 체크한 후에 회사에 속하는 개인이 없을 경우 회사데이터를 지운다.)
                        sql = String.format("DELETE FROM Company WHERE companyCode='%s';",
                                companyCode);
                        stmt.executeUpdate(sql);
                    }
                }
            }
        }
        catch (SQLException e)
        {
            System.out.println("[SQL Error : " + e.getMessage() +"]");
        }
    }
}
```

### delete 코드 설명
delete메소드는 명함철에서 takeOut을 통해 반환받은 명함(VisitingCard)의 참조변수를 매개변수로 전달 받는데 이를 통하여 명함철에서 꺼낸 명함의 정보를 알 수 있습니다.<br><br>
매개변수로 입력 받은 명함은 명함철에서는 꺼내어져서 더이상 명함철에는 없는 명함이지만 해당 명함의 개인과 회사 정보는 아직 데이터베이스에 저장되어 있습니다.<br><br>
그래서 데이터베이스에서도 이 명함에 해당하는 개인과 회사의 정보를 지워야 하는데 이를 위해서 매개변수로 입력 받은 명함의 정보를 이용합니다.<br><br>
명함의 개인 정보와 회사 정보 전체를 이용해, 즉, 명함(VisitingCard)의 9개 필드를 이용하여 데이터베이스에서 해당하는 정보의 companyCode와 code를 SELECT(검색)을 합니다.<br><br>
검색 결과는 단 하나의 데이터가 Resultset에 저장될 것입니다.<br><br>
즉, if(rs.next())을 통해 rs의 처음이자 마지막 항목으로 이동하여 데이터가 있는지 확인 후에 선택구조로 들어가서 getString을 통해 companyCode를 구합니다.<br><br>
그리고 getString을 통해 code를 구하고, 이 code의 Personal정보를 지우는 쿼리문을 만들어 데이터베이스에 전달해줍니다.<br><br>
그리고 아까 구한 companyCode를 이용하여 데이터베이스에서 이 companyCode를 가지고 있는 개인 데이터의 name을 검색(SELECT)하는 쿼리문을 실행시켜 ResultSet에 그 결과를 저장합니다.<br><br>
ResultSet에 데이터가 있다면 회사에 소속된 개인이 있다는 뜻이기 때문에 해당 회사 정보를 지우면 안되고, 데이터가 없다면 회사에 소속된 개인이 없다는 뜻이기 때문에 해당 회사의 정보를 지우면 됩니다.<br><br>
이는 앞에서도 언급했듯이 부모는 자식이 없는 경우에만 지울 수 있기 때문입니다.

## replace 코드
```java
public class main
{
     //replace
    public static void replace()
    {
        try (Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306" +
                     "/VisitingCardBinder?serverTimezone=Asia/Seoul", "root", "1q2w3e");
             PreparedStatement pstmt = con.prepareStatement("DELETE FROM Personal;"))
        {
            //Company는 Personal이 먼저 다 지워지기전에는 지울수없음!
            //먼저 DB에 있는 Personal 객체 정보들을 모두 지움.
            pstmt.executeUpdate();
            //이후에 DB에 있는 Company 객체 정보를  모두 지움.
            pstmt.executeUpdate("DELETE FROM Company;");
            //반복을 돌리면서 새로 insert함.
            for (VisitingCard visitingCard : visitingCardBinder.getVisitingCards())
            {
                insert(visitingCard);
            }
        }
        catch (SQLException e)
        {
            System.out.println("[SQL Error : " + e.getMessage() + "]");
        }
    }
}
```

### replace 코드 설명
replace는 명함철의 명함들을 개인의 이름을 기준으로 오름차순으로 정렬한 다음에 데이터베이스도 명함철처럼 개인의 이름을 기준으로 오름차순으로 정렬하기 위해 호출하는 메소드입니다.<br><br>
이를 위해서 먼저 데이터베이스에 있는 자식(개인들)의 정보를 다 지우고, 그 다음 부모(회사)의 정보를 다 지웁니다.<br><br>
이 후 for each 반복문을 통해 명함철에서 이름을 기준으로 오름차순으로 정렬된 명함을 순차적으로 하나씩 데이터베이스에 다시 INSERT해주기 위해 명함을 매개변수로 하는 insert를 호출해줍니다.<br><br>
for each 반복문이 끝나고 나면 데이터베이스에도 명함철과 같은 순서로 개인의 정보와 회사 정보가 저장됩니다.<br><br>

# 마치며
이번 시간에는 명함철 프로그램을 MySQL과 연동시키기 위해 데이터베이스의 관계 설정에 대한 개념을 공부하였고, 이를 통해 특히 외래키(FOREIGN KEY)와 INNER JOIN에 대해서 배울 수 있었습니다.<br><br>
또한 응용프로그래밍을 위해 메소드를 구현할 때 데이터베이스에 접근하여 SELECT한 결과를 바탕으로 다시 로직을 세우는 과정도 거쳤습니다.<br><br>
이를 통해 데이터베이스 사용방법과 개념에 대해서 더 깊게 이해할 수 있었습니다.<br><br>
잘못된 점이나 궁굼한 사항이 있으시면 댓글이나 이메일로 남겨주시면 답장드리겠습니다.<br><br>
긴 글 읽어주셔서 감사합니다^^