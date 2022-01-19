---
layout: single
title:  "Java프로젝트하면서 추상클래스, 추상메소드, 다형성, 부모와 자식간의 상속 개념 배우기"
categories: Java
tag: [Java, 프로젝트, 가계부, AccountBook, 오버로딩, 오버라이딩, 다형성, 추상클래스, 추상메소드, 상속]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 시작하며
이번 시간에는 저번 시간에 만든 Date클래스(LocalDate를 활용하여 커스터마이징한 Date)를 활용하여 가계부(AccountBook)프로그램을 구현하려고 합니다.<br>
<a href="https://injae7034.github.io/java/nineteenth/"
target="_blank">Java의 LocalDate를 활용해서 나만의 Date클래스 만들기</a>

<br><br>

가계부(AccountBook)는 파일이자 컨트롤이기 때문에 데이터를 저장하고 있는 entity 클래스를 먼저 구현하려고 합니다.<br><br>
**entity를 구현하면서 추상클래스, 추상메소드, 부모와 자식간의 상속관계에 대해서 공부**해도록 합시다.<br><br>
entity를 구현하기 위해서 가계부에 어떠한 데이터가 필요한지 알아봅시다.<br><br>
가계부에서 일단 **수입**인지 **지출**인지를 먼저 판단해야 합니다.<br><br>
그 다음으로 돈을 받거나 쓴 **일자**가 필요합니다.<br><br>
그리고 돈을 무슨 목적으로 받았는지 또는 썼는지를 명시할 **적요**가 필요합니다.<br><br>
돈을 얼마나 받았거나 썼는지를 나타내는 **금액**도 필요하고, 이 금액을 바탕으로 현재 **잔액**이 얼마있는지 필요할 것입니다.<br><br>
추가적으로 비고사항을 적기 위한 **비고**란도 필요할 것입니다.<br><br>
그래서 **Income**클래스 생성하여 **수입**에 관한 처리를 하도록 하고, **Outgo**클래스를 생성하여 **지출**에 관한 처리를 하도록 합니다.<br><br>
저번 시간에 만들었던 **Date**클래스를 **일자**에 사용하고, **String**클래스를 **적요**에 사용합니다.<br><br>
**금액**과 **잔액**은 **int**형을 사용하고, **비고**는 **String**클래스를 이용합니다.

# Income 클래스 필드멤버
Income클래스는 수입을 나타내며 인스턴스 필드 멤버로는 date(일자), briefs(적요), amount(금액), balance(잔액), remarks(비고)가 있습니다.

# Outgo 클래스 필드멤버
Outgo클래스는 지출을 나타내며 인스턴스 필드 멤버로는 date(일자), briefs(적요), amount(금액), balance(잔액), remarks(비고)가 있습니다.

# Income과 Outgo의 필드멤버 중복
위에서 보다시피 Income과 Outgo는 수입이냐 지출이냐의 차이만 있지 모든 필드멤버가 동일합니다.<br><br>
즉, 둘은 중복 또는 **공통된 사항들**을 가지고 있다는 말입니다.<br><br>
이러한 공통된 사항들을 굳이 Income과 Outgo로 각각 나누어서 비효율적으로 관리할 필요가 있을까요?<br><br>
이를 효율적으로 개선하기 위해서 생긴 것이 **부모클래스**입니다.

# Account(부모클래스)
Income과 Outgo의 공통된 필드 멤버들을 **Account**라는 **부모클래스**를 생성하여, Account에서 date(일자), briefs(적요), amount(금액), balance(잔액), remarks(비고)를 필드 멤버로 관리합니다.<br><br>
그리고 **Income과 Outgo는 Account를 상속받는 자식클래스**가 되면 Income과 Outgo는 date(일자), briefs(적요), amount(금액), balance(잔액), remarks(비고)를 직접 필드 멤버로 두지 않고, 부모인 Account에 접근하여 이 필드들을 이용할 수 있습니다.<br><br>
그래서 이 필드들과 관련된 **공통된 사항들은 모두 Account에서 처리**하고, **Income과 Outgo의 개별적인 특성에 대해서만 각자 처리**를 하면 효율적일 것입니다.

## Account 클래스의 필드 멤버
```java
public class Account
{
    //인스턴스 필드 멤버
    protected Date date;
    protected String briefs;
    protected int amount;
    protected int balance;
    protected String remarks;
}
```
필드 멤버로 아까 언급한 date, briefs, amount, balance, remarks가 있는데 여기서 접근제어자는 private이 아니라 protected입니다.<br><br>
그 이유는 protected로 해야 부모인 Account를 상속받는 자식들인 Income과 Outgo에서 접근할 수 있기 때문입니다.<br><br>
private으로 설정하면 자기 자신을 제외하고, 자식이더라도 아무도 접근을 할 수 없습니다.

## 생성자(오버로딩)
생성자는 매개변수가 없는 디폴트 생성자와 각 필드들에 대입할 매개변수를 5개 가지는 생성자 이렇게 2개를 구현하였습니다.
### 디폴트 생성자
```java
public class Account
{
    //디폴트 생성자
    public Account()
    {
        this.date = Date.today();
        this.briefs = "";
        this.amount = 0;
        this.balance = 0;
        this.remarks = "";
    }
}
```
디폴트생성자로 Account를 생성하면 일자는 오늘 날짜로 하고, 나머지는 공란 또는 0으로 설정합니다.

### 매개변수를 가지는 생성자
```java
public class Account
{
    //매개변수를 가지는 생성자
    public Account(Date date, String briefs, int amount, int balance, String remarks)
    {
        this.date = date;
        this.briefs = briefs;
        this.amount = amount;
        this.balance = balance;
        this.remarks = remarks;
    }
}
```
매개변수를 가지는 생성자는 매개변수로 입력 받은 값들을 그대로 필드 멤버에 대입하여 Account를 생성해줍니다.

## equals 메소드 오버라이딩
```java
public class Account
{
    //equals 오버라이딩
    @Override
    public boolean equals(Object obj)
    {
        boolean ret = false;
        if(obj instanceof Account)
        {
            if(this.date.equals(((Account)obj).date) == true &&
                    this.briefs.compareTo(((Account)obj).briefs) == 0
                    && this.amount == ((Account)obj).amount &&
                    this.balance == ((Account)obj).balance &&
                    this.remarks.compareTo(((Account)obj).remarks) == 0)
            {
                ret = true;
            }
        }
        return ret;
    }
}
```
Object클래스의 메소드인 equals를 오버라이딩하여 Account의 특성에 맞게 변경해줍니다.<br><br>
매개변수가 Object형이기 때문에 먼저 Account로 다운캐스팅이 되는지 확인해주고, 가능하면 Account로 형변환을 하여 5가지 필드 멤버가 서로 같으면 true를 반환해줍니다.<br><br>
Account로 형변환이 안되거나 5가지 필드 멤버 중에 하나만 달라도 디폴트값인 false를 반환합니다.

## toString 메소드 오버라이딩
```java
public class Account
{
    //toString 오버라이딩
    @Override
    public String toString()
    {
        //통화에 원화표시를 해주고, 세자리수마다 콤마(,)를 찍어줌.
        String amount = NumberFormat.getCurrencyInstance(Locale.KOREA).format(this.amount);
        String balance = NumberFormat.getCurrencyInstance(Locale.KOREA).format(this.balance);
        return new String("일자 : " + this.date.toString() + ", 적요 : "
                + this.briefs + ", 금액 : " + amount + ", 잔액 : "
                + balance + ", 비고 : " + this.remarks);
    }
}
```
Object클래스의 메소드인 toString을 오버라이딩하여 Account의 특성에 맞게 변경해줍니다.<br><br>
amount와 balance의 현재 자료형이 int인데 이를 출력할 때는 통화의 특성에 맞게, 그리고 한국에서 사용하는 원화를 기준으로 하여 출력할 수 있도록 처리를 해줍니다.<br><br>
그러기 위해서 NumberFormat클래스의 정적메소드인 getCurrencyInstance에 매개변수로 Locale.KOREA를 넣어 원화를 기준으로 하고, format에 amount또는 balance를 넣어서 해당 int형 자료형을 통화단위로 바꾸서 세자리마다 콤마(,)가 찍히도록 변경해줍니다.<br><br>
이 후 각 필드들을 잘 나타내도록 문자열을 생성하여 반환합니다.

## getter 메소드
Account의 모든 필드 멤버가 protected이기 때문에 자식이 아닌 이상 이 클래스의 필드값에 접근할 수 없기 때문에 그 필드 멤버의 값을 외부에서 얻어오기 위해 각 필드마다 getter메소드를 만들어줍니다.
```java
public class Account
{
    //getter
    public Date getDate() {return this.date;}
    public String getBriefs() {return this.briefs;}
    public int getAmount() {return this.amount;}
    public int getBalance() {return this.balance;}
    public String getRemarks() {return this.remarks;}
}
```

# Income과 Outgo의 차이점
Income과 Outgo의 공통점을 가진 Account클래스 구현이 끝났습니다.<br><br>
그럼 Income과 Outgo의 차이점은 무엇일까요?<br><br>
딱 하나입니다.<br><br>
Income은 수입, 즉, 들어오는 돈이기 때문에 +로 금액(amount)을 표시하고, Outgo는 지출, 즉, 나가는 돈이기 때문에 -로 금액(amount)을 표시합니다.<br><br>
이외에는 모든 점이 동일합니다.<br><br>
그래서 Income과 Outgo클래스를 구현할 때는 금액(amount)이 +인지 -인지에 유의하면 됩니다.<br><br>

## Account의 setter 메소드
Account에서 amount와 remarks만 변경할 수 있도록 설정하였습니다.<br><br>
이는 개인마다 의견이 다르지만 일자(Date)나 적요(briefs)를 손대면 장부조작이 용이하기 때문에 이를 방지하기 위해서 막았습니다.<br><br>
물론 개인이 이용하는 가계부에 그런 것이 아무 상관이 없지만 일단은 저는 amount와 remarks만 변경할 수 있도록 만들었습니다.<br><br>
그리고 balance의 경우는 amount값이 바뀌면 자동으로 바꿔줘야 하는 종속적인 관계이기 때문에 balance 역시 setter 메소드를 만들었습니다.<br><br>
굳이 Income과 Outgo의 차이점을 설명한 후에 setter메소드의 설명을 하는 이유는 setter메소드 중에서  setBalacne나 setRemarks는 공통적으로 처리가 가능하나 setAmount는 Income인지 Outgo인지에 따라 다른 처리가 필요하기 때문입니다.<br><br>
```java
public abstract class Account
{
    //setter
    //추상메소드 setAmount(자손인 Income이나 Outgo에서 구현함)
    public abstract void setAmount(int amount);
    public void setBalance(int balance){ this.balance = balance; }
    public void setRemarks(String remarks){ this.remarks = remarks; }
}
```
setBalance와 setRemarks는 매개변수로 입력받은 값을 그대로 대입해주면 됩니다.<br><br>
remakrs야 그냥 문자열 교체이기 때문에 당연히 그냥 대입을 하면 되고, balance는 어차피 나중에 amount를 기반으로 계산한 값을 넣기 때문에 그 계산한 값이 -이면 그대로 -를 대입해주면 되고, +이면 +를 그대로 대입해서 현재 잔액이 +얼마인지 -얼마인지를 나타냅니다.<br><br>
그러나 setAmount의 경우는 다른데, Income의 amount는 +값을 가지고, Outgo의 amount는 -값을 가집니다.<br><br>
매개변수로 전달되는 amount는 항상 +값인데 Income은 이를 그대로 저장하면 되고, Outgo는 여기에 -1을 곱하여 음수로 바꿔준 다음에 amount에 저장해야 합니다.<br><br>
그래서 Income인지 Outgo인지에 따라 그 정의가 다릅니다.<br><br>
즉, setAmount는 반환형이나 매개변수 자료형, 메소드명은 모두 동일하나(선언부는 동일) 그 정의가 다르기 때문에 Account에서는 그 선언부만 선언하고, 자식클래스인 Income과 Outgo에서 각각 클래스의 특성에 맞도록 정의(구현)하도록 합니다.<br><br>
이렇게 하기 위해서 Account에서 setAmount는 **추상메소드**로 선언하여야합니다.<br><br>
# 추상메소드, 추상클래스
일반메소드는 내용(정의)없이 선언만으로 표시할 수 없는데, **추상메소드**는 이후에 자식이 자신을 구현해주기 때문에(상속받는 자식이 일반클래스라면 반드시 부모의 추상메소드를 구현해야함) 이렇게 선언부만 작성할 수 있습니다.<br><br>
그리고 **추상메소드가 1개 이상**있으면 그 클래스는 **추상클래스로 선언**을 해줘야하는데 그 이유는 추상메소드가 미완의 메소드이기 때문에 그 메소드를 가지고 있는 클래스 역시 미완의 클래스인 추상클래스로 선언을 해줘야 합니다.<br><br>
추상클래스는 본인의 객체(인스턴스)를 생성할 수 없고, 자식의 객체(인스턴스)에서만 존재할 수 있습니다.<br><br>
물론 애초에 setAmount를 Account에서 선언하지 않고, Income과 Outgo에서 바로 선언하고 정의하면 되지 뭐하러 저렇게 Account에서 선언하여 추상클래스와 추상메소드를 만들고, 번거롭게 자식클래스에서 구현을 하느냐 하는 의문이 생길 수 있습니다.<br><br>
그렇게 하는 가장 큰 이유는 **다형성과 오버라이딩** 때문입니다.<br><br>
그래서 이렇게 부모 클래스에서 선언만 해두면 나중에 굉장히 편합니다.<br><br>
이것을 증명하기 위해서는 Account에서는 한계가 있습니다ㅠㅠ<br><br>
AccountBook의 메소드에서 이를 구현하면서 **다형성과 오버라이딩의 편리함**에 대해 자세하게 설명하도록 하겠습니다.<br><br>
부디 다음 글을 꼭 참고하시길 바라며, 일단은 다음 설명으로 넘어가도록 하겠습니다.

## Account의 clone 메소드
```java
public abstract class Account
{
    //추상메소드 clone(자손인 Income이나 Outgo에서 구현함)
    public abstract Account clone();
}
```
Account클래스는 아까 말했듯이 추상클래스이기 때문에 객체를 생성할 수 없기 때문에 clone메소드를 정의할 필요는 없고, 자식들이 이를 구현할 수 있도록 선언만 해주면 됩니다.<br><br>
여기서 중요한 점은 반환형이 Account라는 점입니다.<br><br>
자식들도 clone을 구현할 때 Account로 반환을 해줘야 한다는 말이죠.<br><br>
이는 clone을 구현할 때 좀 더 자세하게 설명하도록 하겠습니다.

# Income(자식클래스)
Income클래스는 Account의 자식클래스로써 Account의 모든 것을 물려받고, 추가적으로 본인의 특성만 구현하면 됩니다.<br><br>
**public class Income extends Account** 를 통해 자신이 Account를 상속하고 있다는 것을 나타내줍니다.<br><br>
필드 멤버는 따로 두지 않고, 부모인 Account 클래스의 필드 멤버를 그대로 사용합니다.

## 생성자
생성자는 Account와 동일하게 매개변수 없는 디폴트 생성자와 매개변수를 가지는 생성자가 있습니다.
### 디폴트 생성자
```java
public class Income extends Account
{
    //디폴트 생성자
    public Income()
    {
        super();
    }
}
```
디폴트생성자에서는 그냥 super()를 호출하여 부모인 Account클래스의 디폴트생성자를 호출해줍니다.

### 매개변수를 가지는 생성자
```java
public class Income extends Account
{
    //매개변수를 가지는 생성자
    public Income(Date date, String briefs, int amount, int balance, String remarks)
    {
        super(date, briefs, amount, balance, remarks);
    }
}
```
매개변수를 가지는 생성자 역시 super(date, briefs, amount, balance, remarks)를 호출하여 부모인 Account클래스의 매개변수를 가지는 생성자를 호출해줍니다.

## setAmount 메소드 오버라이딩
```java
public class Income extends Account
{
    //setAmount 오버라이딩
    @Override
    public void setAmount(int amount)
    {
        this.amount = amount;
    }
}
```
Account의 추상메소드였던 setAmount를 Income에서 자신의 특성에 맞게 그대로 대입하도록 구현해줍니다.

## clone 메소드 오버라이딩 
```java
public class Income extends Account
{
    //clone 오버라이딩
    @Override
    public Account clone()
    {
        Date date = new Date(this.date.getYear(), this.date.getMonth().getValue(),
                this.date.getDay());
        return new Income(date, this.briefs, this.amount, this.balance, this.remarks);
    }
}
```
먼저 Date클래스의 객체를 새로 생성해줍니다.<br><br>
그리고 Income의 매개변수를 가지는 생성자를 반환하는데, 반환형이 Account인데 Income생성자를 반환하는 것이 가능할까요?<br><br>
네 가능합니다.<br><br>
**다형성**의 원리로 인해 가능합니다.

# 다형성이란?
부모클래스는 생성할 때 본인의 생성자를 이용해서 본인클래스를 생성하거나 자식클래스를 이용해서 본인을 생성하는 방법 이렇게 2가지 방법이 있습니다.<br><br>
Account의 경우는 추상클래스이기 때문에 본인의 클래스로는 본인의 객체를 생성하지 못하고, 자식의 클래스로는 객체를 생성할 수 있습니다.<br><br>
예를 들어, Account가 추상클래스가 아니라고 가정하면 Account클래스의 객체를 생성하는 방법은 2가지 입니다.
```java
Account one = new Account();
Account other = new Income();
```
즉, 자식은 생성자로 부모클래스를 생성할 수 있기 때문에 clone에서 반환형이 Account이지만 자식의 생성자인 new Income()을 반환할 수 있는 것입니다.<br><br>
이렇게 Account에서 clone을 추상메소드로 선언하고, Account로 반환값을 설정한 뒤에 자식클래스에서는 자식 클래스의 생성자를 반환하면 나중에 AccountBook에서 메소드를 구현할 때 편리함이 있습니다.<br><br>
이는 다음 글에서 자세하게 설명하도록 하겠습니다.

# Outgo(자식클래스)
Outgo클래스는 Income클래스의 구현과 원리가 동일합니다.

## 생성자
### 디폴트 생성자
```java
public class Outgo extends Account
{
    //디폴트 생성자
    public Outgo()
    {
        super();
    }
}
```

### 매개변수를 가지는 생성자
```java
public class Outgo extends Account
{
    //매개변수를 가지는 생성자
    public Outgo(Date date, String briefs, int amount, int balance, String remarks)
    {
        super(date, briefs, amount, balance, remarks);
    }
}
```

## setAmount 메소드 오버라이딩
```java
public class Outgo extends Account
{
    //setAmount 오버라이딩
    @Override
    public void setAmount(int amount)
    {
        this.amount = amount * (-1);
    }
}
```
Account의 추상메소드였던 setAmount를 Outgo에서 자신의 특성에 맞게 -1을 곱하여 음수로 만들어준 다음에 그 값을  대입하도록 구현해줍니다.

## clone 메소드 오버라이딩 
```java
public class Outgo extends Account
{
    //clone 오버라이딩
    @Override
    public Account clone()
    {
        Date date = new Date(this.date.getYear(), this.date.getMonth().getValue(),
                this.date.getDay());
        return new Outgo(date, this.briefs, this.amount, this.balance, this.remarks);
    }
}
```
Income의 clone과 원리가 동일합니다.<br><br>
차이점은 반환할 때 Outgo생성자를 반환하는 점입니다.<br><br>

# 마치며
이번 시간에는 Account클래스를 구현하면서 추상클래스, 추상메소드, 다형성, 부모와 자식간의 상속 개념에 대해 배울 수 있었습니다.<br><br>
물론 추상클래스와 추상메소드를 사용하는 이유인 다형성의 원리와 그 장점에 대해서는 설명이 미흡하였습니다.<br><br>
이에 대한 자세한 설명은 AccountBook을 구현하면서 가능할 것 같아서 다음 글로 미루도록 하겠습니다.<br><br>
다음 글에서 이것에 대해 자세하게 설명할테니 다음 글을 꼭 참고 부탁드리겠습니다.<br><br>
궁굼하시거나 잘못된 점에 대한 지적은 언제나 환영입니다^^ 