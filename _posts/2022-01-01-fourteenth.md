---
layout: single
title:  "Java프로젝트하면서 Object 메소드 오버라이딩 개념 익히기"
categories: Java
tag: [Java, 명함철, 프로젝트, VisitingCard, Cloneable, toString, equals, 오버라이딩, 얕은 복사, 깊은 복사]
toc: true
toc_label: "목차"
toc_sticky: true
---
# 명함철 프로젝트를 시작하면서
저번까지 ArrayList를 활용하여 주소록 프로그램을 만들면서 String의 특성, ArrayList의 얕은 복사, 깊은 복사, 문자열 입출력, CUI프로그래밍, MySQL등 다양한 것들을 배웠습니다.<br><br>
이번에는 LinkedList를 활용하여 명함철 프로그램을 만들면서 공부하려고 합니다.<br><br>
명함철(VisitingCardBinder)을 만들려면 우선 명함(VisitingCard)이 있어야겠죠...<br><br>
그래서 먼저 명함 클래스를 만들려고 합니다.<br><br>
명함을 만들려고 자세히 보니 명함에는 개인(Personal)의 정보와 회사(Company)의 정보가 있음을 알 수 있습니다.<br><br>
개인의 정보와 회사의 정보를 각각 개인, 회사 레코드로 나눠서 관리하는게 더 좋겠다는 생각이 듭니다.<br><br>
그렇다면 명함을 만들기 전에 우선 개인과 회사 클래스를 먼저 만드는 게 좋겠네요.<br><br>
이번 시간에는 개인(Personal)과 Company(회사), 그리고 명함(VisitingCard)를 구현하면서 Object의 메소드를 오버라이딩해보겠습니다.

# Personal 클래스
Personal 클래스는 명함에 있는 개인의 정보들을 저장 및 관리하는 entity 클래스입니다.<br><br>
## Personal 클래스 필드
명함에 있는 개인 정보들은 name(이름), position(직책), cellularPhoneNumber(휴대폰번호), emailAddress(이메일주소)로 추려서 이들을 필드로 설정하였고 자료형은 전부 String, 접근제어자는 전부 private으로 정하여 외부에서 필드멤버에 접근할 수 없도록 하였습니다.<br><br>
```java
public class Personal
{
    //인스턴스 필드멤버
    private String name;
    private String position;
    private String cellularPhoneNumber;
    private String emailAddress;
}
```
## Personal 클래스 생성자
생성자로는 매개변수를 받지 않는 디폴트 생성자와 각 필드들을 매개변수로 입력받는 생성자를 정의했습니다.<br><br>
```java
public class Personal
{
     //디폴트 생성자
    public Personal()
    {
        this.name = "";
        this.position = "";
        this.cellularPhoneNumber = "";
        this.emailAddress = "";
    }
    //매개변수를 가지는 생성자
    public  Personal(String name, String position, String cellularPhoneNumber,
                     String emailAddress)
    {
        this.name = name;
        this.position = position;
        this.cellularPhoneNumber = cellularPhoneNumber;
        this.emailAddress = emailAddress;
    }
}
```

## clone 메소드 오버라이딩하기
```java
public class Personal implements Cloneable
{
    //Cloneable 의 clone 메소드 오버라이딩하기(구현하기)
    @Override
    public Personal clone() throws CloneNotSupportedException
    {
        return (Personal)super.clone();
    }
}
```
### clone 메소드 설명
Personal 객체를 복사를 하는 기능을 가진 Cloneable인터페이스의 clone을 구현하기 위해 Personal클래스 옆에 우선 implements Cloneable을 적어줍니다.<br><br>
clone메소드를 오버라이딩하겠다는 의미죠.<br><br>
그리고 내부에서는 Object의 clone(접근제어가 prorected라서 내부에서만 사용이 가능함)을 이용한 뒤에 마지막에 Personal로 형변환을 하여 반환하는 단순한 구조입니다.<br><br>
Personal 클래스의 모든 멤버가 String이기 때문에 String의 특성상 이렇게 해도 깊은 복사가 됩니다.<br><br>
즉, 복사본의 내용을 변경해도 원본의 String필드들의 내용은 그대로라는 말이죠.<br><br>
String은 내용을 바꾸면 바뀌기 전 내용은 그대로 두고, 바꾼 내용으로 새로운 객체를 형성하기 때문에 가능한 일입니다.<br><br>
이는 앞에서 만들었던 주소록의 Personal을 구현할 때도 이야기했던 내용이므로 좀 더 자세하게 알고 싶으신 분은 아래 링크를 참고하시면 됩니다.<br>
<a href="https://injae7034.github.io/java/ninth/#string%EC%9D%98-%ED%8A%B9%EC%84%B1" target="_blank">Java프로젝트하면서 String의 특성으로 인한 깊은 복사 설명하기</a>

## equals 메소드 오버라이딩하기
```java
public class Personal implements Cloneable
{
    //같은 Personal 객체인지 확인하기(오버라이딩하기)
    @Override
    public boolean equals(Object obj)
    {
        boolean ret = false;
        if(obj instanceof Personal)
        {
            if(this.name.compareTo(((Personal)obj).name)==0
                    && this.position.compareTo(((Personal)obj).position)==0
                    && this.cellularPhoneNumber.compareTo(((Personal)obj).cellularPhoneNumber)==0
                    && this.emailAddress.compareTo(((Personal)obj).emailAddress)==0)
            {
                ret = true;
            }
        }
        return ret;
    }
}
```
### equals 메소드 설명
Personal객체끼리 서로 같은지 비교하기 위해 Object클래스의 equals를 오버라이딩하였습니다.<br><br>
우선 **instanceof**을 이용하여 매개변수로 입력 받은 obj가 Personal로 다운캐스팅이 되는지 확인합니다.<br><br>
다운캐스팅이 된다고 하면 내부에서 obj를 Personal클래스로 형변환하여 Personal클래스의 멤버인 각 필드들(String)에 접근하여 서로 간의 멤버 내용(문자열)이 같은지 비교(compareTo)합니다.<br><br>
Personal의 모든 필드멤버의 내용이 같으면 true를 반환하고, 하나라도 다르거나 형변환이 안될경우 false를 반환합니다.<br><br>

## toString 메소드 오버라이딩하기
```java
public class Personal implements Cloneable
{
    //테스트 출력용
    @Override
    public String toString()
    {
        return new String(this.name + ", " + this.position + ", " +
                this.cellularPhoneNumber + ", " + this.emailAddress);
    }
}
```
### toString 메소드 설명
나중에 테스트를 위해 콘솔창에 출력할 때 코드중복이 심할 것 같아서 테스트 출력용으로 이용하기 위해 Object의 toString을 오버라이딩하였습니다.<br><br>
콘솔창에는 위의 코드와 같이 콤마(,)와 띄어쓰기 한칸으로 구분을 하여 출력하도록 하였습니다.

## getter메소드
```java
public class Personal implements Cloneable
{
    //getter 메소드
    //name 가져오기
    public String getName() { return this.name; }
    //position 가져오기
    public  String getPosition() { return this.position; }
    //cellularPhoneNumber 가져오기
    public String getCellularPhoneNumber() { return this.cellularPhoneNumber; }
    //emailAddress 가져오기
    public String getEmailAddress() { return  this.emailAddress; }
}
```
### getter메소드 설명
Personal의 각 필드들은 private이기 때문에 외부에서 접근을 할 수 없습니다.<br><br>
그래서 외부에서 각 필드의 내용을 가져올 수 있는 getName, getPosition, getCelluarPhoneNumber, getEmailAddress를 정의하였습니다.<br><br>

## setter 메소드
```java
public class Personal implements Cloneable
{
    //setter 메소드
    //position 변경하기
    public void setPosition(String position)
    {
        this.position = position;
    }
    //cellularPhone 변경하기
    public void setCellularPhoneNumber(String cellularPhoneNumber)
    {
        this.cellularPhoneNumber = cellularPhoneNumber;
    }
    //emailAddress 변경하기
    public void setEmailAddress(String emailAddress)
    {
        this.emailAddress = emailAddress;
    }
}
```
### setter 메소드 설명
VisitingCardBinder에서 얕은 복사, 깊은 복사 테스트를 하기 위해 별도로 만들었습니다.<br><br>
원래는 명함의 개념상 명함의 내용을 수정한다는 것이 말이 안되나(명함의 내용이 바뀌면 새로 명함을 만들기 때문에)여기서는 얕은 복사, 깊은 복사를 실험하기 위해 변경할 수 있게 메소드를 만들었습니다.<br><br>

# Company 클래스
Company 클래스는 명함에 있는 회사의 정보들을 저장 및 관리하는 entity클래스입니다.<br><br>
## Company 클래스 필드
명함에 있는 회사 정보들은 name(상호), address(주소), telephoneNumber(전화번호), faxNumber(팩스번호), url(회사홈페이지)로 추려서 이들을 필드로 설정하였고, 자료형은 전부 String, 접근제어자는 private으로 설정하여 외부에서 접근할 수 없게 만들었습니다.<br><br>
```java
public class Company
{
    //인스턴스 필드멤버
    private String name;
    private String address;
    private String telephoneNumber;
    private String faxNumber;
    private String url;
}
```
## Company 클래스 생성자
생성자로는 Personal클래스와 동일하게 매개변수를 받지 않는 디폴트 생성자와 필드들을 매개변수로 입력받는 생성자를 정의했습니다.<br><br>
```java
public class Company
{
    //디폴트생성자
    public Company()
    {
        this.name = "";
        this.address = "";
        this.telephoneNumber = "";
        this.faxNumber = "";
        this.url = "";
    }
    //매개변수를 가지는 생성자
    public Company(String name, String address, String telephoneNumber,
                   String faxNumber, String url)
    {
        this.name = name;
        this.address = address;
        this.telephoneNumber = telephoneNumber;
        this.faxNumber = faxNumber;
        this.url = url;
    }
}
```

## clone 메소드
```java
public class Company implements Cloneable
{
    //Cloneable 의 clone 메소드 오버라이딩하기(구현하기)
    @Override
    public Company clone() throws CloneNotSupportedException
    {
        return (Company)super.clone();
    }
}
```
위의 Personal클래스와 동일한 원리라서 설명을 생략하도록 하겠습니다.

## equals 메소드
```java
public class Company implements Cloneable
{
    //같은 Company 객체인지 확인하기(오버라이딩하기)
    @Override
    public boolean equals(Object obj)
    {
        boolean ret = false;
        if(obj instanceof Company)
        {
            if(this.name.compareTo(((Company)obj).name)==0
                    && this.address.compareTo(((Company)obj).address)==0
                    && this.telephoneNumber.compareTo(((Company)obj).telephoneNumber)==0
                    && this.faxNumber.compareTo(((Company)obj).faxNumber)==0
                    && this.url.compareTo(((Company)obj).url)==0)
            {
                ret = true;
            }
        }
        return ret;
    }
}
```
위의 Personal클래스와 동일한 원리라서 설명을 생략하도록 하겠습니다.

## toString 메소드
```java
public class Company implements Cloneable
{
    //테스트 출력용
    @Override
    public String toString()
    {
         return new String(this.name + ", " + this.address + ", " +
                this.telephoneNumber + ", " + this.faxNumber + ", " + this.url);
    }
}
```
위의 Personal클래스와 동일한 원리라서 설명을 생략하도록 하겠습니다.

## getter메소드
```java
public class Company implements Cloneable
{
    //getter 메소드
    //name 가져오기
    public String getName() { return this.name; }
    //address 가져오기
    public  String getAddress() { return this.address; }
    //telephoneNumber 가져오기
    public String getTelephoneNumber() { return this.telephoneNumber; }
    //faxNumber 가져오기
    public String getFaxNumber() { return  this.faxNumber; }
    //url 가져오기
    public  String getUrl() { return  this.url; }
}
```
위의 Personal클래스와 동일한 원리라서 설명을 생략하도록 하겠습니다.

## setter 메소드
```java
public class Company implements Cloneable
{
    //setter 메소드
    //address 변경하기
    public void setAddress(String address)
    {
        this.address = address;
    }
    //telephoneNumber변경하기
    public void setTelephoneNumber(String telephoneNumber)
    {
        this.telephoneNumber = telephoneNumber;
    }
    //faxNumber 변경하기
    public void setFaxNumber(String faxNumber)
    {
        this.faxNumber = faxNumber;
    }
    //url 변경하기
    public void setUrl(String url)
    {
        this.url = url;
    }
}
```
위의 Personal클래스와 동일한 원리라서 설명을 생략하도록 하겠습니다.

# VisitingCard 클래스
이제 Personal과 Company클래스를 구현했으니 본격적으로 VisitingCard 클래스를 구현할 차례입니다.<br><br>
VisitingCard클래스는 개인(Personal)과 회사(Company)의 정보를 관리하는 entity클래스입니다.
## VisitingCard 클래스 필드
VisitingCard 클래스는 Personal과 Company를 멤버로 가집니다.<br><br>
멤버로 가지되 이너클래스로 가지는 것이 아니라 Personal과 Company의 참조변수(주소)값을 멤버로 가집니다.<br><br>
(지금와서 생각해보니 이너클래스로 가지는 것이 더 나은거 같기도 하네요;;)
```java
public class VisitingCard
{
    //인스턴스 필드멤버
    private Personal personal;
    private Company company;
}
```
## VisitingCard 클래스 생성자
생성자로는 매개변수가 없는 디폴트 생성자, 이미 생성된 Personal과 Company를 매개변수로 하는 생성자, 생성자 내부에서 Personal과 Company를 생성시키위 위해 각각의 필드 9개를 매개변수로 하는 생성자를 정의하였습니다.<br>
<br>

```java
public class VisitingCard
{
    //디폴트 생성자
    public VisitingCard()
    {
        this.personal = new Personal();
        this.company = new Company();
    }
    //매개변수를 2개 가지는 생성자
    public VisitingCard(Personal personal, Company company)
    {
        this.personal = personal;
        this.company = company;
    }
    //매개변수를 9개 가지는 생성자
    public  VisitingCard(String personalName, String position, String cellularPhoneNumber,
                         String emailAddress, String companyName, String address,
                         String telephoneNumber, String faxNumber, String url)
    {
        this.personal = new Personal(personalName, position, cellularPhoneNumber,
                emailAddress);
        this.company = new Company(companyName, address, telephoneNumber,
                faxNumber, url);
    }
}
```

## clone 메소드
clone메소드의 경우 앞의 Personal, Company클래스와 동일한 원리로 구현하였습니다.<br><br>
```java
public class VisitingCard implements Cloneable
{
    //Cloneable 의 clone 메소드 오버라이딩하기(구현하기)
    @Override
    public VisitingCard clone() throws CloneNotSupportedException
    {
        return (VisitingCard) super.clone();
    }
}
```
### clone 메소드 문제점 : 얕은 복사
하지만... 나중에 이렇게 하면 **문제가 생긴다**는 것을 깨달았습니다...<br><br>
위의 방식으로 visitingCard의 clone메소드를 정의하면 내부에서 Personal과 Company클래스의 clone은 호출되지 않습니다.<br><br>
위의 코드대로라면 Object클래스의 clone메소드가 호출되어, 새로운 VisitingCard공간이 만들어지지만 그 멤버인 Personal과 Company의 경우 원본의 참조변수(주소)값만을 단순하게 복사한 다음 복사본을 반환합니다.<br><br>
즉, 얕은 복사가 됩니다.<br><br>
위의 방식으로 원본을 복사한 뒤에 복사본의 VisitingCard 객체에서 Personal 또는 Company 클래스의 객체 내용을 변경하면 원본의 VisitingCard 객체의 Personal 또는 Company 클래스의 객체 내용 역시 변경되어 버립니다ㅠ<br><br>
이 땐 몰랐는데 나중에 VisitingCardBinder에서 깊은 복사 테스트를 해보다가 잘못된 것을 알게 되었습니다ㅠ<br><br>
그래서 해당 코드를 수정하였는데, 다음과 같습니다.


### clone 메소드 수정하기 : 깊은 복사
```java
public class VisitingCard implements Cloneable
{
    //Cloneable 의 clone 메소드 오버라이딩하기(구현하기)
    @Override
    public VisitingCard clone() throws CloneNotSupportedException
    {
        return new VisitingCard(this.personal.clone(), this.company.clone());
    }
}
```
VisitingCard를 새로 생성하면서 생성자의 매개변수로 Persoanal과 Company객체의 clone메소드를 전달합니다.<br><br>
Personal과 Company객체의 clone을 통해 반환되는 복사본들은 원본과 내용은 동일하지만 새로 생성을 했기 때문에 주소값이 다릅니다.<br><br>
즉, Personal과 Company객체의 clone을 통해 반환되는 복사본은 원본과는 별도의 공간을 가지고 있는 별개의 객체입니다.<br><br>
return으로 반환되는 복사본인 VisitingCard는 이제 원본과는 별도의 Personal객체와 Company를 가지게 되어서, 복사본의 Personal 또는 Company객체의 내용을 변경하여도 원본의 내용은 바뀌지 않습니다.<br><br>
즉, 성공적으로 깊은 복사가 되었습니다.<br><br>
이에 대한 자세한 설명은 후속편인 VisitingCardBinder를 구현할 때 메모리맵을 첨부하여 설명하도록 하겠습니다.<br><br>
아래 링크가 후속편 링크이니 이를 참고하시면 됩니다.<br>
<a href="https://injae7034.github.io/java/fifteenth/#clone" target="_blank">Java프로젝트하면서 LinkedList에서 얕은 복사 깊은 복사 설명하기</a>

## equals 메소드 
```java
public class VisitingCard implements Cloneable
{
    //같은 VisitingCard 인지 확인하기(오버라이딩하기)
    @Override
    public boolean equals(Object obj)
    {
        boolean ret = false;
        if(obj instanceof VisitingCard)
        {
            if(this.personal.equals(((VisitingCard)obj).personal) == true
                && this.company.equals(((VisitingCard)obj).company) == true)
            {
                ret = true;
            }
        }
        return ret;
    }
}
```
### equals 메소드 설명
VisitingCard의 equals는 메소드 내부에서 VistingCard의 멤버인 Personal클래스와 Company클래스의 equals 메소드를 호출하여 Personal과 Company 객체가 모두 같으면 true이고, 다운캐스팅이 안되거나 Personal과 Company객체 중 하나라도 다르면 false를 반환하도록 구현했습니다.<br><br>

## toString 메소드
```java
public class VisitingCard implements Cloneable
{
    //테스트 출력용
    @Override
    public String toString()
    {
        return new String("개인 -> " + this.personal.toString() + "\n" +
                            "회사 -> " + this.company.toString());
    }
}
```

### toString 메소드 설명
테스트 할 시 중복된 코드를 최소화하기 위해 정의했습니다.<br><br>
여기서도 메소드 내부에서 Personal, Company클래스의 toString을 호출합니다.<br><br>

## getter메소드
```java
public class VisitingCard implements Cloneable
{
    //personal getter 메소드
    //getPersonalName
    public String getPersonalName() { return this.personal.getName(); }
    //getPosition
    public String getPosition() { return this.personal.getPosition(); }
    //getCellularPhoneNumber
    public String getCellularPhoneNumber() { return this.personal.getCellularPhoneNumber(); }
    //getEmailAddress
    public String getEmailAddress() { return this.personal.getEmailAddress(); }

    //company getter 메소드
    //getCompanyName
    public String getCompanyName() { return this.company.getName(); }
    //getAddress
    public String getAddress() { return this.company.getAddress(); }
    //getTelephoneNumber
    public String getTelephoneNumber() { return this.company.getTelephoneNumber(); }
    //getFaxNumber
    public String getFaxNumber() { return this.company.getFaxNumber(); }
    //getUrl
    public String getUrl() { return this.company.getUrl(); }
}
```
### getter 메소드 설명
VisitngCard의 멤버인 Personal과 Company의 멤버의 값을 가져올 수 있도록 9개의 getter메소드를 정의하였습니다.<br><br>각 getter메소드는 내부에서 Personal과 Company의 getter메소드를 호출합니다.

## setter 메소드
```java
public class VisitingCard implements Cloneable
{
    //persoanl setter 메소드
    //position 변경하기
    public void setPosition(String position)
    {
        this.personal.setPosition(position);
    }
    //cellularPhoneNumber 변경하기
    public void setCellularPhoneNumber(String cellularPhoneNumber)
    {
        this.personal.setCellularPhoneNumber(cellularPhoneNumber);
    }
    //emailAddress 변경하기
    public void setEmailAddress(String emailAddress)
    {
        this.personal.setEmailAddress(emailAddress);
    }

    //company setter 메소드
    //address 변경하기
    public void setAddress(String address)
    {
        this.company.setAddress(address);
    }
    //telephoneNumber 변경하기
    public  void setTelephoneNumber(String telephoneNumber)
    {
        this.company.setTelephoneNumber(telephoneNumber);
    }
    //faxNumber 변경하기
    public void setFaxNumber(String faxNumber)
    {
        this.company.setFaxNumber(faxNumber);
    }
    //url 변경하기
    public void setUrl(String url)
    {
        this.company.setUrl(url);
    }
}
```

### setter 메소드 설명
VisitngCard의 멤버인 Personal과 Company의 멤버에서 개인의 성명과 상호명을 제외한 값을 수정할 수 있도록 7개의 setter메소드를 정의하였습니다.<br><br>
각 setter메소드는 내부에서 Personal과 Company의 setter메소드를 호출합니다.

# 테스트 시나리오
이제 Personal, Company, VistingCard메소드의 클래스들이 잘 작동하는지 테스트를 해보도록 하겠습니다.

## Personal 클래스 테스트
```java
public class Main
{
    public static void main(String[] args) throws CloneNotSupportedException
    {
         //Personal 클래스 테스트
        System.out.println("Personal 클래스 테스트");
        //1. Personal 디폴트 생성자
        System.out.println("1. Personal 디폴트 생성자");
        Personal personalOne = new Personal();
        System.out.println(personalOne);
        //2. 매개변수를 가지는 생성자
        System.out.println("2. 매개변수를 가지는 생성자");
        Personal personalTwo = new Personal("홍길동", "대리",
                "01099840345", "Hong@naver.com");
        System.out.println(personalTwo);
        //3. personalOne과 personalTwo비교하기
        System.out.println("3. personalOne과 personalTwo 비교하기");
        System.out.println(personalOne.equals(personalTwo));
        //4. clone으로 복사하기
        System.out.println("4. clone으로 복사하기");
        personalOne = personalTwo.clone();
        System.out.println(personalOne);
        //5.personalOne과 personalTwo비교하기
        System.out.println("5. personalOne과 personalTwo 비교하기");
        System.out.println(personalOne.equals(personalTwo));
    }
}
```
첫번째는 디폴트 생성자로 생성했기 때문에 출력을 하면 공란과 콤마들만 출력이 될 것입니다.<br><br>
두번째는 매개변수를 가지는 생성자로 생성했기 때문에 입력한 매개변수대로 출력이 될 것입니다.<br><br>
세번째는 비어있는 Personal객체와와 내용이 있는 Personal객체를 equal로 비교하면 false가 출력될 것입니다.<br><br>
네번째는 내용이 있는 Personal객체의 clone메소드를 통해 내용이 비어있는 클래스에 내용을 복사하였으므로, clone이후 비어있는 Personal에 내용이 복사되었기 때문에 출력을 하면 내용이 출력이 될 것입니다.<br><br>
다섯번째는 원본과 복사본이 같은지 비교하는 것인데 복사본이 원본의 내용을 똑같이 가지고 있기 때문에 true가 출력될 것입니다.<br><br>
그럼 위 프로그램을 실행시켜 콘솔창에서 결과를 확인해보도록 합시다.<br><br>
![Personal클래스테스트결과](../../images/2022-01-01-fourteenth/Personal클래스테스트결과.JPG)
<br><br>
테스트 결과 Persoanl클래스의 구현이 제대로 되었음을 확인할 수 있었습니다.<br><br>

## Company 클래스 테스트
```java
public class Main
{
    public static void main(String[] args) throws CloneNotSupportedException
    {
        //Company 클래스 테스트
        System.out.println("Company 클래스 테스트");
        //1. Company 디폴트 생성자
        System.out.println("1. Company 디폴트 생성자");
        Company companyOne = new Company();
        System.out.println(companyOne);
        //2. 매개변수를 가지는 생성자
        System.out.println("2. 매개변수를 가지는 생성자");
        Company companyTwo = new Company("삼성전자", "서울시 서초구",
                "023345678", "023345679", "samsung.com");
        System.out.println(companyTwo);
        //3. personalOne과 personalTwo비교하기
        System.out.println("3. companyOne companyTwo 비교하기");
        System.out.println(companyOne.equals(companyTwo));
        //4. clone으로 복사하기
        System.out.println("4. clone으로 복사하기");
        companyOne = companyTwo.clone();
        System.out.println(companyOne);
        //5.personalOne과 personalTwo비교하기
        System.out.println("5. companyOne companyTwo 비교하기");
        System.out.println(companyOne.equals(companyTwo));
    }
}
```
Company클래스의 테스트는 Persoanl클래스의 테스트와 동일합니다.<br><br>
결과를 확인해보면<br><br>
![Company클래스테스트결과](../../images/2022-01-01-fourteenth/Company클래스테스트결과.JPG)
<br><br>
Company클래스 잘 구현된 것을 확인할 수 있습니다.<br><br>

## VisitingCard 클래스 테스트
```java
public class Main
{
    public static void main(String[] args) throws CloneNotSupportedException
    {
        //VisitingCard 클래스 테스트
        System.out.println("VisitingCard 클래스 테스트");
        //1. VisitingCard 디폴트 생성자
        System.out.println("1. VisitingCard 디폴트 생성자");
        VisitingCard visitingCardOne = new VisitingCard();
        System.out.println(visitingCardOne);
        //2. 매개변수를 2개 가지는 생성자
        System.out.println("2. 매개변수를 2개 가지는 생성자");
        VisitingCard visitingCardTwo = new VisitingCard(personalTwo, companyTwo);
        System.out.println(visitingCardTwo);
        //3. 매개변수를 9개 가지는 생성자
        System.out.println("3. 매개변수를 9개 가지는 생성자");
        VisitingCard visitingCardThree = new VisitingCard(personalOne.getName(),
                personalOne.getPosition(), personalOne.getCellularPhoneNumber(),
                personalOne.getEmailAddress(), companyOne.getName(),
                companyOne.getAddress(), companyOne.getTelephoneNumber(),
                companyOne.getFaxNumber(), companyOne.getUrl());
        System.out.println(visitingCardThree);
        //4. visitingCardOne과 visitringCardTwo 비교하기
        System.out.println("4. visitingCardOne과 visitringCardTwo 비교하기");
        System.out.println(visitingCardOne.equals(visitingCardTwo));
        //5. clone으로 복사하기
        System.out.println("5. clone으로 복사하기");
        visitingCardOne = visitingCardTwo.clone();
        System.out.println(visitingCardOne);
        //6. visitingCardOne과 visitringCardTwo 비교하기
        System.out.println("6. visitingCardOne과 visitringCardTwo 비교하기");
        System.out.println(visitingCardOne.equals(visitingCardTwo));
    }
}
```
VisitingCard 테스트 역시 Persoanl, Company클래스들의 테스트와 동일합니다.<br><br>
결과를 확인해보면<br><br>
![VisitingCard클래스테스트결과](../../images/2022-01-01-fourteenth/VisitingCard클래스테스트결과.JPG)
<br><br>
VisitingCard클래스 잘 구현된 것을 확인할 수 있습니다.

# 마치며
이번 시간에는 **명함철 프로젝트를 진행하면서 entity클래스인 Personal, Company, VisitingCard클래스를 구현**하였습니다.<br><br>
이 과정에서 **Object의 clone, equals, toString을 오버라이딩**하였습니다.<br><br>
다음 시간에는 아까 VisitingCard클래스의 clone을 구현할 때 잠깐 설명하였던 얕은 복사와 깊은 복사와 관련하여 
**"Java프로젝트하면서 LinkedList에서 얕은 복사 깊은 복사 설명하기"** 작성하도록 하겠습니다.<br><br>  
궁굼한 사항이나 틀린 사항은 댓글을 남겨주시거나 이메일을 보내주시면 확인하고, 답하도록 하겠습니다.<br><br>
긴 글 읽어주셔서 감사합니다^^