---
layout: single
title:  "Java의 LocalDate를 활용해서 나만의 Date클래스 만들기"
categories: Java
tag: [Java, 프로젝트, Date, LocalDate, Month, DayOfWeek, 오버로딩]
toc: true
toc_label: "목차"
toc_sticky: true
---

# LocalDate 클래스
LocalDate는 Java 8부터 제공하는 날짜 정보와 관련된 처리를 도와주는 유용한 라이브러리 클래스입니다.<br><br>
이전에는 Date와 Calender 클래스가 있었다고 하는데 요새는 거의 안쓴다고 하네요.<br><br>
시간의 경우 LocalTime을 사용하면 되고, 시간날짜 둘다 사용할 경우 LocalDateTime을 사용하면 됩니다.<br><br>
저는 날짜만 필요해서 이번시간에는 LocalDate만 사용하도록 하겠습니다.<br><br>
LocalDate를 사용하기 위해서는 import java.time.LocalDate;를 해주시면 됩니다.<br><br>
추가적으로 요일(DayOfWeek)클래스를 사용하기 위해 import  java.time.DayOfWeek;해주고, 달(Month)클래스를 사용하기 위해 import java.time.Month;를 해줍니다.<br><br>
이제 이를 활용하여 Java에서 제공하는 Date클래스가 아니라 나만의 Date클래스를 만들어보도록 하겠습니다.<br><br>

# Date 클래스
Java에서 제공하는 Date클래스 대신 별도로 Date클래스를 만드는 이유는 제 입맛에 맞게 커스터마이징하여 이를 나중에 가계부(AccountBook)프로젝트를 구현할 때 사용하기 위해서 입니다.<br><br>

## 인스턴스 필드 멤버
```java
import java.time.Month;
import  java.time.DayOfWeek;
import java.time.LocalDate;

public class Date
{
    //인스턴스 필드멤버
    private int year;
    private Month month;
    private int day;
    private DayOfWeek dayOfWeek;
}
```
년도를 나타내는 year, 달을 나타내는 month, 날짜를 나타내는 day, 요일을 나타내는 dayOfWeek를 인스턴스 필드멤버로 설정합니다.<br><br>
year나 day의 경우 int자료형을 사용하고, month는 위에서 import했던 Month클래스를 사용하고, dayOfWeek역시 위에서 import한 DayOfWeek클래스를 사용합니다.

## 생성자(오버로딩)
Date클래스의 생성자로는 int형 매개변수를 3개 가지는 생성자, 문자열을 매개변수로 가지는 생성자 이렇게 2가지로 **오버로딩**하여 정의하였습니다.<br><br>
매개변수가 각각 다르기 때문에 생성자도 이런식으로 오버로딩이 가능합니다.
### int형 매개변수를 3개 가지는 생성자
```java
public class Date
{
    //int형 매개변수를 3개 가지는 생성자
     public Date(int year, int month, int day)
    {
        this.year = year;
        this.month = Month.of(month);
        this.day = day;
        this.dayOfWeek = LocalDate.of(year, month, day).getDayOfWeek();
    }
}
```
year, month, day를 int자료형으로 입력받아 이를 바탕으로 Date클래스를 생성합니다.<br><br>
year와 day는 Date클래스의 인스턴스멤버인 year와 day 바로 대입하면 됩니다.<br><br>
month의 경우 매개변수로 입력받을 때는 int형으로 받았는데, Date클래스의 month의 자료형은 Month이기 때문에 변환이 필요합니다.<br><br>
그래서 Month클래스의 정적메소드인 of클래스를 사용하여 입력받은 int형 month를 Month로 변환시킨 다음에 Date클래스의 month에 저장합니다.<br><br>
dayOfWeek(요일)은 따로 매겨변수로 입력받지 않아도, '년월일'을 알면 그 값을 알아낼 수 있습니다.<br><br>
LocalDate클래스의 정적메소드인 of에 매개변수로 입력받은 int형인 year, month, day를 그대로 다시 매개변수로 넣어주면 해당날짜를 가지는 LocalDate클래스의 객체가 생성됩니다.<br><br>
이 LocalDate객체의 인스턴스메소드인 getDayOfWeek를 이용하면 해당 년월일의 요일 값을 알아낼 수 있습니다.

### 문자열을 매개변수로 가지는 생성자
```java
public class Date
{
    //문자열을 매개변수로 가지는 생성자
    public Date(String date)
    {
        LocalDate localDate = LocalDate.of(Integer.parseInt(date.substring(0,4)),
                Integer.parseInt(date.substring(4,6)),Integer.parseInt(date.substring(6,8)));
        this.year = localDate.getYear();
        this.month = localDate.getMonth();
        this.day = localDate.getDayOfMonth();
        this.dayOfWeek = localDate.getDayOfWeek();
    }
}
```
날짜를 문자열로 표현하여 매개변수로 입력받을 경우 사용하는 생성자입니다.<br><br>
예를 들어, "20220119"이런식으로 매개변수를 입력하는 경우입니다.<br><br>
date가 String클래스이기 때문에 String클래스의 인스턴스메소드인 substring을 이용합니다.<br><br>
substring(0,4)이면 '년'을 나타내고, substring(4,6)이면 '월'을 나타내고, substring(6,8)은 '일'을 나타냅니다.<br><br>
이렇게 추출한 문자열을 Integer클래스의 정적메소드인 parseInt를 사용하여 int형으로 바꿔줍니다.<br><br>
이 후 int형이 된 '년', '월', '일'을 LocalDate의 정적메소드인 of에 매개변수로 전달하여 해당 '년', '월', '일'을 가지는 LocalDate 객체를 생성합니다.<br><br>
LocalDate 객체의 인스턴스메소드인 getYear, getMonth, getDayOfMonth, getDayOfWeek를 호출하여, Date클래스의 인스턴스 필드멤버인 year, month, day, dayOfWeek에 각각 저장합니다.

## today 정적 메소드
```java
public class Date
{
    //오늘 날짜 생성
    public static Date today()
    {
        LocalDate localDate = LocalDate.now();
        return new Date(localDate.getYear(), localDate.getMonth().getValue(),
                localDate.getDayOfMonth());
    }
}
```
today 정적메소드 내부에서 LocalDate 클래스의 정적메소드인 now를 호출하여 오늘 날짜를 받아서 LocalDate 객체를 생성합니다.<br><br>
그리고 LocalDate의 인스턴스 메소드인 getYear, getMonth, getDayofMonth, getDayOfWeek를 이용하여 오늘 년도, 월, 날짜, 요일을 구해서 새로운 Date클래스 객체를 생성하여 이를 반환합니다.

## yesterday 정적 메소드
```java
public class Date
{
    //어제 날짜 생성
    public static Date yesterday()
    {
        LocalDate yesterday = LocalDate.now().minusDays(1);
        return  new Date(yesterday.getYear(), yesterday.getMonth().getValue(),
                yesterday.getDayOfMonth());
    }
}
```
yesterday 정적메소드 내부에서 LocalDate 클래스의 정적메소드인 now를 호출하여 오늘 날짜를 받아서 LocalDate 객체를 생성한 뒤에 LocalDate의 인스턴스 메소드인 minusDays에 매개변수로 1을 입력하여 어제 날짜를 가지는 LocalDate 클래스의 객체를 생성합니다.<br><br>
그리고 LocalDate의 인스턴스 메소드인 getYear, getMonth, getDayofMonth, getDayOfWeek를 이용하여 년, 월, 일, 요일을 구해서 새로운 Date클래스 객체를 생성하여 이를 반환합니다.

## tommorow 정적 메소드
```java
public class Date
{
    //내일 날짜 생성
    public static Date tomorrow()
    {
        LocalDate tomorrow = LocalDate.now().plusDays(1);
        return new Date(tomorrow.getYear(), tomorrow.getMonth().getValue(),
                tomorrow.getDayOfMonth());
    }
}
```
tommorow 정적메소드 내부에서 LocalDate 클래스의 정적메소드인 now를 호출하여 오늘 날짜를 받아서 LocalDate 객체를 생성한 뒤에 LocalDate의 인스턴스 메소드인 plusDays에 매개변수로 1을 입력하여 내일 날짜를 가지는 LocalDate 클래스의 객체를 생성합니다.<br><br>
그리고 LocalDate의 인스턴스 메소드인 getYear, getMonth, getDayofMonth, getDayOfWeek를 이용하여 년, 월, 일, 요일을 구해서 새로운 Date클래스 객체를 생성하여 이를 반환합니다.

## previousDate 메소드(오버로딩)
previousDate는 매개변수를 1개(int 자료형인 days만 입력)만 받거나 매개변수를 2개(Date클래스의 객체의 참조변수값과 int 자료형 days를 같이 매개변수로 받음)를 가지도록 정의하였습니다.
### 매개변수 1개(int 자료형 변수)를 가지는 previousDate
```java
public class Date
{
     //오늘 날짜기준으로 매개변수로 입력한 날짜만큼 이전 날짜 생성
    public static Date previousDate(int days)
    {
        LocalDate previousDate = LocalDate.now().minusDays(days);
        return new Date(previousDate.getYear(), previousDate.getMonth().getValue(),
                previousDate.getDayOfMonth());
    }
}
```
매개변수를 1개 가지는 previousDate는 오늘 날짜를 기준으로 매개변수로 입력받은 날짜만큼 minusDays를 시킨 다음 그 날짜를 기준으로 '년', '월', '일', '요일'을 구하여 이를 바탕으로 새로운 Date 클래스를 생성하여 반환합니다.
### 매개변수 2개(Date객체 참조변수값, int 자료형 변수)를 가지는 previousDate
```java
public class Date
{
    //매개변수로 입력받은 Date를 매개변수로 입력한 날짜만큼 이전 날짜 생성
    public static Date previousDate(Date date, int days)
    {
        LocalDate previousDate = LocalDate.of(date.getYear(),
                date.getMonth().getValue(), date.getDay()).minusDays(days);
        return new Date(previousDate.getYear(), previousDate.getMonth().getValue(),
                previousDate.getDayOfMonth());
    }
}
```
매개변수를 2개 가지는 previousDate는 입력 받은 date의 날짜를 기준으로 LocalDate객체를 생성한 다음에 입력 받은 days만큼 minusDays를 시킨 다음에 '년', '월', '일', '요일'을 구하여 이를 바탕으로 새로운 Date 클래스를 생성하여 반환합니다.

## nextDate 메소드(오버로딩)
nextDate 역시 매개변수를 1개(int 자료형인 days만 입력)만 받거나 매개변수를 2개(Date클래스의 객체의 참조변수값과 int 자료형 days를 같이 매개변수로 받음)를 가지도록 정의하였습니다.

### 매개변수 1개(int 자료형 변수)를 가지는 nextDate
```java
public class Date
{
    //오늘 날짜기준으로 매개변수로 입력한 날짜만큼 이후 날짜 생성
    public static Date nextDate(int days)
    {
        LocalDate nextDate = LocalDate.now().plusDays(days);
        return new Date(nextDate.getYear(), nextDate.getMonth().getValue(),
                nextDate.getDayOfMonth());
    }
}
```
매개변수를 1개 가지는 nextDate는 오늘 날짜를 기준으로 매개변수로 입력받은 날짜만큼 plusDays를 시킨 다음 그 날짜를 기준으로 '년', '월', '일', '요일'을 구하여 이를 바탕으로 새로운 Date 클래스를 생성하여 반환합니다.

### 매개변수 2개(Date객체 참조변수값, int 자료형 변수)를 가지는 nextDate
```java
public class Date
{
     //매개변수로 입력받은 Date를 매개변수로 입력한 날짜만큼 이후 날짜 생성
    public static Date nextDate(Date date, int days)
    {
        LocalDate nextDate = LocalDate.of(date.getYear(),
                date.getMonth().getValue(), date.getDay()).plusDays(days);
        return new Date(nextDate.getYear(), nextDate.getMonth().getValue(),
                nextDate.getDayOfMonth());
    }
}
```
매개변수를 2개 가지는 nextDate는 입력 받은 date의 날짜를 기준으로 LocalDate객체를 생성한 다음에 입력 받은 days만큼 plusDays를 시킨 다음에 '년', '월', '일', '요일'을 구하여 이를 바탕으로 새로운 Date 클래스를 생성하여 반환합니다.

## Date 대소비교
먼저 Date객체들끼리 대소비교를 하기 위해 기준을 세웁니다.<br><br>
당연하게도 1순위는 년도끼리 비교하여 년도가 더 크면 더 큰 Date입니다.<br><br>
년도가 서로 같으면 2순위로 월끼리 비교하여 월이 더 크면 더 큰 Date입니다.<br><br>
년도와 월이 서로 같으면 3순위로 날짜까지 비교하여 날짜가 더 크면 더 큰 Date입니다.<br><br>
년도와 월, 날짜까지 같으면 같은 Date입니다.<br><br>
이제 이 기준을 바탕으로 메소드를 구현합니다.

### isGreaterThan 메소드
```java
public class Date
{
     //Date끼리 누가 더 큰지 비교
    public boolean isGreaterThan(Date other)
    {
        boolean ret = false;
        if (this.year > other.year)
        {
            ret = true;
        }
        else if (this.year == other.year && this.month.compareTo(other.month) > 0)
        {
            ret = true;
        }
        else if (this.year == other.year && this.month == other.month && this.day > other.day)
        {
            ret = true;
        }
        return ret;
    }
}
```
선택구조(if, else if, else if)를 통해 위에서 언급한 기준대로 대소비교를 합니다.<br><br>
세가지 기준 중에 하나라도 충족하면 true이고 아니면 디폴트값인 false를 반환합니다.

### isLowerThan 메소드
```java
    //Date끼리 누가 더작은지 비교
    public boolean isLowerThan(Date other)
    {
        boolean ret = false;
        if (this.year < other.year)
        {
            ret = true;
        }
        else if (this.year == other.year && this.month.compareTo(other.month) < 0)
        {
            ret = true;
        }
        else if (this.year == other.year && this.month == other.month && this.day < other.day)
        {
            ret = true;
        }
        return ret;
    }
```
선택구조(if, else if, else if)를 통해 위에서 언급한 기준의 반대로 대소비교를 합니다.<br><br>
세가지 기준 중에 하나라도 충족하면 true이고 아니면 디폴트값인 false를 반환합니다.

### equals 메소드 오버라이딩
```java
public class Date
{
    @Override
    public boolean equals(Object obj)
    {
        boolean ret = false;
        if(obj instanceof Date)
        {
            if (this.year == ((Date)obj).year && this.month == ((Date)obj).month
                    && this.day == ((Date)obj).day)
            {
                ret = true;
            }
        }
        return ret;
    }
}
```
Object의 equals메소드를 오버라이딩하였는데, 먼저 매개변수 obj가 Object이기 때문에 Date로 다운캐스팅되는지 먼저 확인 후에 Date로 다운캐스팅이 되면 Date로 형변환을 하여 위에서 언급한대로 '년', '월', '일'이 모두 같으면 true를 반환합니다.<br><br>
형변환이 안되거나 '년', '월', '일' 중 하나라도 다르면 디폴트값인 false를 반환합니다.

## toString 메소드 오버라이딩
Object의 toString메소드를 오버라이딩하여 Date클래스의 특성에 맞게 바꿔줍니다.<br><br>
toString의 경우 나중에 콘솔창에서 메인테스트를 할 때 결과를 출력하기 위한 용도로 사용합니다.
```java
public class Date
{
    //테스트 출력용
    @Override
    public String toString()
    {
        //Month 자료형을 정수형으로 바꿔줌
        int month = this.month.getValue();
        String stringMonth;
        //month가 한 자리수이면
        if(month<10)
        {
            //앞에 0을 붙여줌
            stringMonth = new String("0" + month);
        }
        else
        {
            stringMonth = Integer.toString(month);
        }
        String stringDay;
        //day가 한자리 수이면
        if(this.day < 10)
        {
            //앞에 0을 붙여줌
            stringDay = new String("0" + this.day);
        }
        else
        {
            stringDay = Integer.toString(this.day);
        }
        return new String(this.year + "-" + stringMonth + "-" +
                stringDay + "-" + this.dayOfWeek);
    }
}
```
Date클래스의 필드멤버인 month는 자료형이 Month이기 때문에 출력을 하면 영문으로 해당하는 월이 출력됩니다.<br><br>
저는 year와 day는 숫자로 출력되는데 month만 영어로 출력되는게 싫어서 숫자로 바꿔서 출력하도록 했습니다.<br><br>
그러기 위해서 먼저 Month클래스의 인스턴스 메소드인 getValue를 통해 Month를 해당 하는 숫자로 바꿔줍니다.<br><br>
예를 들어 AUGUST는 8로 바꿔줍니다.<br><br>
이 때 숫자로 바꿔주면 10, 11, 12일 경우 2자리 숫자라 상관없지만 1~9는 한 자리 숫자라서 년도와 같이 기재할 때는 되도록이면 앞에 0을 붙여주는게 좋을 것 같아서 이에 대한 처리를 하였습니다.<br><br>
즉, month를 숫자로 바꾼 뒤에 10보다 작으면 "0 + month"로 stringMonth 문자열을 생성해주었습니다.<br><br>
이를 통해 숫자가 8인 경우 10보다 작기 때문에 stringMonth에는 0이 붙어 "08"이 저장됩니다.<br><br>
day도 month와 같은 원리를 적용합니다.<br><br>
이렇게되면 2022년 1월 1일의 경우 0이 붙어서 2022-01-01 문자열이 반환되고, 2021년 12월 31일인 경우 0이 붙지 않고 그대로 2021-12-31 문자열이 반환됩니다.

## clone 메소드 오버라이딩
```java
public class Date implements Cloneable
{
     //clone
    @Override
    public Date clone() throws CloneNotSupportedException
    {
        return (Date)super.clone();
    }
}
```
clone메소드를 구현하기 위해 먼저 clone을 오버라이딩하려는 클래스가 Cloneable 인터페이스를 implements해야 합니다.<br><br>
이후 throws CloneNotSupportedException 처리를 해주고, 내부에서는 super.clone앞에 Date클래스로 형변환한 값을 반환합니다.<br><br>
Date는 클래스 중에는 상속받은 것이 없기 때문에 자동으로 최상위 부모클래스인 Object를 상속받습니다.<br><br>
그래서 super는 Object를 가리키게 됩니다.<br><br>
Object의 clone메소드는 깊은 복사를 보장해주지 않지만 Date클래스이 경우 얕은 복사 깊은 복사를 따질 필요없이 Object의 clone을 하면 깊은 복사가 됩니다.<br><br>
왜냐하면 year와 day의 경우 int 기본자료형이라서 당연히 상관이 없고, month의 Month와 dayOfWeek의 DayOfWeek 클래스 역시 String처럼 immutable한 성격을 가지고 있어서, 값이 바뀌면 기존의 객체는 그대로 두고 새로운 객체를 생성하기 때문입니다.<br><br>
즉, Month와 DayOfWeek는 String과 같이 immutable이기 때문에 기본자료형처럼 깊은 복사, 얕은 복사를 신경쓰지 않아도 됩니다.<br><br>
이에 대한 자세한 설명은 아래 링크에 있습니다.<br>
<a href="https://injae7034.github.io/java/ninth/#string%EC%9D%98-%ED%8A%B9%EC%84%B1" target="_blank">String클래스의 특성</a>

## getter 메소드
Date의 인스턴스 필드멤버가 모두 private이기 때문에 이에 접근하여 값을 구하기 위해서는 getter메소드가 필요합니다.
```java
public class Date
{
    //getter
    public int getYear() {return this.year;}
    public Month getMonth() {return this.month;}
    public int getDay() {return this.day;}
    public DayOfWeek getDayOfWeek() {return this.dayOfWeek;}
}
```

## 테스트 코드
이제 위의 기능들이 모두 원하는대로 잘돌아가는지 확인하기 위하여 테스트 코드를 작성한 뒤에 그 결과를 확인합니다.
```java
public class Main
{
    public static void main(String[] args) throws CloneNotSupportedException
    {
        //1. 매개변수 3개를 입력하여 Date 생성
        System.out.print("1. 매개변수 3개를 입력하여 Date 생성하고 출력하고 출력하기 : ");
        Date lastDayOfLastYear = new Date(2021, 12, 31);
        System.out.println(lastDayOfLastYear);
        //2. 문자열로 날짜를 입력하여 Date 생성
        System.out.print("2. 문자열로 날짜를 입력하여 Date 생성하고 출력하기 : ");
        Date firstDayOfThisYear = new Date("20220101");
        System.out.println(firstDayOfThisYear);
        //3. 작년 마지막 날짜에 하루 더함
        System.out.print("3. 작년 마지막 날짜에 하루 더해주고 출력하기 : ");
        Date nextDate = Date.nextDate(lastDayOfLastYear, 1);
        System.out.println(nextDate);
        //4. 올해 처음 날짜에 하루 빼주기
        System.out.print("4. 올해 처음 날짜에 하루 빼주고 출력하기 : ");
        Date previousDate = Date.previousDate(firstDayOfThisYear, 1);
        System.out.println(previousDate);
        //5. 오늘 날짜 생성하기
        System.out.print("5. 오늘 날짜 생성하고 출력하기 : ");
        Date today = Date.today();
        System.out.println(today);
        //6. 어제 날짜 생성하기
        System.out.print("6. 어제 날짜 생성하고 출력하기 : ");
        Date yesterday = Date.yesterday();
        System.out.println(yesterday);
        //7. 내일 날짜 생성하기
        System.out.print("7. 내일 날짜 생성하고 출력하기 : ");
        Date tomorrow = Date.tomorrow();
        System.out.println(tomorrow);
        //8. 어제에서 이틀 이동하여 내일 날짜와 같은지 비교하기
        System.out.print("8. 어제에서 이틀 이동하여 내일 날짜와 같은지 비교하기 : ");
        System.out.println(Date.nextDate(yesterday, 2).equals(tomorrow));
        //9. 오늘이 어제보다 더 큰지 결과 출력하기
        System.out.print("9. 오늘이 어제보타 더 큰지 결과 출력하기 : ");
        System.out.println(today.isGreaterThan(yesterday));
        //10. 오늘이 내일보다 더 작은지 결과 출력하기
        System.out.print("10. 오늘이 내일보다 더 작은지 결과 출력하기 : ");
        System.out.println(today.isLowerThan(tomorrow));
        //11. 오늘이 어제랑 같은지 결과 출력하기
        System.out.print("11. 오늘이 어제랑 같은지 결과 출력하기 : ");
        System.out.println(today.equals(yesterday));
        //12. 오늘이 어제보다 작은지 결과 출력하기
        System.out.print("12. 오늘이 어제보다 작은지 결과 출력하기 : ");
        System.out.println(today.isLowerThan(yesterday));
        //13. 오늘이 어제보다 큰지 결과 출력하기
        System.out.print("13. 오늘이 어제보다 큰지 결과 출력하기 : ");
        System.out.println(today.isGreaterThan(tomorrow));
        //14. 오늘에서 17일 전으로 이동하고 결과 출력하기
        System.out.print("14. 오늘에서 17일 전으로 이동하고 결과 출력하기 : ");
        previousDate = Date.previousDate(17);
        System.out.println(previousDate);
        //15. 오늘에서 15일 이후로 이동하고 결과 출력하기
        System.out.print("15. 오늘에서 15일 이후로 이동하고 결과 출력하기 : ");
        nextDate = Date.nextDate(15);
        System.out.println(nextDate);
        //16. 오늘 날짜 복사하고 결과 출력하기
        System.out.print("16. 오늘 날짜 복사하고 결과 출력하기 : ");
        Date copyDate = today.clone();
        System.out.println(copyDate);
    }
}
```
### 결과

![테스트코드결과](../../images/2022-01-19-nineteenth/테스트코드결과.JPG)
<br><br>
위와 같이 원하는 결과가 나옴을 확인할 수 있었습니다.

# 마치며
이번 시간에는 Java의 LocalDate를 활용해서 나만의 Date클래스 만들면서 LocalDate의 기본적인 메소드들과 Month, DayOfWeek의 클래스들에서 대해서도 배울 수 있었습니다.<br><br>
이를 활용하여 다음시간부터는 가계부(AccountBook)을 구현하도록 하겠습니다.<br><br>
잘못된 점이나 궁굼한 사항이 있으시면 댓글이나 이메일로 남겨주시면 답장드리겠습니다.<br><br>
긴 글 읽어주셔서 감사합니다^^