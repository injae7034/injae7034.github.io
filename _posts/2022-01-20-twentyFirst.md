---
layout: single
title:  "Java프로젝트하면서 오버로딩 그리고 다형성, 오버라이딩을 적용하여 코드 효율성 올리기"
categories: Java
tag: [Java, 프로젝트, 가계부, AccountBook, 오버로딩, 오버라이딩, 다형성, 추상클래스, 추상메소드, 상속]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 시작하며
저번 시간에 가계부(AccountBook)에서 사용할 엔티티 클래스인 Account, Income과 Outgo에 대해서 정의했습니다.<br>
<a href="https://injae7034.github.io/java/twentieth/"
target="_blank">Account, Income과 Outgo클래스 만들기</a>

<br><br>
Account는 추상클래스이자 부모클래스이고, Income과 Outgo는 Account를 상속 받아 구현하는 자식클래스입니다.<br><br>
Account는 추상메소드를 가지고 있기 떄문에 이를 상속받는 자식들은 반드시 이 추상메소드를 구현해야 합니다.<br><br>

Account의 추상메소드인 setAmount와 clone을 설명하면서 이들은 추상메소드로 만들지 말고, Income과 Outgo의 메소드로 각각 정의하면 되는데 **왜 굳이 추상메소드로 설정하여 Account를 추상클래스로 만드는지에 대해서 이번 시간에 AccountBook클래스를 구현하면서 자세히 알아보도록 하겠습니다.**<br><br>
또한 **오버로딩과 다형성, 오버라이딩을 적용하면 어떻게 코드를 효율적으로 짤 수 있는지**도 알아보도록 하겠습니다.

# AccountBook 클래스
엔티티클래스인 Account, Income, Outgo를 관리할 컨트롤(파일) 클래스가 바로 AccountBook입니다.<br><br>
AccountBook은 가계부를 의미하는데 가계부는 보통 수입항목과 지출항목이 있을 것이고, 이들을 순차적으로 저장하고 보여주는 테이블 형식일 것입니다.<br><br>
그렇기 때문에 AccountBook에서 자료구조는 배열이 적합합니다.<br><br>
그래서 AccountBook은 ArrayList를 사용하여 수입항목과 지출항목들을 관리하려고 합니다.<br><br>
근데 이 때 벌써부터 문제가 생깁니다...<br><br>
그럼 AccountBook클래스는 수입항목과 지출항목 2개로 이루어지니까 **ArrayList\<Income\>, ArrayList\<Outgo\>** 이렇게 2개의 ArrayList가 필요할까요?<br><br>
만약에 **다형성**이 적용되지 않는다면 Income은 ArrayList\<Income\>에서 저장 및 관리하고, Outgo는 ArrayList\<Outgo\>에서 저장 및 관리해야 합니다.<br><br>
Income과 Outgo의 각 필드멤버가 앞에서 말했듯이 다 똑같은데 굳이 이렇게 비효율적으로 2개로 나눠서 관리할 필요가 있을까요?<br><br>
그래서 등장한 것이 부모 클래스인 Account입니다.
# 다형성을 적용하여 Account 객체 생성하기
Account 객체를 생성하는 방법은 2가지인데요...
첫번째는 다음과 같습니다.
```java
Account account = new Account();
```
Account 자신의 생성자로 자신을 생성하는 이때까지 해오던 가장 쉬운 방법입니다;;<br><br>
(물론 지금 제가 구현하고 있는 가계부 프로그램에서 Account는 추상클래스이기 때문에 자기 자신의 객체를 생성할 수 없어서 위의 방법은 사용할 수 없습니다.<br><br>
Account가 추상클래스가 아니고 일반클래스라면 위처럼 자기자신의 생성자로 객체생성이 가능합니다.)<br><br>
두번째 방법은 드디어 **다형성**을 적용한 방법입니다.
```java
Account account = new Income();
```
부모클래스인 Account를 자식클래스인 Income의 생성자로 생성할 수 있다는 말이죠.<br><br>
이는 부모클래스(Account)가 추상클래스라서 본인(Account)의 생성자로 본인의 객체를 생성할 수 없어도, 자식(Income)의 생성자를 이용하면 본인(Account)의 객체를 생성할 수 있습니다.<br><br>
즉, 부모클래스인 Account가 일반클래스이든 추상클래스이든 상관없이 자식클래스의 Income 생성자를 이용하여 부모클래스인 Account의 객체를 생성할 수 있습니다.<br><br>
이는 또다른 자식인(Account를 상속받는) Outgo에도 마찬가지로 적용됩니다.
```java
Account account = new Outgo();
```
위와 같이 Outgo의 생성자로도 Account를 생성할 수 있습니다.<br><br>
이뿐만 아니라 다음과 같은 표현도 가능합니다.
```java
Income income = new Income();
Account account = income;
Outgo outgo = new Outgo();
account = outgo;
```
즉, 부모인 Account 객체의 참조변수값에 이미 생성된 자식클래스의 참조변수값을 저장할 수 있습니다. 

# 다형성을 적용하여 ArrayList\<Account\> 하나만 사용하기
위에서 말했듯이 다형성을 적용하면 Account객체를 Income이나 Outgo의 생성자로 생성할 수 있습니다.<br><br>
그럼 Income으로 생성한 Account객체와 Outgo로 생성한 Account객체는 완전히 같을까요?<br><br>
결과부터 말씀드리면 Income으로 생성한 Account객체와 Outgo로 생성한 Account객체는 서로 다릅니다.<br><br>
Income으로 생성한 Account객체는 Income의 특성을 가지고 있기 때문에 Income으로는 다운캐스팅이 되지만 Outgo로는 다운캐스팅이 되지 않습니다.<br><br>
반대로 Outgo로 생성한 Account객체 역시 마찬가지구요.<br><br>
저번 시간에 Account클래스에서 추상메소드인 setAmount와 clone을 선언하였고, 이 추상클래스들을 자식클래스인 Income과 Outgo에서 각각 자신의 클래스의 특성에 맞게 구현하도록 했습니다.<br><br>
그럼 이제 Income 생성자로 생성한 Account 객체에서 setAmount를 호출하면 Income에서 오버라이딩한 setAmount가 호출됩니다.<br><br>
Outgo 생성자로 생성한 Account 객체에서 setAmount를 호출하면 Outgo에서 오버라이딩한 setAmount가 호출됩니다.<br><br>
즉, 자식클래스의 생성자로 Account 객체를 생성하면 Income이나 Outgo 클래스를 따로 관리할 필요없이 Account 하나로 통일하여 관리하면서도 Income이나 Outgo 클래스의 특성을 살릴 수 있다는 말입니다.<br><br>
원래는 ArrayList\<Income\>에는 Income클래스만 저장하고, ArrayList\<Outgo\>에는 Outgo클래스만 저장하였지만

**다형성을 적용**하여 **ArrayList\<Account\>**에 Income클래스나 Outgo클래스 둘다 **구분없이 저장**할 수 있게 되었습니다.

# 다형성 적용시 주의할 점
여기서 주의할 점은 **자식클래스로 생성한 Account객체** 또는 **자식클래스의 참조변수값을 저장한 Account객체**는 **Account 자신이 가진 메소드만 호출할 수 있다**는 것입니다.<br><br>
즉, setAmount나 clone은 추상메소드라도 자신이 가지고 있기 때문에 호출하여 오버라이딩된 자식클래스의 메소드가 호출되는 것입니다.<br><br>
만약에 Account에는 정의 또는 선언되어 있지 않고, Income에만 정의되어 있는 메소드가 있을 경우, Income의 생성자로 생성한 Account객체에서는 이 Income에만 있는 메소드를 호출할 수 없습니다.<br><br>
또한 Income생성자로 생성한 Income의 객체의 참조변수값을 Account객체의 참조변수값에 대입한 경우 역시 마찬가지로 Income에만 정의된 메소드를 호출할 수 없습니다.<br><br>
그래서 추상클래스를 사용합니다.<br><br>
내용이 없는 setAmount와 clone을 부모클래스인 Account에서 추상클래스로 선언이 되어 있으면 Income생성자로 생성된 Account나 Income의 참조변수값을 저장한 Account에서 Income에서 오버라이딩된 내용의 setAmount와 clone을 호출할 수 있습니다.

## AccountBook 클래스 필드멤버
위의 다형성을 적용하면 AccountBook클래스는 ArrayList\<Account\> accounts와 그 사용량(저장된 Account객체 개수)을 나타내는 length 이렇게 2개가 있습니다.
```java
public class AccountBook
{
    //인스턴스 필드 멤버
    private ArrayList<Account> accounts;
    private int length;
}
```

## 생성자
디폴트 생성자 하나만 구현했습니다.
```java
public class AccountBook
{
    //디폴트생성자
    public AccountBook()
    {
        this.accounts = new ArrayList<Account>();
        this.length = 0;
    }
}
```
ArrayList\<Account\>를 생성하여 그 주소를 저장하고, 당연히 저장된 것이 없기 떄문에 길이(사용량)는 0으로 초기화해줍니다.

# 도메인 용어 정리
**추상클래스와 추상메소드, 다형성, 오버라이딩**에 대해 설명하다 보니 도메인 용어 정리를 못했네요...;;<br><br>
일단 AccountBook에서는 항목을 추가할 때는 앞에서 주소록 프로그램과 똑같이 가계부에 기재한다는 용어를 사용하여 **기재하기(record)**를 사용합니다.<br><br>
기재한 항목을 배열 인덱스로 구하는 **getAt**도 정의하였습니다.<br><br>
항목을 찾을 때는 **찾기(find)**, 항목을 수정할 때는 **수정하기(correct)**를 사용합니다.<br><br>
수정하기(correct)에서 **수정할 수 있는 내용은 금액(amount)과 비고(remarks)**로 정하였습니다.<br><br>
나머지 멤버인 일자(date), 적요(briefs)는 수정하지 못하도록 했습니다.<br><br>
그 이유는 개인에 따라 다르지만 일자와 적요까지 다 수정을 할 수 있다면 장부조작을 쉽게 할 수 있다고 생각하여 금액과 비고만 수정할 수 있도록 하였습니다.<br><br>
**잔액(balance)은 금액(amount)에 종속적인 관계**이기 때문에 금액이 바뀌면 자동으로 잔액(balance)도 바뀝니다.<br><br>
지우는 기능은 없습니다.<br><br>
가계부에서 이미 기재한 내용을 나중에 지운다는 것은 개인적으로 장부조작과 같은 개념이라고 생각해서 지우는 기능은 과감히 없앴습니다.<br><br>
정렬하기 기능도 없는데 가계부는 일자대로 순차적으로 보여주는 것이 맞기 때문에 이미 정렬이 된 상태이기 때문에 따로 다른 기준으로 정렬할 필요가 없다고 생각했습니다.<br><br>
주소록과 비교해서 추가된 도메인 용어는 **계산하기(calculate)**입니다.<br><br>
주소록은 저장된 데이터로 어떠한 연산을 할 필요가 없이 데이터를 잘 저장하고, 검색하고, 편하게 볼 수 있으면 됩니다.<br><br>
하지만 가계부는 주소록과 달리 여기서 하나의 기능이 추가되야 합니다.<br><br>
즉, 사용자가 가계부를 사용하는 결정적인 이유는 저장된 데이터로 연산처리를 해서 유의미한 결과를 알 수 있기 때문이죠.<br><br>
그래서 이 연산처리를 해주는 메소드가 바로 계산하기(calculate)입니다.<br><br>
계산하기를 통해 **일정 기간동안의 총수입, 총지출, 총잔액**을 알 수 있도록 구현할 것입니다.

## record 메소드
```java
public class AccountBook
{
    //record
    public int record(Date date, String briefs, int amount, String remarks)
    {
        //accountBook에 이전에 기입된 account가 있으면
        Account account = null;
        int balance = 0;
        if(this.length > 0)
        {
            //accountBook에서 가장 최근에 기입된 account를 구한다.
            account = this.accounts.get(this.length - 1);
            //balance(잔액)을 구한다.
            balance = account.getBalance();
        }
        //새로운 balance를 구한다.(누적)
        balance += amount;
        //금액(amount)이 양수이면
        if(amount >= 0)
        {
            account = new Income(date, briefs, amount, balance, remarks);
        }
        //금액(amount)이 음수이면
        else
        {
            account = new Outgo(date, briefs, amount * (-1), balance, remarks);
        }
        //ArrayList에 새로 생성한 account를 추가한다.
        int index = -1;
        boolean doesRecordingSucceed = this.accounts.add(account);
        //가계부(AccountBook)의 account개수를 증가시킨다.
        this.length++;
        //성공적으로 ArrayList에 account가 추가되었으면
        if(doesRecordingSucceed == true)
        {
            //새로 추가된 account의 위치를 구한다.
            index = this.accounts.indexOf(account);
        }
        //위치를 출력한다.
        return index;
    }
}
```

### record 메소드 설명
AccountBook의 인스턴스 필드멤버인 length를 통해 저장된 account항목이 있는지 확인합니다.<br><br>
length가 0보다 크면 저장된 account항목이 있다는 뜻이고 그게 아니면 저장된 account 항목이 없다는 뜻입니다.<br><br>
저장된 account 항목이 있다면, 즉 length가 0보다 크면 가장 최근에 기재된 account 항목을 구합니다.<br><br>
배열은 0부터 시작하기 때문에 사용량(length)에서 -1을 해준 값을 ArrayList의 get메소드에 매개변수로 전달하면 가장 마지막 account항목을 반환합니다.<br><br>
가장 마지막 account 항목에 가장 최신으로 반영된 잔액(balance)이 저장되어 있기 때문에 이를 balance 변수에 대입합니다.<br><br>
만약에 저장된 account 항목이 없다면 balance 변수는 디폴트값인 0이 저장되어 있을 겁니다.<br><br>
이 후 이 balance 변수에 record메소드에서 매개변수로 전달 받은 amount를 더해줍니다.<br><br>
즉, 가장 최근에 반영된 balance에 amount를 더해줘서 그 값을 누적시킵니다.<br><br>
amount는 int형으로 나중에 record 메소드를 호출하면서 매개변수로 입력할 때 수입이면 +로 입력을 받도록 할 것이고, 지출이면 -로 입력되도록 설정할 것입니다.<br><br>
이러한 가정하에서 **다형성**을 적용하여 **금액(amount)이 양수**이면 **Income 객체를 생성**하여 **Account 객체에 참조변수값을 저장**하고, **금액(amount)이 음수**이면 **Outgo 객체를 생성**하여 **Account 객체에 참조변수값을 저장**합니다.<br><br>
그리고 ArrayList\<Account\>에 Account 객체의 참조변수값을 add시켜줍니다.<br><br>
add가 성공적으로 되었다면 true가 반환될 것이고, 이 account객체의 참조변수값을 indexof의 매개변수로 전달하여 그 배열첨자 위치값을 구한 다음 이를 반환합니다.

## getAt 메소드
```java
public class AccountBook
{
    //getAt
    public Account getAt(int index) {return this.accounts.get(index);}
}
```
getAt은 접근하고자 하는 배열첨자 위치(index)를 매개변수로 입력 받아서 내부에서 index를 매개변수로 전달하면서  ArrayList\<Account\>의 get메소드를 호출하면서 반환합니다.

## getLength 메소드
```java
public class AccountBook
{
    //getLength
    public int getLength() {return this.length;}
}
```
AccountBook의 필드멤버인 lenth(가계부에서 account항목의 개수)가 private이기 때문에 외부에서 그 값을 알아내기 위해 접근을 할 수 없습니다.<br><br>
그래서 외부에서 그 값을 알아내기 위해 getter메소드를 구현해줍니다.<br><br>
setter는 따로 해줄 필요가 없는데 그 이유는 record메소드에서 account 객체를 추가할 때 length를 증가시켜주도록 처리하고 있기 때문입니다.<br><br>
record메소드 내부에서는 AccountBook 클래스 내부 접근이기 때문에 setter메소드 필요없이 바로 접근하여 값을 변경해줄 수 있습니다.<br><br>
또한 length 값을 변경시키는 유일한 행위는 record 밖에 없기 때문에 다른 상황은 신경쓸 필요가 없습니다.<br><br>
(지우기 기능이 없고, 기재하기 기능만 구현했기 때문에...)

## printAllAccounts 메소드
```java
public class AccountBook
{
    //printAllAccounts
    public int printAllAccounts()
    {
        for(Account account : this.accounts)
        {
            System.out.println(account);
        }
        return this.length;
    }

}
```
나중에 콘솔창에서 테스트를 할 때 가계부(AccountBook)에 있는 모든 Account 항목들을 출력해야 하는 일이 있을텐데 그 때마다 코드 중복이 심할 거 같아서 미리 가계부의 전체 Account 항목을 출력하는 메소드를 구현합니다.<br><br>
for each 반복문을 돌려서 내부에서 println메소드에 account객체를 출력하도록 했습니다.<br><br>
println에서 객체의 참조변수값만 매개변수로 전달할 경우 자동으로 뒤에 .toString이 붙게 되는데 저번 시간에 Account 클래스를 구현하면서 Object의 toString 메소드를 Account 클래스의 특성에 맞게 오버라이딩하였습니다.<br>
<a href="https://injae7034.github.io/java/twentieth/#tostring-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9"
target="_blank">Account 클래스에서 Object의 toString 메소드 오버라이딩</a>

<br><br>

그래서 **System.out.println(account);** 을 호출하면 **System.out.println(account.toString());**이 될 것이고, Account 클래스에서 오버라이딩된 toString이 콘솔창에 출력될 것입니다.

## find 메소드 오버로딩
가계부에서 검색조건을 여러 개 설정하였습니다.<br><br>
이 **검색 조건에 따라서 매개변수의 자료형이나 그 개수가 달라지기 때문에 find 메소드에 오버로딩을 적용**하였습니다.<br><br>
find 메소드는 총 5개를 오버로딩하였는데, 첫번째는 일자(date)로 찾기, 두번째는 적요(briefs)로 찾기, 세번째는 일자(date)와 적요(briefs)로 찾기, 네번째는 기간(date, date)으로 찾기, 다섯번째는 기간(date, date)과 적요(briefs)로 찾기입니다.<br><br>

### find(date)
```java
public class AccountBook
{
    //Find(date)
    public ArrayList<Integer> find(Date date)
    {
        //date로 찾은 Account 객체들의 위치를 저장할 위치ArrayList 생성하기
        ArrayList<Integer> indexes = new ArrayList<>();
        //가계부(AccountBook)의 처음부터 끝까지 반복한다.
        for(int i = 0; i < this.length; i++)
        {
            //해당 위치의 Account 객체의 date가 찾고자 하는 date와 같으면
            if(date.equals(this.accounts.get(i).getDate()) == true)
            {
                //위치를 저장한다.
                indexes.add(i);
            }
        }
        //위치배열을 출력한다.
        return indexes;
    }
}
```
매개변수로 Date클래스의 객체(일자)를 입력받습니다.<br><br>
메소드 내부에서 같은 Date(일자)를 가진 Account 객체의 배열첨자의 위치들을 저장할 ArrayList\<Integer\>를 생성합니다.<br><br>
그리고 가계부(AccountBook)의 저장된 account 항목의 처음부터 끝까지 반복을 합니다.<br><br>
for 반목문 내에서 매개변수로 입력받은 일자(date)와 account 항목의 일자(date)가 서로 같은지 비교합니다.<br><br>
그러기 위해서 우선 ArrayList\<Account\>의 get메소드를 통해 account 객체를 구한 다음 getDate를 통해 Account클래의 필드멤버인 date값을 구합니다.<br><br>
date끼리 같은지 비교를 하기 위해서 예전에 Date클래스를 구현할 때 Object의 equals를 오버라이딩하였기 때문에 이 equals를 호출하여 동등비교를 합니다.<br>
<a href="https://injae7034.github.io/java/nineteenth/#equals-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9"
target="_blank">Java의 LocalDate를 활용해서 나만의 Date클래스 만들기 - equals 메소드 오버라이딩</a>

<br><br>
equals의 결과 true이면 같은 일자(date)를 가지고 있기 때문에 배열첨자위치를 저장하는 ArrayList\<Integer\>의 indexes에 그 위치를 add시킵니다.<br><br>
이렇게 for반복을 돌리면서 같은 일자(date)를 찾아 그 배열첨자 위치를 저장한 다음에 ArrayList\<Integer\> indexes를 반환합니다.

### find(briefs)
```java
public class AccountBook
{
    //find(briefs)
    public ArrayList<Integer> find(String briefs)
    {
        //briefs로 찾은 Account 객체들의 위치를 저장할 위치ArrayList 생성하기
        ArrayList<Integer> indexes = new ArrayList<>();
        //가계부(AccountBook)의 처음부터 끝까지 반복한다.
        for(int i = 0; i < this.length; i++)
        {
            //해당 위치의 Account 객체의 briefs가 찾고자 하는 briefs와 같으면
            if(briefs.compareTo(this.accounts.get(i).getBriefs()) == 0)
            {
                //위치를 저장한다.
                indexes.add(i);
            }
        }
        //위치배열을 출력한다.
        return indexes;
    }
}
```
매개변수로 String 클래스인 적요(briefs)를 입력받습니다.<br><br>
메소드 내부에서 같은 briefs(적요)를 가진 Account 객체의 배열첨자의 위치들을 저장할 ArrayList\<Integer\>를 생성합니다.<br><br>
그리고 가계부(AccountBook)의 저장된 account 항목의 처음부터 끝까지 반복을 합니다.<br><br>
for 반목문 내에서 매개변수로 입력받은 briefs(적요)와 account 항목의 briefs(적요)가 서로 같은지 비교합니다.<br><br>
그러기 위해서 우선 ArrayList\<Account\>의 get메소드를 통해 account 객체를 구한 다음 getBriefs를 통해 Account클래의 필드멤버인 briefs값을 구합니다.<br><br>
breifs는 String 클래스이기 때문에 String 클래스에서 대소동등비교를 하기 위해 이미 구현해놓은 compareTo 메소드를 활용합니다.<br><br>
compareTo는 매개변수로 입력 받은 String보다 compareTo 메소드를 호출한 String이 더 크면 1이 반환되고, 같으면 0이 반환되고, 작으면 -1이 반환됩니다.<br><br>
저희는 동등비교를 해야하기 때문에 같은지, 즉 그 값이 0인지를 선택구조로 물어보고, 값이 0이면(briefs가 서로 같으면) 배열첨자위치를 저장하는 ArrayList\<Integer\>의 indexes에 그 위치를 add시킵니다.<br><br>
이렇게 for반복을 돌리면서 같은 적요(briefs)를 찾아 그 배열첨자 위치를 저장한 다음에 ArrayList\<Integer\> indexes를 반환합니다.

### find(date, briefs)
```java
public class AccountBook
{
    //find(date, briefs)
    public ArrayList<Integer> find(Date date, String briefs)
    {
        //같은 date를 가진 Account 객체들의 위치배열을 받환받는다.
        ArrayList<Integer> sameDates = this.find(date);
        //같은 date를 가진 Account 객체들 중에서 같은 briefs를 가진
        //Account 객체들의 위치를 저장할 위치ArrayList 생성하기
        ArrayList<Integer> indexes = new ArrayList<>();
        //같은 date를 가진 Account 객체들의 위치배열 처음부터 끝까지 반복한다.
        for(int i = 0; i < sameDates.size(); i++)
        {
            //가계부에서 같은 date를 가진 Account 객체의 briefs가 찾고자 하는 briefs와 같으면
            if(briefs.compareTo(this.accounts.get(sameDates.get(i)).getBriefs()) == 0)
            {
                //위치를 저장한다.
                indexes.add(sameDates.get(i));
            }
        }
        //같은 date를 가진 Account 객체들 중에서 같은 briefs를 가진
        //Account 객체들의 위치배열을 반환한다.
        return indexes;
    }
}
```
매개변수로 일자(date)와 적요(briefs)를 받습니다.<br><br>
우선 앞에서 구현한 find(date) 메소드를 호출하여 가계부(AccountBook)에서 같은 일자(date)를 가진 Account 객체들의 배열첨자 위치를 저장한 ArrayList\<Integer\> sameDates를 반환받습니다.<br><br>
이 sameDates는 ArrayList\<Account\>에서 매개변수로 입력 받은 date와 같은 date를 가진 Account 객체들의 배열첨자 위치 값을 배열 요소로 하고 있습니다.<br><br>
그리고 이제 같은 date를 가진 Account 객체들 중에서 같은 briefs를 가진 Account 객체들의 배열첨자 위치를 저장할 ArrayList\<Integer\> indexes를 생성합니다.<br><br>
이제 for 반복문을 돌리는데 이 때 주의할 점은 AccountBook의 length만큼(가계부에서 account의 개수만큼) 반복을 돌리는 것이 아니라는 점입니다.<br><br>
저희는 이미 find(date)를 통해서 AccountBook에서 같은 date를 가진 Account 객체들의 배열첨자 위치와 그 개수를 알고 있습니다.<br><br>
즉, 저희가 찾고자 하는 것은 같은 일자를 가진 account 항목들 중에서 적요(briefs)도 같은 account 항목들을 찾는 것입니다.<br><br>
그래서 여기서는 반복문의 조건으로 가계부(AccountBook)에서 같은 일자(date)를 가진 Account 객체들의 배열첨자 위치를 저장한 ArrayList\<Integer\> sameDates의 size만큼 반복을 돌립니다.<br><br>
for 반복문 내부에서는 같은 briefs를 가진 account항목을 찾기 위해 compareTo를 이용합니다.<br><br>
그래서 같은 briefs를 가졌으면 그 배열첨자 위치를 ArrayList\<Integer\> indexes에 저장하고, 반복이 끝나면 indexes를 반환합니다.<br><br>
이 indexes에는 같은 date와 같은 briefs를 가진 account 객체들의 배열첨자 위치 값이 저장되어 있을 겁니다.

### find(date, date)
```java
public class AccountBook
{
    //find(date, date)
    public ArrayList<Integer> find(Date fromDate, Date toDate)
    {
        //Account 객체들 중에서 fromDate부터 toDate까지
        //Account 객체들의 위치를 저장할 위치ArrayList 생성하기
        ArrayList<Integer> indexes = new ArrayList<>();
        //같은 date들의 위치를 저장할 임시공간인 ArrayList 생성하기
        ArrayList<Integer> sameDates = null;
        //fromDate가 toDate보다 작거나 같은동안 반복한다.
        while(fromDate.isGreaterThan(toDate) == false)
        {
            //가계부(AccountBook)에서 fromDate와 같은 Account객체들의
            //위치를 찾아서 그 위치를 저장한 배열을 반환한다.
            sameDates = this.find(fromDate);
            //같은 dates의 위치를 저장한 배열의 처음부터 마지막까지 반복한다.
            for(int i = 0; i < sameDates.size(); i++)
            {
                //같은 dates의 위치를 저장한다.
                indexes.add(sameDates.get(i));
            }
            //fromDate를 1일 증가시켜준다.
            fromDate = Date.nextDate(fromDate, 1);
        }
        return indexes;
    }
}
```
매개변수로 일자(date)와 일자(date)를 입력받습니다.<br><br>
즉 일자부터 일자까지 기간을 검색합니다.<br><br>
예를 들어 new Date("20220101"), new Date("20220120")을 매개변수로 입력 받으면 2022년 1월 1일부터 2022년 1월 20일까지의 date를 가진 account 항목들의 위치를 검색해주는 기능을 수행합니다.<br><br>
메소드 내부에서 먼저 기간동안의 account 객체들의 배열첨자 위치를 저장할 ArrayList\<Integer\> indexes를 생성합니다.<br><br>
그리고 같은 일자(date)들의 위치를 저장할 임시공간인 ArrayList\<Integer\> sameDates를 null로 초기화합니다.<br><br>
이 후 while 반복문을 돌리는데 이 때 조건이 fromDate가 toDate보다 작거나 같은동안 반복합니다.<br><br>
이를 위해서 Date클래스에서 미리 구현해놓은 isGreaterThan메소드를 호출합니다.<br>
<a href="https://injae7034.github.io/java/nineteenth/#isgreaterthan-%EB%A9%94%EC%86%8C%EB%93%9C"
target="_blank">Java의 LocalDate를 활용해서 나만의 Date클래스 만들기 - isGreaterThan 메소드</a>

<br><br>
그 값이 false인동안은 fromDate가 toDate보다 작거나 같다는 뜻이기 때문에 계속 반복을 돌립니다.<br><br>
그리고 while반복문 내에서 find(fromDate)메소드를 호출하여 fromDate와 같은 일자를 가진 Account 객체들의 배열첨자 위치를 저장한 sameDates를 반환받습니다.<br><br>
이 sameDates의 처음배열요소부터 마지막배열요소까지, 즉, size만큼 for반복을 돌리면서 배열첨자 위치(sameDates.get(i))를 indexes에 저장합니다.<br><br>
여기서 i가 아니라 sameDates.get(i)를 add시켜주는 이유는 i는 sameDates의 배열첨자위치이기 때문에 sameDates가 할당해제되면 아무 의미가 없는 것입니다.<br><br>
sameDates의 배열첨자가 중요한 것이 아니라 sameDates에 저장된 배열요소인 AccountBook의 ArrayList\<Account\>에서 같은 일자를 가진 account 객체들의 배열첨자 위치를 저장하는 것이 목적이기 때문에 sameDates.get(i)를 통해 sameDates의 배열요소(값)를 indexes에 add시켜줍니다.<br>
for 반복문이 끝나면 해당 일자와 같은 account 객체들의 위치가 모두 indexes에 저장되었기 때문에 다음 날짜로 이동을 하여야 합니다.<br><br>
다음 날짜로 이동(해당 일자 + 1일)하기 위해서 Date클래스의 정적메소드인 nextDate(Date date, int days)를 호출합니다.<br>
<a href="https://injae7034.github.io/java/nineteenth/#%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-2%EA%B0%9Cint-%EC%9E%90%EB%A3%8C%ED%98%95-%EB%B3%80%EC%88%98-date%EA%B0%9D%EC%B2%B4-%EC%B0%B8%EC%A1%B0%EB%B3%80%EC%88%98%EA%B0%92%EB%A5%BC-%EA%B0%80%EC%A7%80%EB%8A%94-nextdate"
target="_blank">Java의 LocalDate를 활용해서 나만의 Date클래스 만들기 - nextDate 메소드</a>

nextDate를 호출하고 그 반환값을 fromDate에 저장하면 +1일이 누적된 일자(date)가 fromDate에 저장이 될 것이고, 다시 while반복의 조건문으로 가서 fromDate.isGreaterThan(toDate) == false 를 확인합니다.<br><br>
+1일을 해도 fromDate가 toDate보다 여전히 작으면 다시 반복을 들어가고, 같아도 반복문을 다시 들어갑니다.<br><br>
+1일을 해서 fromDate가 toDate보다 일자가 커지면 그제서야 while반복을 빠져 나오고, 기간동안 account객체들의 배열첨자 위치를 저장한 indexes를 반환하면서 find메소드가 종료됩니다.

### find(date, date, briefs)
```java
public class AccountBook
{
    //find(date, date, briefs)
    public ArrayList<Integer> find(Date fromDate, Date toDate, String briefs)
    {
        //Account 객체들 중에서 fromDate부터 toDate까지 Account 객체들 중에서
        //같은 briefs를 가진 Account 객체들의 위치를 저장할 위치ArrayList 생성하기
        ArrayList<Integer> indexes = new ArrayList<>();
        //같은 date들의 위치를 저장할 임시공간인 ArrayList 생성하기
        ArrayList<Integer> sameDates = null;
        //fromDate가 toDate보다 작거나 같은동안 반복한다.
        while(fromDate.isGreaterThan(toDate) == false)
        {
            //가계부(AccountBook)에서 fromDate와 같은 date를 가진
            //Account객체들의 위치를 찾아서 그 위치를 저장한 배열을 반환한다.
            sameDates = this.find(fromDate);
            //같은 dates의 위치를 저장한 배열의 처음부터 마지막까지 반복한다.
            for(int i = 0; i < sameDates.size(); i++)
            {
                //가계부에서 같은 date를 가진 Account 객체의 briefs가 찾고자 하는 briefs와 같으면
                if(briefs.compareTo(this.accounts.get(sameDates.get(i)).getBriefs()) == 0)
                {
                    //위치를 저장한다.
                    indexes.add(sameDates.get(i));
                }
            }
            //fromDate를 1일 증가시켜준다.
            fromDate = Date.nextDate(fromDate, 1);
        }
        return indexes;
    }
}
```
매개변수로 일자(date)와 일자(date), 그리고 적요(briefs)를 받습니다.<br><br>
위에서 기간으로 찾는 find매소드와 로직이 같습니다.<br><br>
차이점은 find(date, date)의 경우 같은 일자(date)를 가진 account 객체들의 배열첨자 위치를 무조건 저장했다면, find(date, date, briefs)의 경우 **선택구조를 통해 briefs가 같은 경우만** account 객체들의 배열참자 위치를 저장합니다.<br><br>
즉, 위의 find(date, date)는 기간동안 모든 account 항목들을 검색하는 것이거, find(date, date, briefs)는 기간동안 같은 적요(briefs)를 가지는 account 항목들만 검색하겠다는 말입니다.

## correct 메소드
```java
public class AccountBook
{
    //correct
    public int correct(int index, int amount, String remarks)
    {
        //매개변수로 입력 받은 위치의 Account객체를 구한다.
        Account account = this.accounts.get(index);
        int balance = 0;//잔액을 저장할 임시공간
        //account가 수입(Income)이면
        if(account instanceof Income)
        {
            //balance를 구한다.
            balance = account.getBalance() + (amount - account.getAmount());
        }
        //account가 지출(Outgo)이면
        else
        {
            //balance를 구한다.
            balance = account.getBalance() + (account.getAmount() + amount);
        }
        //Account객체의 금액(amount)을 변경해준다.
        account.setAmount(amount);
        //Account객체의 잔액(balance)을 변경해준다.
        account.setBalance(balance);
        //Account객체의 비고(remarks)를 변경해준다.
        account.setRemarks(remarks);
        //Account객체의 잔액이 수정되었기 때문에 수정된 Account객체
        //이후의 Account객체들의 잔액들도 모두 수정해줘야함.
        //수정된 Account객체 이후부터 가계부의 마지막 Account객체까지 반복한다.
        Account afterAccount = null;
        for(int i = index + 1; i < this.length; i++)
        {
            //잔액을 수정한 Account객체를 구한다.
            account = this.accounts.get(i - 1);
            //잔액을 수정한 Account객체의 다음 Account객체를 구한다.
            afterAccount = this.accounts.get(i);
            //afterAccount가 수입(Income)이면
            if(afterAccount instanceof Income)
            {
                //수정할 잔액을 구한다.
                balance = account.getBalance() + afterAccount.getAmount();
            }
            //afterAccount가 지출(Outgo)이면
            else
            {
                //수정할 잔액을 구한다.
                balance = account.getBalance() - afterAccount.getAmount();
            }
            //잔액을 수정한 Account객체의 다음 Account객체의 잔액을 수정한다.
            afterAccount.setBalance(balance);
        }
        return index;
    }
}
```

### correct 메소드 설명
ArrayList\<Account\>에서 수정할 Account 객체의 배열첨자 위치인 index를 매개변수로 입력 받고, 수정할 금액(amount)와 수정할 비고(remarks)를 매개변수로 입력받습니다.<br><br>
수정할 금액인 amount는 record와는 달리 무조건 양수로 입력받도록 설정합니다.<br><br>
메소드 내부에서 먼저 수정할 Account의 객체를 get메소드를 통해 구합니다.<br><br>
구한 account는 수입일수도 있고, 지출일수도 있습니다.<br><br>
수입인지 지출인지에 따라 잔액(balance)의 값이 달라지기 때문에 구분하여서 balance를 구해줘야 합니다.<br><br>
그래서 instanceof을 활용하여 account가 Income인지 Outgo인지를 확인하는 선택구조를 사용합니다.<br><br>
account가 수입이면 잔액을 구할 때 account의 getBalance를 통해 기존 누적 잔액을 먼저 구합니다.<br><br>
그리고 매개변수로 입력받은 amount가 앞으로 변경될 amount이기 때문에 이 amount에 현재 account에 저장된 변경 전 amount의 값을 getAmount로 구한 다음 빼줍니다.<br><br>
예를 들어 현재 account는 수입이고 현재 저장된 잔액이 5,000원이고, 금액은 10,000원일 때, 변경할 금액이 8,000원이면 위의 식대로 하면 5,000 + (8,000 - 10,000) = 3,000원이 잔액이 됩니다.<br><br>
변경할 금액이 12,000원이면 5,000 + (12,000 - 10,000) = 7,000원이 잔액이 됩니다.<br><br>
account가 지출이고 현재 저장된 잔액이 5,000원이고, 금액은 -10,000일 때, 변경할 금액이 8,000원이면 5,000 + (-10,000 + 8,000) = 3,000원이 잔액이 됩니다.<br><br>
이처럼 수입이냐 지출이냐에 따라 잔액 처리가 다르기 때문에 instanceof을 이용한 선택구조로 이를 나눠서 처리합니다.<br><br>
balance가 구해졌으면 이제 account의 필드멤버인 금액(amount)을 매개변수로 입력 받은 금액으로 변경해줘야 합니다.<br><br>
setAmount(amount)를 통해 금액을 변경하는데, 저번 시간에 Account의 setAmount는 추상메소드라서 자식들이 이를 자신의 특성에 맞게 구체화시킨다고 했습니다.<br>
<a href="https://injae7034.github.io/java/twentieth/#account%EC%9D%98-setter-%EB%A9%94%EC%86%8C%EB%93%9C"
target="_blank">Account의 setter 메소드</a>

<br><br>

Income은 수입이기 때문에 금액(amount)을 + 값으로 저장하기 때문에 setAmount(amount)에서 매개변수로 입력받은 amount를 그대로 대입하도록 처리했습니다.<br>
<a href="https://injae7034.github.io/java/twentieth/#setamount-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9"
target="_blank">Income의 setAmount 메소드 오버라이딩</a>

<br><br>

Outgo는 지출이기 때문에 금액(amount)를 - 값으로 저장하기 때문에 setAmount(amount)에서 매개변수로 입력받은 amount에 -1을 곱한 다음 저장하도록 처리했습니다.<br>
<a href="https://injae7034.github.io/java/twentieth/#setamount-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9-1"
target="_blank">Outgo의 setAmount 메소드 오버라이딩</a>

<br><br>
Account의 setAmount는 이렇게 Income과 Outgo 클래스들이 자신의 특성에 맞게 오버라이딩하였기 때문에 account를 instanceof 하여서 Income인지 Outgo인지 따로 확인을 하여 처리할 필요없이 account에서 바로 setAmount를 호출하면 됩니다.<br><br>
account가 Income이면 Income에서 오버라이딩한 setAmount가 호출될 것이고, 반대로 account가 Outgo이면 Outgo에서 오버라이딩한 setAmount가 호출 될 것입니다.<br><br>

즉, **프로그래머는 수정할 account가 Income인지 Outgo인지 구분할 필요없이 setAmount를 호출**하면 알아서 구분이 되어 처리가 됩니다.<br><br>

# 다형성과 오버라이딩을 이용하여 correct 코드 수정하기

그럼 위의 correct 코드에서 이 방법을 적용하면 더 간단하게 코드를 짤 수 있지 않을까요?<br><br>
**위에서 balance(잔액)를 구할 떄 instanceof을 이용하여 Income인지 Outgo인지 구분하여 처리**를 하였습니다.<br><br>
여기서도 프로그래머가 구분할 필요없이 처리하도록 할 수 있지 않을까요?

## Account 클래스에 추상메소드(calculateBalance) 추가
그렇게 하기 위해서 **Account 클래스에 calculateBalance라는 새로운 추상메소드**를 선언해줍니다.
```java
public abstract class Account
{
    //잔액(balance) 계산하기 메소드
    public abstract int calculateBalance(int amount);
}
```

## Income 클래스에서 calculateBalance 오버라이딩(구현)
Income 클래스에서 Account 클래스의 추상메소드인 calculateBalance를 오버라이딩(구현)해주는데 위의 correct에서 instanceof으로 구분하여 Income일 때 balance를 구했던 과정을 그대로 옮겨줍니다.
```java
public class Income extends Account
{
    //잔액(balance) 계산하기 메소드 오버라이딩
    @Override
    public int calculateBalance(int amount)
    {
        return this.balance + (amount - this.amount);
    }
}
```

## Outgo 클래스에서 calculateBalance 오버라이딩(구현)
Outgo 클래스에서 Account 클래스의 추상메소드인 calculateBalance를 오버라이딩(구현)해주는데 마찬가지로 위의 correct에서 instanceof으로 구분하여 Outgo일 때 balance를 구했던 과정을 그대로 옮겨줍니다.
```java
public class Outgo extends Account
{
    //잔액(balance) 계산하기 메소드 오버라이딩
    @Override
    public int calculateBalance(int amount)
    {
        return this.balance + (this.amount + amount);
    }
}
```

### calculateBlace 메소드를 이용하여 correct 코드 수정하기
```java
public class AccountBook
{
    //correct
    public int correct(int index, int amount, String remarks)
    {
        //매개변수로 입력 받은 위치의 Account객체를 구한다.
        Account account = this.accounts.get(index);
        //변경할 금액을(amount) 바탕으로 변경될 잔액을 구한다.
        int balance = account.calculateBalance(amount);
        //Account객체의 금액(amount)을 변경해준다.
        account.setAmount(amount);
        //Account객체의 잔액(balance)을 변경해준다.
        account.setBalance(balance);
        //Account객체의 비고(remarks)를 변경해준다.
        account.setRemarks(remarks);
        //Account객체의 잔액이 수정되었기 때문에 수정된 Account객체
        //이후의 Account객체들의 잔액들도 모두 수정해줘야함.
        //수정된 Account객체 이후부터 가계부의 마지막 Account객체까지 반복한다.
        Account afterAccount = null;
        for(int i = index + 1; i < this.length; i++)
        {
            //잔액을 수정한 Account객체를 구한다.
            account = this.accounts.get(i - 1);
            //잔액을 수정한 Account객체의 다음 Account객체를 구한다.
            afterAccount = this.accounts.get(i);
            //afterAccount가 수입(Income)이면
            if(afterAccount instanceof Income)
            {
                //수정할 잔액을 구한다.
                balance = account.getBalance() + afterAccount.getAmount();
            }
            //afterAccount가 지출(Outgo)이면
            else
            {
                //수정할 잔액을 구한다.
                balance = account.getBalance() - afterAccount.getAmount();
            }
            //잔액을 수정한 Account객체의 다음 Account객체의 잔액을 수정한다.
            afterAccount.setBalance(balance);
        }
        return index;
    }
}
```
보시다시피 instandof Income인지 outgo인지 선택과정과 그에 대한 처리가 **int balance = account.calculateBalance(amount);** 단 한 줄로 대체됩니다.<br><br>
코드가 굉장히 깔끔해졌고, 프로그래머는 이제 account가 Income인지 Outgo인지 알아낼 필요없이 바로 calculateBalance를 호출하면 알아서 Income과 Outgo를 구분하여 처리해줍니다.<br><br>
이제 다시 나머지 correct 코드를 설명으로 돌아갑시다.<br><br>
setBalance와 setRemarks는 Account클래스이 일반메소드로써 Income과 Outgo 구분없이 이 메소드를 공통으로 사용하여 그 값을 바로 대입해줍니다.<br>
<a href="https://injae7034.github.io/java/twentieth/#account%EC%9D%98-setter-%EB%A9%94%EC%86%8C%EB%93%9C"
target="_blank">Account의 setter 메소드</a>

# correct 메소드 구현시 주의할 점
correct메소드에서 주의할 점이 단순히 해당 위치의 Account 객체의 amount와 balance만 수정해주면 끝이 아닙니다.<br><br>
해당 위치의 Account 객체의 amount를 수정해주면 balance를 바꾸는 처리까지는 현재 완료했습니다.<br><br>
문제는 해당 위치의 Account 객체 이후의 Account 객체들의 balance값들이 문제가 됩니다.<br><br>
해당 위치의 Account 객체 이후의 Account 객체들의 amount는 바뀌지 않았지만 balance의 특성으로 인해 Account 객체 이후의 Account 객체들의 balance 값을 모두 수정해줘야 합니다.<br><br>
balance의 특성은 amount에 종속적이기 때문에 amount에 따라 그 값이 바뀐다고 했습니다.<br><br>
그리고 또 다른 특성이 amount는 해당 Account 객체에만 해당되지만 balance는 누적값이라는 점입니다.<br><br>
즉, 해당 Account 객체의 balance값의 의미는 현재 account항목까지 잔액(balance)이 얼마인지 누적된 값(= 총수입 - 총지출)을 의미합니다.<br><br>
그래서 해당 Account의 amount값이 바뀌면 그 이전의 Account 객체들은 영향을 받지 않지만 그 이후의 Account 객체들은 그 영향을 받기 때문에 balance 값들을 모두 수정해줘야하는 처리를 별도로 해줘야 합니다.<br><br>
그래서 for 반복문을 통해 i의 초기값, 즉, 시작을 수정한 Account 객체의 다음 위치부터 가계부의 마지막 account 객체까지 반복을 하면서 순차적으로 balance값을 모두 수정해주는 처리를 해줍니다.<br><br>
코드를 보면 아시다시피 여기서도 account 객체가 Income(수입)인지 Outgo(지출)인지에 따라 instanceof으로 구분하여 잔액(balance)을 구하고 있습니다.<br><br>
여기에도 위에서 했던 것처럼 **다형성과 오버라이딩을 적용**하여 이 과정을 없애도록 하겠습니다.<br><br>
추가적으로 **오버로딩을 적용**하여 메소드명을 새로 정하기 않고, **calculateBalance 명칭을 그대로 사용**하도록 하겠습니다.

# 다형성과 오버라이딩, 오버로딩을 적용하여 correct 메소드 코드 수정하기
우선 Account 클래스에서 Account를 매개변수로 받는 추상메소드 alculateBalance(Account account)를 선언해줍니다.
```java
public abstract class Account
{
    //잔액(balance) 계산하기 메소드
    public abstract int calculateBalance(Account account);
}
```
아까 전에 선언한 추상메소드인 calculateBalance는 매개변수가 int amount였기 때문에 새로 선언하는 추상메소드인 alculateBalance(Account account)와는 **매개변수의 자료형이 다르기 때문에 오버로딩을 적용**할 수 있습니다.

## Income 클래스에서 calculateBalance 오버라이딩(구현)
Income 클래스에서 Account 클래스의 추상메소드인 calculateBalance를 오버라이딩(구현)해주는데 위의 correct에서 instanceof으로 구분하여 Income일 때 balance를 구했던 과정을 그대로 옮겨줍니다.
```java
public class Income extends Account
{
    //잔액(balance) 계산하기 메소드 오버라이딩
    @Override
    public int calculateBalance(Account account)
    {
        return account.balance + this.amount;
    }
}
```

## Outgo 클래스에서 calculateBalance 오버라이딩(구현)
Outgo 클래스에서 Account 클래스의 추상메소드인 calculateBalance를 오버라이딩(구현)해주는데 마찬가지로 위의 correct에서 instanceof으로 구분하여 Outgo일 때 balance를 구했던 과정을 그대로 옮겨줍니다.
```java
public class Outgo extends Account
{
    //잔액(balance) 계산하기 메소드 오버라이딩
    @Override
    public int calculateBalance(Account account)
    {
        return account.balance - this.amount;
    }
}
```

### calculateBlace 메소드를 이용하여 correct 코드 수정하기
```java
public class AccountBook
{
    //correct
    public int correct(int index, int amount, String remarks)
    {
        //매개변수로 입력 받은 위치의 Account객체를 구한다.
        Account account = this.accounts.get(index);
        //변경할 금액을(amount) 바탕으로 변경될 잔액을 구한다.
        int balance = account.calculateBalance(amount);
        //Account객체의 금액(amount)을 변경해준다.
        account.setAmount(amount);
        //Account객체의 잔액(balance)을 변경해준다.
        account.setBalance(balance);
        //Account객체의 비고(remarks)를 변경해준다.
        account.setRemarks(remarks);
        //Account객체의 잔액이 수정되었기 때문에 수정된 Account객체
        //이후의 Account객체들의 잔액들도 모두 수정해줘야함.
        //수정된 Account객체 이후부터 가계부의 마지막 Account객체까지 반복한다.
        for(int i = index + 1; i < this.length; i++)
        {
            //잔액을 수정한 Account객체의 다음 Account객체를 구한다.
            account = this.accounts.get(i);
            //수정할 잔액을 구한다.
            balance = account.calculateBalance(this.accounts.get(i - 1));
            //잔액을 수정한다.
            account.setBalance(balance);
        }
        return index;
    }
}
```
보시다시피 instandof Income인지 outgo인지 선택과정과 그에 대한 처리가 **int balance = account.calculateBalance(this.accounts.get(i - 1));** 단 한 줄로 대체됩니다.<br><br>
코드가 굉장히 깔끔해졌고, 프로그래머는 이제 account가 Income인지 Outgo인지 알아낼 필요없이 바로 calculateBalance를 호출하면 알아서 Income과 Outgo를 구분하여 처리해줍니다.<br><br>
이렇게 calculateBalance을 통해 반환 받은 balance값을 account 객체의 setBalance메소드를 호출하여 그 값을 바꿔줍니다.<br><br>
이후 바뀐 Account객체의 위치를 반환하고 correct 메소드가 종료됩니다.

# 현재 correct 코드에서 좀 더 고민해보기 - 비효율성
여기서 더 나아가 봅시다.<br><br>
해당 위치의 Account 객체의 금액(amount)을 변경하게 되면 해당 위치의 잔액(balance)는 물론 그 뒤에 위치한 모든 Account 객체들의 잔액(balance)을 모두 바꿔줘야한고 했습니다.<br><br>
이 과정은 단순히 한 개의 항목을 수정하는 것이 아니라 만약에 항목이 10,000개가 있는데 제일 첫번째 항목의 금액을 수정할 경우 이 후에 위치한 9,999개의 잔액을 모두 변경해줘야 하는 시간이 많이 걸리는 처리입니다.<br><br>
물론 금액(amount)을 변경하였을 경우 어쩔 수 없이 위의 처리를 해줘야 합니다.<br><br>
하지만 금액(amount)은 변경하지 않고, 비고(remarks)만 변경하였을 경우에는 어떨까요?<br><br>
위의 코드대로라면 비고만 변경했음에도 불구하고, for 반복문을 통해 바뀌지도 않은 값을 그대로 대입을 해줘야 합니다.<br><br>
아까 예시대로라면 9,999개의 항목의 잔액이 바뀌지 않았음에도 불구하고, 값을 그대로 대입하는 과정을 9,999번 해야 합니다.<br><br>
굉장히 비효율적이라고 할 수 있습니다;;
물론 금액과 비고를 둘 다 바꾸면 아무 문제가 없지만 금액은 그대로인데 비고만 바꾸는 경우는 이러한 비효율적인 처리가 발생하지 않도록 해줄 필요가 있습니다.<br><br>
방법은 두가지입니다.<br><br>
## 해결책1 - correct 메소드 내부에서 처리하기
메소드 내부에서 balance를 구하고, setAmount를 하기 전에 먼저 매개변수로 입력 받은 amount값이 해당 위치의 Account 객체의 amount 값과 다른지 먼저 확인해보는 과정을 추가합니다.<br><br>
다르면 위에서 했던 과정을 그대로 하면 되고, 같은 경우는 remarks만 바꿔주고, amount와 balance를 바꿔주는 처리를 제외합니다.
```java
public class AccountBook
{
    //correct
    public int correct(int index, int amount, String remarks)
    {
        //매개변수로 입력 받은 위치의 Account객체를 구한다.
        Account account = this.accounts.get(index);
        //매개변수로 입력 받은 remarks로 Account객체의 remarks를 변경해준다.
        account.setRemarks(remarks);
        //금액의 변경이 있으면
        if(amount != account.getAmount())
        {
            //변경할 금액을(amount) 바탕으로 변경될 잔액을 구한다.
            int balance = account.calculateBalance(amount);
            //Account객체의 금액(amount)을 변경해준다.
            account.setAmount(amount);
            //Account객체의 잔액(balance)을 변경해준다.
            account.setBalance(balance);

            //Account객체의 잔액이 수정되었기 때문에 수정된 Account객체
            //이후의 Account객체들의 잔액들도 모두 수정해줘야함.
            //수정된 Account객체 이후부터 가계부의 마지막 Account객체까지 반복한다.
            for(int i = index + 1; i < this.length; i++)
            {
                //잔액을 수정한 Account객체의 다음 Account객체를 구한다.
                account = this.accounts.get(i);
                //수정할 잔액을 구한다.
                balance = account.calculateBalance(this.accounts.get(i - 1));
                //잔액을 수정한다.
                account.setBalance(balance);
            }
        }
        return index;
    }
} 
```
비고는 해당 Account 객체 하나만 수정해주면 되기 때문에 remarks가 변경되었는지 변경되지 않았는지 따지지 않고 무조건 바꿔줍니다.<br><br>
그게 remarks가 같은지 안같은지 따져서 바꿀지 말지 정하는 것보다 빠릅니다.<br><br>
remarks를 바꾸고 나서 **if(amount != account.getAmount())**을 통해 매개변수로 입력 받은 amount와 수정할 Account 객체의 amount 값이 다르면 값을 바꿔주는 처리를 하고, 다르면 바로 위치값 index를 반환하고 메소드를 종료시킵니다.<br><br>
이렇게 하면 비고만 바꿨을 때 correct 코드에서 발생했던 비효율적인 과정을 없앨 수 있습니다.

## 해결책2 - correct 메소드 오버로딩하기
두 번째 방법은 correct메소드 내부에서 처리하는 것이 아니라 외부에서 처리하는 방법으로 애초에 금액만 수정하는 correct, 비고만 수정하는 correct, 금액과 비고 둘 다 수정하는 correct 이렇게 3개를 정의하는 것입니다.<br><br>
금액만 수정하는 correct의 매개변수는 바꿀 위치인 int index와 바꿀 금액 int amount이고, 비고만 수정하는 correct의 매개변수는 바꿀 위치인 int index, 바꿀 비고 String remarks입니다.<br><br>
그리고 금액과 비고 둘다 수정하는 correct메소드의 매개변수는 int index, int amount, String remarks이므로 각각 매개변수가 다르기 때문에 correct라는 같은 명칭을 사용하여 오버로딩할 수 있습니다.
```java
public class AccountBook
{
    //correct 메소드 오버로딩
    public int correct(int index, int amount)
    {
        //매개변수로 입력 받은 위치의 Account객체를 구한다.
        Account account = this.accounts.get(index);
        //변경할 금액을(amount) 바탕으로 변경될 잔액을 구한다.
        int balance = account.calculateBalance(amount);
        //Account객체의 금액(amount)을 변경해준다.
        account.setAmount(amount);
        //Account객체의 잔액(balance)을 변경해준다.
        account.setBalance(balance);

        //Account객체의 잔액이 수정되었기 때문에 수정된 Account객체
        //이후의 Account객체들의 잔액들도 모두 수정해줘야함.
        //수정된 Account객체 이후부터 가계부의 마지막 Account객체까지 반복한다.
        for(int i = index + 1; i < this.length; i++)
        {
            //잔액을 수정한 Account객체의 다음 Account객체를 구한다.
            account = this.accounts.get(i);
            //수정할 잔액을 구한다.
            balance = account.calculateBalance(this.accounts.get(i - 1));
            //잔액을 수정한다.
            account.setBalance(balance);
        }
        return index;
    }
    public int correct(int index, String remarks)
    {
        //매개변수로 입력 받은 위치의 Account객체를 구한다.
        Account account = this.accounts.get(index);
        //매개변수로 입력 받은 remarks로 Account객체의 remarks를 변경해준다.
        account.setRemarks(remarks);
        return index;
    }
    //correct
    public int correct(int index, int amount, String remarks)
    {
        //매개변수로 입력 받은 위치의 Account객체를 구한다.
        Account account = this.accounts.get(index);
        //변경할 금액을(amount) 바탕으로 변경될 잔액을 구한다.
        int balance = account.calculateBalance(amount);
        //Account객체의 금액(amount)을 변경해준다.
        account.setAmount(amount);
        //Account객체의 잔액(balance)을 변경해준다.
        account.setBalance(balance);
        //Account객체의 비고(remarks)를 변경해준다.
        account.setRemarks(remarks);
        //Account객체의 잔액이 수정되었기 때문에 수정된 Account객체
        //이후의 Account객체들의 잔액들도 모두 수정해줘야함.
        //수정된 Account객체 이후부터 가계부의 마지막 Account객체까지 반복한다.
        for(int i = index + 1; i < this.length; i++)
        {
            //잔액을 수정한 Account객체의 다음 Account객체를 구한다.
            account = this.accounts.get(i);
            //수정할 잔액을 구한다.
            balance = account.calculateBalance(this.accounts.get(i - 1));
            //잔액을 수정한다.
            account.setBalance(balance);
        }
        return index;
    }
}
```
이렇게 3가지 방식으로 오버로딩하면 적재적소에 필요한 correct를 이용하면 되기 때문에 애초에 비효율적인 처리가 발생하지 않습니다.<br><br>
그래서 저는 첫 번째 방법보다는 두 번째 방법을 추천합니다.<br><br>
메소드가 여러 가지 기능을 동시에 하려다 보면 비대해지게 되는데 그것보다는 세분화하여 한 번에 한가지 기능을 하는 것이 코드 이해도 쉽고, 유지 및 보수 차원에서도 더 좋기 때문입니다.<br><br>

# calculate 메소드
calculate메소드를 구현하기 위해서 또 다른 개념을 적용하였는데 이번 글에서 다 설명하다보면 글이 길어지고, 이번 글의 핵심 개념인 오버로딩, 다형성, 오버라이딩과도 벗어나는 개념이라서 다음 글에서 이에 대해 좀 더 자세하게 설명하려고 합니다.
# 마치며
이번 시간에는 AccountBook의 메소드를 구현하면서 **오버로딩, 다형성, 오버라이딩을 적용하면 어떻게 코드를 더 효율적으로 짤 수 있는지**를 배우게 되었습니다.<br><br>
다음 시간에는 AccountBook에서 아직 구현하지 않은 마지막 메소드인 calculate을 구현하면서 **HashMap**에 대해 배워보려고 합니다.<br><br>
궁굼하시거나 잘못된 점에 대한 지적은 언제나 환영입니다^^
