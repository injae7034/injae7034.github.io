---
layout: single
title:  "우아한스터디-1주차-스터디-Q&A-왜-엔티티-필드에-id가-존재하는가?-구현체가-하나라도-인터페이스가-필요한가?"
categories: Java
tag: [Java, 우아한스터디, 만들면서 배우는 클린 아키텍쳐, 1장, 2장, 3장]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 만들면서 배우는 클린 아키텍쳐 스터디 1주차 Q&A
# 왜 엔티티 클래스의 필드 멤버에 id가 존재하는가?
1장에서 "계층형 아키텍처는 데이터베이스 설계를 유도한다."고 말하고 있습니다.  

전통적인 계층형 아키텍처의 토대는 데이터베이스이기 때문에 도메인 계층이 영속성 계층(데이터베이스)에 의존하게 된다는 말이었습니다.  

그러나 육각형 아키텍처를 이용하면 도메인 중심의 설계를 할 수 있다고 책에서 말하고 있습니다.  

제가 웹 강의나 자료를 볼 때 코드에서 항상 엔티티 클래스가 필드멤버로 id를 가지는 것을 봤습니다.  

도메인 주도 설계를 하는데 id를 왜 필드로 가지는지 이해할 수 없었습니다.  

어차피 엔티트 클래스의 객체는 모든 내용이 같더라도 참조값이 서로 다르면 차이를 구분할 수 있는데 굳이 멤버에 왜 id가 필요한 것인지를 이해할 수 없었습니다.  

그리고 더 나아가 이것이야말로 도메인 설계를 할 때 영속성 계층에 영향을 받은 것이 아닌가하는 생각이 들었습니다.  

즉, 도메인 주도 설계를 하는데 이 때부터 이미 데이터베이스를 사용한다는 것을 가정하고, 필드멤버에 id를 두는 것이기 때문에 완전한 의미의 도메인 주도 설계라고 할 수 없고, 영속성 계층, 데이터베이스에 종속되는 설계라고 생각했습니다.  

그래서 이것에 대하 스터디 시간에 물어봤습니다.  

그러자 실무자분들의 많은 대답을 들을 수 있었는데 지금 기억에 남는 것은 실무에서 데이터베이스에서 원하는 데이터를 가져올 때 객체에 id가 없다면 유일함을 보장할 수 없기 때문에 모든 필드 멤버를 다 비교헤서 유일한 것을 찾아야 하기 때문에 필드가 3~4개가 아니라 엄청나게 많다면 이것을 다 비교해야하는 것이 첫 번째 문제이며, 두번째로는 그렇게 모든 필드를 다 비교한다고해서 반드시 유일하다는 것을 보장할 수 있는지도 문제라고 말해주셨습니다.  

그래서 이로 인해 비용이 상승하기 때문에 엔티티 클래스의 필드에 id를 두는 것이 비용적인 면이나 효율적인 면에서 좋은 선택이라고 말해주셨습니다.  

그러고 나서 예전에 제가 구현했던 명함 프로그램을 봤는데 명함철에서 명함을 지울 때는 명함의 주소값으로 손쉽게 바로 지울 수 있었지만(명함의 주소값을 유일하기 때문에) 데이터베이스에 저장된 명함을 지우기 위해서는 필드값이 9개인데 이 모든 것을 비교하여 찾아서 데이터베이스에서 해당 명함을 지웠습니다.  

왜냐하면 제가 설계한 명함 엔티티 클래스에는 id가 없었기 때문에 프로그램 상에서는 주소로 유일함을 구분할 수 있지만 데이터베이스에서는 그것이 불가능하여 모든 필드(9개)를 다 비교하여 해당하는 명함을 찾은 다음 지웠습니다.  

## 명함철에서 명함을 지우는 코드
명함의 참조값을 이용해 명함철에서 해당 명함을 바로 찾아낼 수 있음.
```java
    public VisitingCard takeOut(VisitingCard visitingCard)
    {
        //명함철에서 꺼내려고 하는 vistingCard 위치구하기
        int index = this.visitingCards.indexOf(visitingCard);
        //명함철에서 꺼내려고 하는 카드가 처음이 아니면
        if(index > 0)
        {
            //현재 카드 위치를 이전 위치로 정한다.
            this.current = this.visitingCards.get(index - 1);
        }
        //명함철에서 꺼내려는 카드가 처음 명함이면
        else
        {
            //명함철 개수가 1개 이상이면(현재 카드를 빼도 명함철에 카드가 남아있으면)
            if(this.visitingCards.size() > 1)
            {
                //다음 카드 위치를 현재 카드위치로 정한다.
                this.current = this.visitingCards.get(index + 1);
            }
            //명함철에 현재 명함이 1개라서 이 카드를 빼면 더이상 명함이 없을 경우
            else
            {
                this.current = null;
            }
        }
        //해당 명함을 명함철에서 꺼낸다.
        boolean flag = this.visitingCards.remove(visitingCard);
        //개수를 감소시킨다.
        this.length--;
        if(flag == true)
        {
            return visitingCard;
        }
        else
        {
            return null;
        }
    }
```

## 데이터베이스에서에서 해당하는 명함을 지우는 코드
```java
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
```
위의 takeOut메소드 호출 이후에(명함철에서 해당 명함을 먼저 지운 후에) 데이터베이스에서도 해당 명함의 정보를 지우기 위해 delete메소드를 호출합니다.  

명함(VisitingCard)은 회사(Company)와 개인(Personal) 클래스의 객체를 필로 가지고, 회사는 5개의 필드(id는 없음), 개인은 4개의 필드(id는 없음)를 가집니다.  

필드에는 id가 없기 때문에 해당 명함을 데이터베이스에 저장할 때는 id를 만드는 별도의 코드(getCompanyCode, getPersonalCode 등)를 통해 테이블에는 id(Primary key)와 함께 저장됩니다.  

delete 메소드의 매개변수로 입력받은 명함에는 id를 알아낼 수 없기 때문에(필드에 id가 없어서) 9개의 필드를 이용하여 데이터베이스에서 companyCode와 personalCode를 알아내고, 이를 통해 데이터베이스에서 개인데이터를 먼저 지우고, 회사에 속한 개인이 더 이상 없을 경우 회사 데이터도 지웁니다.  

결국 엔티티 클래스의 필드에 id값이 없기 때문에 간단한 토이프로젝트에서도 데이터베이스의 코드를 구하기 위해 9개의 필드가 필요한데 복잡한 실무에서는 이보다 더한 상황이 발생할 수 있고, 심지어 필드를 다 이용하더라도 유일함을 보장할 수 없을수도 있습니다.  

그래서 이러한 경우를 방지하기 위해 엔티티 클래스의 필드에 id를 두는 것이 데이터베이스와 함께 프로그램을 유지보수 관리하기 용이합니다.  

# 구현체가 하나라도 인터페이스가 필요한가?
책에서 포트와 어댑터 아키텍처(육각형 아키텍처)를 구현하는 예시에서 port의 in 패키지에 SendMoneyUseCase 인터페이스가 위치하고, application 패키지에서 이를 구현하는 SendMoneyService 클래스가 위치해 있습니다.  

즉, 인터페이스를 구현하는 구현체가 하나이고, 더이상의 구현체가 필요하지 않을 경우에는 굳이 이 아키텍처 구조를 형식상으로라도 지키기 위해서 인터페이스를 두고, 한 개의 구현체를 구현하는 것이 나은지 아니면 거추장스러운 형식에서 벗어나서 실용적으로 인터페이스를 두지 않고 클래스만 두어서 바로 이용하는 것이 나은지를 물어봤습니다.  

만약에 구현체가 하나 밖에 없는데 인터페이스를 두게 되면 개발자 입장에서는 어쨌든 컨트롤러든 웹에서 이 인터페이스 코드를 봤을 때 이것의 구현 코드를 한번 더 찾아야 하는 수고로움이 있습니다.  

하지만 인터페이스 대신 바로 구현하는 클래스가 있으면 이러한 수고로움은 줄어들 것입니다.  

하지만 이렇게 되면 결국에는 웹 계층에서 서비스 코드를 호출하게 됨으로써 포트와 어댑터 아키텍처가 무너지게 됩니다.  

이 하나가 결국에는 다른 코드에서도 편하게 구현하자는 생각을 가지게 되어 나중에 시간이 흐르면 이런 코드들이 쌓여서 육각형 아키텍처와는 거리가 먼 구조를 가지게 됩니다.  

그래서 간단한 프로젝트가 아니라면 이러한 상황에서도 포트와 어탭터 아키텍처 형식를 유지하기 위해 구현체가 하나더라도 엄격하게 인터페이스를 둬서 코드를 작성해야 합니다.  

그리고 만약에 웹 계층에서 인터페이스(포트) 대신에 서비스 구현 코드(비지니스 로직)가 바로 들어간다면 이는 나중에 테스트할 때도 굉장히 번거로워 지기 때문에 자칫하면 테스트를 건너뛰게 되는 상황을 만들 수 있습니다.  

실무에서는 책의 예제와는 다르게 훨씬 더 구조가 복잡하고 유기적으로 연관된 상황이 많기 때문에 이렇게 단순한 경우는 없을 뿐더러 이것 하나만 생각해서는 나중에 다른 팀의 코드나 다른 개발자의 코드와 연동이 될 때 문제가 발생할 수 있기 때문에 단순한 경우더라도 쉽게 지름길을 가려고 하지 말고, 엄격하게 아키텍처 형식을 유지하는 것이 좋다고 생각합니다.

이상으로 제가 질문한 2가지에 대한 Q&A를 마칩니다.  

이 답변은 스터디시간에 실무자분들이 이야기해준 것을 그대로 적는 것이 아니라 제가 저 나름대로 그분들의 말씀을 이해하기 위해 제 생각을 덧붙였기 때문에 정확히 그 분들의 의견이라고 말하기는 어렵습니다.  

말씀하셨던 것 중에 모르는 내용은 건너뛰고, 최대한 아는 내용 중에서 더 깊게 이해하기 위해 제 배경지식을 같이 적었습니다.  

긴 글을 읽어주셔서 감사합니다^^
