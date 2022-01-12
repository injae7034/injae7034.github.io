---
layout: single
title:  "Java프로젝트하면서 테스트하기 위해 콘솔을 이용한 CUI Programming 구현하기"
categories: Java
tag: [Java, 명함철, 프로젝트, VisitingCardBinder, CUI]
toc: true
toc_label: "목차"
toc_sticky: true
---

# CUI Programming
자바입출력이 잘돌아가는지 제대로 확인하기 위해 CUI Programming을 구현하였습니다.<br><br>
주소록은 배열이라서 CUI에서도 테이블형식을 차용하였지만 명함철의 경우 연결리스트를 활용하고 있기 때문에 테이블형식은 지양하고, 연결리스트의 연결의 특성를 살려서 한 화면에 하나의 명함정보를 출력하고, 이를 이동(처음, 이전, 다음, 마지막)하여 명함을 볼 수 있도록 구현할 것입니다.<br><br>

# Main클래스의 main메소드
```java
package VisitingCardBinder;

import java.io.IOException;
import java.util.ArrayList;
import java.util.Scanner;

public class Main
{
    private static VisitingCardBinder visitingCardBinder;
    public static void main(String[] args) throws IOException
    {
        //메인테스트 시나리오
        //1. VisitingCardBinder 객체를 생성한다.
        visitingCardBinder = new VisitingCardBinder();
        //2. 외부파일이 있으면 외부파일에 있는 Personal과 Company 객체정보를 load한다.
        visitingCardBinder.load();
        //3. 메뉴화면을 출력한다.
        displayMenu();
        //4. 콘솔창에서 menu 번호를 입력을 받는다.
        Scanner scanner = new Scanner(System.in);
        int menuNumber = scanner.nextInt();
        //5. 입력한 menu 번호로 이동한다.
        while (menuNumber != 0)
        {
            switch (menuNumber)
            {
                case 1: formFoTakingIn(); break;
                case 2: formFoFinding(); break;
                case 3: formFoTakingOut(); break;
                case 4: visitingCardBinder.arrange(); break;
                case 5: visitingCardBinder.first(); break;
                case 6: visitingCardBinder.previous(); break;
                case 7: visitingCardBinder.next(); break;
                case 8: visitingCardBinder.last(); break;
                default: break;
            }
            displayMenu();
            menuNumber = scanner.nextInt();
        }
        visitingCardBinder.save();
    }
}
```

## main메소드 설명
Main클래스는 정적멤버로 VisitingCardBinder의 참조변수값을 멤버로 가집니다.<br><br>
그리고 정적매소드인 main에서 VisitingCardBinder를 생성하여 Main클래스의 정적멤버에 그 참조변수(주소)값을 저장합니다.<br><br>
이 후 load메소드를 통해 외부 텍스트 파일에서 데이터를 읽어들여 명함들을 생성하고, 이를 명함철에 끼웁니다.<br><br>load메소드 이후에는 외부 텍스트 파일에 저장된 데이터가 있을 경우에는 명함철에 명함이 끼어져 있을 것이고, 외부 텍스트 파일이 데이터없이 비어있거나 파일 자체가 없을 경우에는 명함철 역시 비어있을 겁니다.<br><br>
이 후 Main클래스의 정적메소드인 displayMenu메소드를 호출하여 메뉴화면을 콘솔창에 출력합니다.<br><br>
**displayMenu의 경우 메뉴창과 현재 명함(VisitingCardBinder의 필드멤버인 current)의 정보가 콘솔창에 출력**될 것입니다.<br><br> 이 출력된 콘솔창을 참고로 하여, 콘솔창에서 menu 번호를 입력을 받습니다.<br><br>
사용자로부터 콘솔창에서 입력을 받기 위해 Scanner클래스를 생성해주고, Scanner의 메소드 nextInt를 통해 사용자가 입력한 숫자를 읽어들입니다.<br><br>
사용자가 1번을 누르면 끼우기 화면(formFoTakingIn)이 출력될 것입니다.<br><br>
2번을 누르면 찾기화면(formFoFinding)이 출력될 것입니다.<br><br>
3번을 누르면 꺼내기화면(formFoTakingOut)을 출력할 것입니다. 즉, 총 화면 구성은 displayMenu까지 포함하여 4가지입니다.<br><br>
4번을 누르면 명함철에 있는 명함을 개인의 성명을 기준으로 하여 오름차순으로 정렬하는 arrage메소들 호출하고, displayMenu에서 arrange메소드 이후의 결과를 보여줍니다.<br><br>즉, 다른 화면이 출력되는 것이 아니고, displayMenu에서 결과만 바뀌는 것(출력되는 명함의 정보가 바뀜)입니다.<br><br>
5번을 누르면 first메소드를 호출하여 현재 명함(current)이 처음 명함으로 바뀔 것이고, displayMenu에서 처음 명함이 콘솔 창에 출력될 것입니다.<br><br>
6번은 이전, 7번은 다음, 8번은 displayMenu내에서 마지막 명함이 출력될 것이고, 0번을 누르면 반복문을 빠져나오고, save메소드를 호출하여 명함철의 명함 정보를 외부텍스트 파일에 저장하고, 프로그램이 종료될 것입니다.<br><br>
0번을 누르지 않는 한(사용자가 프로그램을 종료시키지 않으면)에는 다른 화면이 출력된 이후에도 while반복문을 빠져나가지 않기 때문에 계속해서 displayMenu화면이 출력되고 사용자로부터 번호를 입력받게 될 것입니다. 

# Main클래스의 displayMenu
```java
public class Main
{
    public static void displayMenu()
    {
        System.out.println("\t명함철");
        System.out.println("\t==========================================");
        System.out.println("\t<명함>");
        System.out.println("\t------------------------------------------");
        System.out.println("\t(개인정보)");
        if(visitingCardBinder.getCurrent() != null)
        {
            System.out.printf("\t성명: %s\n", 
            visitingCardBinder.getCurrent().getPersonalName());
            System.out.printf("\t직책: %s\n", 
            visitingCardBinder.getCurrent().getPosition());
            System.out.printf("\t휴대폰번호: %s\n", 
            visitingCardBinder.getCurrent().getCellularPhoneNumber());
            System.out.printf("\t이메일주소: %s\n", 
            visitingCardBinder.getCurrent().getEmailAddress());
        }
        else
        {
            System.out.println("\t성명: ");
            System.out.println("\t직책: ");
            System.out.println("\t휴대폰번호: ");
            System.out.println("\t이메일주소: ");
        }
        System.out.println("\t------------------------------------------");
        System.out.println("\t(회사정보)");
        if(visitingCardBinder.getCurrent() != null)
        {
            System.out.printf("\t상호명: %s\n", 
            visitingCardBinder.getCurrent().getCompanyName());
            System.out.printf("\t주소: %s\n", 
            visitingCardBinder.getCurrent().getAddress());
            System.out.printf("\t전화번호: %s\n", 
            visitingCardBinder.getCurrent().getTelephoneNumber());
            System.out.printf("\t팩스번호: %s\n", 
            visitingCardBinder.getCurrent().getFaxNumber());
            System.out.printf("\t홈페이지주소: %s\n", 
            visitingCardBinder.getCurrent().getUrl());
        }
        else
        {
            System.out.println("\t상호명: ");
            System.out.println("\t주소: ");
            System.out.println("\t전화번호: ");
            System.out.println("\t팩스번호: ");
            System.out.println("\t홈페이지주소: ");
        }
        System.out.println("\t==========================================");
        System.out.printf("\t[1] 끼 우 기\t\t").printf("\t[2] 찾   기\n");
        System.out.printf("\t[3] 꺼 내 기\t\t").printf("\t[4] 정리하기\n");
        System.out.printf("\t[5] 처    음\t\t").printf("\t[6] 이   전\n");
        System.out.printf("\t[7] 다    음\t\t").printf("\t[8] 마 지 막\n");
        System.out.println("\t[0] 명 함 철 프 로 그 램 종 료 하 기");
        System.out.println("\t------------------------------------------");
        System.out.printf("\t메뉴를 선택하십시오! ");
    }
}
```
## displayMenu 설명
현재 명함이 없으면(명함철에 명함 개수가 0이면) 비어있는 상태로 출력을 하고, 현재 명함이 있으면(명함철에 명함 개수가 1개 이상이면) getCurrent메소드를 통해 현재 명함을 구하고, 각 필드의 getter메소드를 통해 필드 정보를 구한 뒤 그 내용들을 출력하고(현재 명함을 출력하고), 그 아래에 메뉴창을 출력합니다.<br><br>
현재 명함이 없는 경우는 아래와 같습니다.<br>
![비어있는경우displayMenu화면](../../images/2022-01-07-seventeenth/비어있는경우displayMenu화면.jpg)
<br><br>
현재 명함이 있는 경우는 아래와 같습니다.<br>

![명함이있는경우displayMenu화면](../../images/2022-01-07-seventeenth/명함이있는경우displayMenu화면.jpg)
<br><br>

# Main클래스의 formFoTakingIn
```java
public class Main
{
    //[1] 끼우기
    public static void formFoTakingIn() throws IOException
    {
        //사용자로부터 콘솔창에 입력을 받기 위해 Scanner 생성하기
        Scanner scanner = new Scanner(System.in);
        String going = "Y";
        while (going.equals("Y") == true || going.equals("y") == true)
        {
            System.out.println();
            System.out.printf("\t명함철 - 끼 우 기\n");
            System.out.printf("\t==============================================\n");
            System.out.printf("\t성명 : ");
            String personalName = scanner.nextLine();
            System.out.printf("\t직책 : ");
            String position = scanner.nextLine();
            System.out.printf("\t휴대폰번호 : ");
            String cellularPhoneNumber = scanner.nextLine();
            System.out.printf("\t이메일주소 : ");
            String emailAddress = scanner.nextLine();
            System.out.printf("\t상호명 : ");
            String companyName = scanner.nextLine();
            System.out.printf("\t주소 : ");
            String address = scanner.nextLine();
            System.out.printf("\t전화번호 : ");
            String telephoneNumber = scanner.nextLine();
            System.out.printf("\t팩스번호 : ");
            String faxNumber = scanner.nextLine();
            System.out.printf("\t홈페이지주소 : ");
            String url = scanner.nextLine();
            System.out.printf("\t----------------------------------------------\n");
            System.out.printf("\t명함을 끼우시겠습니까?(Y/N) ");
            String recording = scanner.nextLine();
            if (recording.equals("Y") == true || recording.equals("y") == true)
            {
                //명함을 생성한다.
                visitingCard = new VisitingCard(personalName, position,
                        cellularPhoneNumber, emailAddress, companyName, address,
                        telephoneNumber, faxNumber, url);
                //명함을 끼운다.
                visitingCard = visitingCardBinder.takeIn(visitingCard);
                //반복문을 벗어난다.
                break;
            }
            System.out.printf("\t============================================================================\n");
            System.out.printf("\t계속하시겠습니까?(Y/N) ");
            going = scanner.nextLine();
        }
    }
}
```

## formFoTakingIn 설명
먼저 사용자로부터 콘솔창에 입력을 받기 위해 Scanner를 생성합니다.<br><br>
이 후 going 문자열의 초기값을 "Y"로 하여 while 반복문의 조건을 만족시킨 다음 while반복문 내부로 들어갑니다.<br><br>
Scanner클래스의 nextLine메소드를 통해 사용자로부터 문자열을 입력받습니다.<br><br>nextLine 메소드는 사용자가 자신이 원하는 문자열을 입력하고, 엔터키를 쳐야 다음으로 넘어갑니다.<br><br>
즉, 성명을 입력하는 칸에서 멈춰있다가 사용자가 문자열을 입력하고 엔터키를 치면 다음으로 직책을 입력하는 란이 콘솔창에 출력됩니다.<br><br>
먼저 성명을 입력하기 전에 콘솔창입니다.<br>
![끼우기성명입력창](../../images/2022-01-07-seventeenth/끼우기성명입력창.jpg)
<br><br>
다음은 성명을 입력한 후에 엔터키를 치면 직책입력창이 출력되는 콘솔창입니다.<br>

![끼우기직책입력창](../../images/2022-01-07-seventeenth/끼우기직책입력창.jpg)
<br><br>
이런식으로 개인의 정보와 회사의 정보를 다 입력하고 엔터키를 치면 이 정보를 명함철에 끼울 지 여부를 묻는 질문이 출력됩니다.<br>

![끼우기전화면](../../images/2022-01-07-seventeenth/끼우기전화면.jpg)
<br><br>
여기서 사용자가 "Y"또는 "y"를 누르면 앞에서 nextLine을 통해 읽은 9가지 필드를 매개변수로 하여 VisitingCard객체를 생성하여 VisitingCardBinder에 takeIn합니다.<br><br>
그리고 반복문을 벗어날 수 있도록 break가 실행됩니다.<br><br>
break가 실행되면 formFoTakingIn의 반복문을 빠져나가기 때문에 displayMenu함수가 호출되고, displayMenu창이 콘솔 창에 출력될 때는 takeIn에서 새로 끼운 명함이 현재 명함으로 출력됩니다.<br><br>(앞의 VisitingCardBinder 게시글을 보시면 takeIn내부에서 현재 명함을 새로 끼운 명함으로 변경해주기 때문입니다.)<br><br>
만약에 사용자가 "Y" 또는 "y"이외의 키를 누르면, 즉, 콘솔창에 입력한 정보를 명함철에 끼우지 않는다면 계속할지 여부를 묻는 질문이 출력되고, 여기서 "Y" 또는 "y"를 누르면 formFoTakingIn이 처음부터 다시 실행되고(while반복구조에 의해서) 다른 키를 누르면 formFoTakingIn은 종료되고 다시 displayMeny창이 출력됩니다.<br><br>

# formFoTakingIn 사용자 친화적으로 개선하기
현재 formFoTakingIn에서 사용자의 편의를 위해 개선할 수 있는 방법이 없을까요?<br><br>
현재 formFoTakingIn에서는 무엇이 불편할까요?<br><br>
바로 명함의 필드가 9개라서 이를 일일이 다 입력을 하려면 힘들다는 점입니다.<br><br>
물론 명함의 정보를 입력하려면 다 입력하긴 해야하는데 사용자입장에서 9개를 다치려니 조금 힘들 수 있습니다.<br><br>
그런 사용자를 위해 프로그래머가 조금 도와주는 기능을 구현할 수 있습니다.<br><br>
어떻게요? 바로 회사 정보는 중복이 있을 수도 있다는 점을 착안하면 가능합니다.<br><br>
즉, 개인의 정보는 중복이 있을 수 없기 때문에 사용자가 반드시 다 입력을 해야하지만, 회사의 정보는 이미 명함철의 다른 명함에 같은 회사의 정보가 있을 수도 있습니다.<br><br>즉, 이미 다른 명함철에 현재 입력하려는 회사의 정보가 있으면 사용자가 입력하지 말고, 프로그래머가 이를 대신 입력하도록 해주는 것입니다.<br><br>
그러면 formFoTakingIn에서 회사정보를 입력할 때 어떻게 명함철에 중복되는 회사 정보가 있는지 알 수 있을까요?
<br><br>
사용자가 상호명을 입력한 후에 그 상호명을 바탕으로 회사 정보를 명함철에서 찾으면 되지 않을까요?<br><br>
왜냐하면 상호명은 중복이 있을 수 없기 때문입니다.("삼성전자"라는 회사가 2개가 있을 수는 없겠죠;;)<br><br>
현재 저희가 가지고 있는 VisitngCardBinder의 메소드로는 할 수 없습니다.<br><br>그래서 새로운 메소드인 findCompanyName을 만들어 구현합니다.

## findCompanyName
```java
public class VisitingCardBinder implements Cloneable
{
    //findCompanyName(명함철에 해당 상호명이 있는지 확인)(상호명은 중복이 있을 수 없음)
    public VisitingCard findCompanyName(String companyName)
    {
        //for each 구문을 통해 처음부터 끝까지 반복을 돌리면서 찾고자 하는 이름과 같은 이름이 있으면 탈출
        for(VisitingCard visitingCard : this.visitingCards)
        {
            if(companyName.compareTo(visitingCard.getCompanyName()) == 0)
            {
                //같은 이름이 있으면 해당 명함을 출력
                return visitingCard;
            }
        }
        //명함철에 해당 상호명이 없으면 null출력
        return null;
    }
}
```

### findCompanyName 설명
매개변수로 상호명을 문자열로 입력 받아서 이를 명함철의 첫 번째 명함부터 마지막 명함까지 반복하면서 입력 받은 상호명과 명함의 상호명이 일치하는지 찾습니다.<br><br>명함에서 입력받은 상호명과 일치하는 상호명이 발견되면 즉시 메소드를 종료하면서 해당 명함을 반환합니다.(return visitingCard)<br><br>
그리고 반복문이 끝나는 동안 일치하는 상호명을 명함철에서 찾지 못하면 null을 반환하도록 합니다.<br><br>

# 개선한 formFoTakingIn
```java
public class Main
{
    //[1] 끼우기
    public static void formFoTakingIn() throws IOException
    {
        //사용자로부터 콘솔창에 입려을 받기 위해 Scanner 생성하기
        Scanner scanner = new Scanner(System.in);
        String going = "Y";
        while (going.equals("Y") == true || going.equals("y") == true)
        {
            System.out.println();
            System.out.printf("\t명함철 - 끼 우 기\n");
            System.out.printf("\t==============================================\n");
            System.out.printf("\t성명 : ");
            String personalName = scanner.nextLine();
            System.out.printf("\t직책 : ");
            String position = scanner.nextLine();
            System.out.printf("\t휴대폰번호 : ");
            String cellularPhoneNumber = scanner.nextLine();
            System.out.printf("\t이메일주소 : ");
            String emailAddress = scanner.nextLine();
            System.out.printf("\t상호명 : ");
            String companyName = scanner.nextLine();
            String address;
            String telephoneNumber;
            String faxNumber;
            String url;
            //상호명으로 찾는다.
            VisitingCard visitingCard = visitingCardBinder.findCompanyName(companyName);
            //찾은 상호명이 있으면(명함철에 해당 상호명의 회사 정보가 이미 있으면)
            if(visitingCard != null)
            {
                address = visitingCard.getAddress();
                System.out.printf("\t주소 : %s\n", address);
                telephoneNumber = visitingCard.getTelephoneNumber();
                System.out.printf("\t전화번호 : %s\n", telephoneNumber);
                faxNumber = visitingCard.getFaxNumber();
                System.out.printf("\t팩스번호 : %s\n", faxNumber);
                url = visitingCard.getUrl();
                System.out.printf("\t홈페이지주소 : %s\n", url);
            }
            //찾은 상호명이 없으면(명함철에 해당 상호명의 회사 정보가 없으면)
            else
            {
                System.out.printf("\t주소 : ");
                address = scanner.nextLine();
                System.out.printf("\t전화번호 : ");
                telephoneNumber = scanner.nextLine();
                System.out.printf("\t팩스번호 : ");
                faxNumber = scanner.nextLine();
                System.out.printf("\t홈페이지주소 : ");
                url = scanner.nextLine();
            }
            System.out.printf("\t----------------------------------------------\n");
            System.out.printf("\t명함을 끼우시겠습니까?(Y/N) ");
            String recording = scanner.nextLine();
            if (recording.equals("Y") == true || recording.equals("y") == true)
            {
                //명함을 생성한다.
                visitingCard = new VisitingCard(personalName, position,
                        cellularPhoneNumber, emailAddress, companyName, address,
                        telephoneNumber, faxNumber, url);
                //명함을 끼운다.
                visitingCard = visitingCardBinder.takeIn(visitingCard);
                //반복문을 벗어난다.
                break;
            }
            System.out.printf("\t============================================================================\n");
            System.out.printf("\t계속하시겠습니까?(Y/N) ");
            going = scanner.nextLine();
        }
    }
}
```

## 개선한 formFoTakingIn 설명
사용자가 상호명을 입력하고 엔터키를 치기 전까지는 똑같고 그 후부터 코드가 살짝 바꼈습니다.<br><br>
사용자가 상호명을 입력하고 엔터키를 치는 순간 입력받은 그 상호명을 findCompanyName의 매개변수로 전달합니다.<br><br>
이 후 반환값을 받는데 이 반환값이 null이라면 일치하는 회사가 없으니 중복된 회사가 없다는 뜻이므로 어쩔 수 없이 사용자가 나머지 회사정보도 입력을 하여야 합니다.<br><br>
하지만 반환값이 null이 아니면(visitingCard객체가 출력이 되면) 명함철에 중복되는 회사 정보가 이미 있다는 뜻이고, 반환 받은 visitingCard객체의 참조변수값을 이용해 회사 정보에 접근하여 주소, 전화번호, 팩스번호, 홈페이지주소를 getter메소드를 통하여 구한 다음에 사용자 대신에 printf로 콘솔창에 출력해줍니다.<br><br>
이렇게 되면 회사 정보가 중복될 경우 상호명까지만 치면 나머지는 프로그램이 알아서 출력해주기 때문에 사용자 입장에서는 편리합니다.<br><br>
또한 사용자가 입력할 때 실수로 상호명은 일치하지만 나머지 필드(주소, 전화번호, 팩스번호, 홈페이지주소) 중 하나라도 오타가 나면 나중에 프로그램상에서 에러가 생길 수 있는데 이러한 문제점을 사전에 차단할 수 있습니다.<br><br>

# Main클래스의 formFoFinding
```java
public class Main
{
    //[2] 찾기
    public static void formFoFinding()
    {
        //사용자로부터 콘솔창에 입려을 받기 위해 Scanner 생성하기
        Scanner scanner = new Scanner(System.in);
        String going = "Y";
        while (going.equals("Y") == true || going.equals("y") == true)
        {
            System.out.println();
            System.out.printf("\t명함철 - 찾기\n");
            System.out.println();
            System.out.printf("\t성명 : ");
            String name = scanner.nextLine();
            System.out.println();
            ArrayList<VisitingCard> indexes = visitingCardBinder.find(name);
            //찾은게 있으면
            if(indexes.size() > 0)
            {
                String personalName;
                String position;
                String cellularPhoneNumber;
                String emailAddress;
                String companyName;
                String address;
                String telephoneNumber;
                String faxNumber;
                String url;
                for(int i = 0; i < indexes.size(); i++)
                {
                    VisitingCard visitingCard = indexes.get(i);
                    personalName = visitingCard.getPersonalName();
                    position = visitingCard.getPosition();
                    cellularPhoneNumber = visitingCard.getCellularPhoneNumber();
                    emailAddress = visitingCard.getEmailAddress();
                    companyName = visitingCard.getCompanyName();
                    address = visitingCard.getAddress();
                    telephoneNumber = visitingCard.getFaxNumber();
                    faxNumber = visitingCard.getFaxNumber();
                    url = visitingCard.getUrl();
                    System.out.printf("\t*번호: %d, *성명 : %s, *직책 : %s, *휴대폰번호 : %s, *이메일주소 : %s,\n" +
                                    "\t*상호명 : %s, *주소 : %s, *전화번호 : %s, *팩스번호 : %s, *홈페이지주소 : %s\n",
                            i + 1, personalName, position, cellularPhoneNumber, emailAddress,
                            companyName, address, telephoneNumber, faxNumber, url);
                    System.out.println();
                }
                System.out.printf("\t찾은 명함을 선택하시겠습니까?(Y/N) ");
                String choosing = scanner.nextLine();
                if(choosing.equals("Y") == true || choosing.equals("y") == true)
                {
                    while(true)
                    {
                        System.out.printf("\t번호 : ");
                        //nextInt대신에 nextLine을 써야 뒤에서 탈이 안남.
                        String stringIndex = scanner.nextLine();
                        int index = Integer.valueOf(stringIndex);
                        //배열첨자는 0부터 시작하기 때문에 사용자가 선택한 번호에서 -1해줘야함!
                        index--;
                        //번호를 제대로 눌렀으면(번호가 배열길이를 넘지 않고 음수가 아니면)
                        if(index < indexes.size() && index >= 0)
                        {
                            //현재 명함을 찾은 명함으로 이동한다.(바꾼다.)
                            visitingCardBinder.move(indexes.get(index));
                            //메소드 종료
                            return;
                        }
                        //번호를 잘못눌렀으면
                        else
                        {
                            System.out.println("\t번호를 잘못 선택하셨습니다.");
                            System.out.println("\t다시 번호를 선택해주세요.");
                        }
                    }
                }
            }
            //찾은게 없으면
            else
            {
                System.out.println("\t명함철에서 찾고자 하는 성명의 명함이 없습니다.");
            }
            System.out.println();
            System.out.printf("\t계속하시겠습니까?(Y/N) ");
            going = scanner.nextLine();
        }
    }
}
```

## formFoFinding 설명
먼저 사용자로부터 콘솔창에서 입력을 받기 위해 Scanner를 생성해주고, while반복문에 처음에는 무조건 들어가기 위해서 going문자열을 "Y"로 초기화 해줍니다.<br><br>
그러면 while반복문 내부로 들어가게 됩니다.<br><br>
사용자로부터 콘솔창에서 성명을 입력받을 수 있도록 성명란을 출력한 다음에 nextLine을 통해 사용자의 응답을 기다립니다.<br>
![찾는성명기다리는중](../../images/2022-01-07-seventeenth/찾는성명기다리는중.jpg)
<br><br>
이후 사용자가 찾고자 하는 성명을 입력한 뒤에 엔터키를 치면 사용자로부터 입력받은 문자열을 name에 저장하고 이를 VisitingCardBinder의 find메소드의 매개변수로 전달합니다.<br><br>
그러면 find는 VisitingCardBinder의 명함중에서 name과 일치하는 개인 성명을 가진 명함을 찾아서 그 참조변수(주소)값을 ArrayList<VisitingCard>에 저장한 뒤에 이를 반환합니다.<br><br>
예를 들어 "홍길동"이라고 입력했을 때 2개의 명함을 찾은 경우입니다.<br>

![찾는명함입력했을때](../../images/2022-01-07-seventeenth/찾는명함입력했을때.jpg)
<br><br>

예를 들어 "윤길동"이라고 입력했을 때 "운길동"과 일치하는 개인 성명을 가진 명함이 없는 경우입니다.<br>
![찾는성명이없는경우](../../images/2022-01-07-seventeenth/찾는성명이없는경우.jpg)
<br><br>
찾은 명함이 있을 경우, 즉,  ArrayList<VisitingCard> indexes의 size가 0보다 크면 찾은 명함을 출력해주고, 찾은 명함이 없으면 찾은 명함이 없다고 출력해줍니다.<br><br>
여기서 찾은 명함이 있는 경우 생각해볼 사항이 있습니다.<br><br>
사용자가 명함철에서 명함을 찾은 이유는 무엇일까요?<br><br>
그 명함이 필요한데 명함이 어디에 위치하는지 모르기 때문에 찾은 겁니다.<br><br>
그렇다면 찾은 명함을 선택하는 과정이 필요하지 않을까요?<br><br>
만약에 formFoFinding에 선택하기 기능이 없다면 명함이 명함철에 있는 것은 알게 됬지만 해당 명함이 명함철의 어디에 위치하는지 모르게 됩니다.<br><br>
그래서 찾은 명함을 꺼내려면 그 명함이 어디에 위치하는지 알기 위해 displayMenu에서 명함을 일일이 이동(처음, 이전, 다음, 마지막)시켜서 다시 찾아야 합니다.<br><br>
이 과정을 없애기 위해 formFoFinding에 선택하기 기능을 추가하는 것입니다.<br><br>
선택하기 기능은 현재 명함을 formFoFinding에서 찾은 명함으로 바로 이동시켜주는 역할입니다.<br><br>
그러나 현재 저희가 구현한 이동 메소드들에는 구체적으로 원하는 위치를 입력하여 거기로 바로 이동하는 메소드가 없습니다.<br><br>
그래서 새로운 메소드를 만들어야 합니다.<br><br>
새로운 메소드로 move(Visitingcard visitngcard)를 구현합니다.<br><br>
즉, 이동하고자 하는 명함의 참조변수값을 매개변수로 전달하면 명함철의 현재 명함(current)의 참조변수값을 입력받은 매개변수로 바꿔주고(이동하고) 이 참조변수값을 반환하는 메소드입니다.<br><br>

### move
```java
public class VisitingCardBinder implements Cloneable
{
    //Move(현재 명함의 위치를 해당 명함으로 이동한다.)
    public VisitingCard move(VisitingCard visitingCard)
    {
        //입력받은 명함이 명함철에서 어디에 위치하는지 구하기
        int index = this.visitingCards.indexOf(visitingCard);
        //해당 명함을 현재 위치로 정하기
        this.current = this.visitingCards.get(index);
        //현재 위치를 출력하기
        return this.current;
    }
}
```

이제 다시 formFoFinding으로 돌아가서 "찾은 명함을 선택하시겠습니까?"라는 질문에 사용자가 "Y" 또는 "y"를 누르면 while(true)라는 무한 반복에 들어갑니다.<br><br>
그리고 사용자로부터 찾은 명함 중에 몇번째 명함을 선택할지 입력을 받습니다.<br><br>
찾은 명함이 1개인 경우 당연히 1번만 입력받을 수 있고, 동명이인이 있어서 찾은 명함이 여래 개인 경우 최대 명함의 개수만큼 번호를 입력받아서 선택할 수 있습니다.<br><br>
그리고 사용자로부터 입력받은 숫자에 -1을 합니다.<br><br>
그 이유는 배열의 시작은 0부터이지만 인간은 1부터 시작해서 번호를 매기기 때문에 인간이 입력한 숫자를 다시 배열기준으로 맞춰야 ArrayIndexOutOfBoundsException에러가 발생하지 않습니다.<br><br>
또한 사용자가 실수로 번호를 잘못 입력하는 경우에도 ArrayIndexOutOfBoundsException에러가 발생하여 프로그램이 종료되기 때무에 이를 방지하기 위해 조건을 둡니다.<br><br>
index - 1 이 반영된 상태에서 if(index < indexes.size() && index >= 0)라는 조건을 통해서 사용자가 올바른 숫자를 입력한 경우에만 ArrayList에서 배열요소(VisitingCard)에 접근한 다음에 해당 명함으로 이동시키는 move메소드를 호출합니다.<br><br>
그리고 formFoFinding화면을 종료시키기 위해 return을 이용합니다.<br><br>
그러면 다시 displayMenu화면 창이 뜨고 현재 명함(current)을 출력할텐데 이 때 아까 move로 현재 명함을 바꿔놨기 때문에 저희가 formFoFinding에서 찾은 명함이 출력됩니다.<br>
![찾은명함선택시화면](../../images/2022-01-07-seventeenth/찾은명함선택시화면.jpg)
<br><br>
해당 조건을 벗어나는 경우에는 사용자가 번호를 잘못 입력했다는 뜻이기 때문에 else처리하여 사용자에게 번호를 잘못 입력했음을 알려주는 메세지를 출력합니다.<br><br>
이 후 while무한반복의 한 사이클이 끝나는데 번호를 올바르게 선택했으면 return을 통해 formFoFinding이 종료되면서 자동으로 while무한반복도 탈출하게 되고, 번호를 잘못입력했을 경우는 while무한반복문 내부로 들어가서 번호를 선택하는 창이 다시 뜨게 됩니다.<br><br>
찾은 게 없거나, 찾았어도 선택을 안하겠다고 한 경우 계속할지 여부를 묻는 창이 뜨고, 사용자가 "Y" 또는 "y"를 누르면 다시 반복문에 들어가고, 이외의 키를 누르면 formFoFinding이 종료되어 displayMenu로 돌아가게 됩니다.<br><br>

# Main클래스의 formFoTakingOut
```java
public class Main
{
    //[3] 꺼내기
    public static void formFoTakingOut()
    {
        //현재 명함이 있으면
        if(visitingCardBinder.getCurrent() != null)
        {
            //명함철에서 현재 명함을 꺼낸다.
            VisitingCard visitingCard = visitingCardBinder.takeOut(visitingCardBinder.getCurrent());
            //사용자로부터 콘솔창에 입려을 받기 위해 Scanner 생성하기
            Scanner scanner = new Scanner(System.in);
            System.out.println();
            System.out.printf("\t명함철 - 꺼내기\n");
            System.out.println("\t==========================================");
            System.out.println("\t<명함>");
            System.out.println("\t------------------------------------------");
            System.out.println("\t(개인정보)");
            System.out.printf("\t성명: %s\n", visitingCard.getPersonalName());
            System.out.printf("\t직책: %s\n", visitingCard.getPosition());
            System.out.printf("\t휴대폰번호: %s\n", visitingCard.getCellularPhoneNumber());
            System.out.printf("\t이메일주소: %s\n", visitingCard.getEmailAddress());
            System.out.println("\t------------------------------------------");
            System.out.println("\t(회사정보)");
            System.out.printf("\t상호명: %s\n", visitingCard.getCompanyName());
            System.out.printf("\t주소: %s\n", visitingCard.getAddress());
            System.out.printf("\t전화번호: %s\n", visitingCard.getTelephoneNumber());
            System.out.printf("\t팩스번호: %s\n", visitingCard.getFaxNumber());
            System.out.printf("\t홈페이지주소: %s\n", visitingCard.getUrl());
            System.out.println("\t==========================================");
            System.out.printf("\t명함을 다시 명함철에 끼우시겠습니까?(Y/N) ");
            String takingInAgain = scanner.nextLine();
            if (takingInAgain.equals("Y") == true || takingInAgain.equals("y") == true)
            {
                //꺼낸 명함을 다시 명함철에 끼운다.
                visitingCardBinder.takeIn(visitingCard);
            }
        }
    }
}
```

## formFoTakingOut 설명

formFoTakingOut은 실행되자 마자 우선 현재 명함이 있는지부터 체크합니다.<br><br>
**현재 명함이 없으면 명함철에 명함이 하나도 없다**는 말인데 명함철에 명함이 하나도 없으면 꺼낼 명함이 없기 때문에 바로 formFoTakingOut을 종료하고 다시 displayMenu창을 출력시킵니다.<br><br>
현재 명함이 있으면 현재명함(current)을 매개변수로 전달하여 takeOut메소드를 호출한 다음에 꺼낸 명함을 반환받습니다.<br><br>
이 후 꺼낸 명함의 정보를 사용자에게 알려주기 위해 꺼낸 명함의 getter 메소드를 통해 명함의 정보를 콘솔창에 출력합니다.<br><br>
이 후 사용자에게 명함을 다시 끼울지 말지를 물어보고 사용자가 명함을 다시 끼우겠다고 하면 꺼낸 명함을 매개변수로 전달하여 takeIn메소드를 통해 다시 명함을 명함철에 끼웁니다.<br><br>
formFoTakingOut은 반복문이 없기 때문에 순차적으로 진행이 되고, 그 진행이 끝나면 formFoTakingOut화면은 종료되고 다시 displayMenu화면이 출력됩니다.<br><br>
즉, 다시 끼우기를 하든 안하든 formFoTakingOut은 종료됩니다.<br><br>


# 마치며

VisitingCardBinder의 CUI화면의 경우 AddressBook과는 다르게 테이블형식을 이용하지 않았습니다.<br><br>
그러다 보니 전체보기 같은 화면창은 따로 구성하지 않았습니다.<br><br>
배열은 테이블 형식으로 전체를 조감할 수 있는 반면에 연결리스트의 경우 현재 화면에 하나의 명함만 보여주다 보니 조금은 답답한 면이 있을 수 있습니다.<br><br>
그래서 원래 C나 C++에서는 GUI프로그래밍을 할 때(WIN32 API, MFC)는 표형식은 아니더라도 연결리스트에서도 전체를 볼 수 있도록, 그리고 검색속도 향상을 시키기 위해 이분검색트리를 추가하였습니다.<br>

[이분검색트리활용](../../images/2022-01-07-seventeenth/이분검색트리활용.jpg)

<br><br>
옆에 있는 저 트리를 이용하면 회사를 클릭했을 때 회사에 소속된 개인리스트가 펼쳐지고, 개인의 성명을 클릭하면 해당 명함이 바로 화면에 출력됩니다.<br><br>회사에 소속된 명함이 몇 개 있는지를 회사에 소속된 개인의 성명 수로 알 수 있으며 회사가 몇 개 있는지도 직관적으로 바로 알 수 있어서 명함철의 전체적인 조감을 하기 좋습니다.<br><br> 
그러나 CUI에서는 이 트리를 어떻게 표현해야할지 좀 막막하네요.<br><br>
그렇다고 Swing이나 JavaFx를 공부할 수도 없고... 좀 고민을 해보겠습니다.<br><br>
우선 다음으로는 이 CUI프로그래밍을 MySQL과 연동시켜 작동하는 글을 올리도록 하겠습니다.<br><br>
잘못된 점이나 궁굼한 사항이 있으시면 댓글이나 이메일로 남겨주시면 답장드리겠습니다.<br><br>
긴 글 읽어주셔서 감사합니다^^