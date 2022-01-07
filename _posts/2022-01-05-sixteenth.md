---
layout: single
title:  "Java로 명함철 구현하기 - 자바입출력(RandomAccessFile)"
categories: Java
tag: [Java, 명함철, 프로젝트, VisitingCardBinder, Writer, RandomAccessFile]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 명함철에서 필요한 파일 개수
이번에는 CUI 프로그래밍으로 넘어가기 전에 먼저 외부텍스트파일의 데이터를 불러오고, 저장할 수 있게 load 와 save 메소드를 먼저 정의하려고 합니다.<br><br>
이번에는 주소록을 만들때보다 약간(?) 더 복잡합니다.<br><br>
주소록에서는 외부텍스트파일을 Personal하나만 만들어서 관리했습니다.<br><br>
명함철에서는 몇개의 텍스트파일이 필요할까요?<br><br>
주소록처럼 VisitingCard.txt 하나만 만들어서 여기서 Personal과 Company정보를 모드 관리하면 될까요?<br><br>
그렇다면 주소록과 똑같이 하면 됩니다.<br><br>
하지만 여기에는 큰 문제가 있습니다.<br><br>
어떤 문제가 있을까요?<br><br>
# 주소록과 명함철의 다른점
바로 명함에서 개인의 정보는 중복이 있을 수 없지만, **'회사 정보는 중복이 있을 수 있다'**라는 점입니다.<br><br>
좀 더 자세히 설명해보자면 명함을 여러 사람한테 받을 수 있습니다.<br><br>예를 들어, 이전에 '삼성전자'를 다니시는 '홍길동'이라는 '대리'님께 명함을 받았습니다.<br><br>이 후 시간이 흘러, '삼성전자'에 다니는 '김길동'이라는 '부장'님께 또 명함을 받게 되었습니다.<br><br>이 때 명함에서 개인의 정보는 서로 다르지만('홍길동' !=  '김길동'), 회사의 정보는 '삼성전자'이기 때문에 서로 같습니다.<br><br>
개인의 정보는 Unique하기 때문에 매번 새로 작성을 해야하는 것이 맞습니다.<br><br>
## 하나의 파일로 관리시 문제점
하지만 VisitingCard.txt로 하나의 파일로 데이터를 관리하다 보면 회사의 정보는 중복이 있을 수 있음에도 불구하고, 매번 적어줘야하는 중복의 문제가 발생합니다.<br><br>
이렇게 하나의 파일로 관리하는 것의 더 큰 문제는 만약에 저에게 100명의 삼성전자 임직원들의 명함이 있다고 가정해봅시다.<br><br>
여기서 만약에 삼성전자가 전화번호를 바꾼다던가 이사를 가서 주소가 바뀐다던가 하는 일이 생겼다고 가정해봅시다.<br><br>그럼 저는 이제 이 하나의 데이터 파일(VisitinCard.txt)에서 삼성전자 100개의 명함에 정보를 일일이 모두 바꿔야 합니다.<br><br>
100개 정도를 바꾸다 보면 사람이다 보니 실수로 1개의 명함이라도 오타 등 잘못 기재할 수 있습니다.<br><br>그렇게 되면 어떻게 될까요?<br><br>
나중에 데이터가 서로 맞지 않아 충돌이 생기는 끔찍한 일이 일어날 수 있습니다.<br><br>복사 붙여넣기를 하더라도 100개를 복붙하는 것도 쉬운 일이 아닌데 개수마저 더 늘어난다면 끔찍하네요ㅠ;;<br><br>
### 해결책
그래서 **명함철에서는 주소록과 다르게 두 개의 파일이 필요**합니다.<br><br>
즉, 개인의 정보는 Personal.txt에 저장하여 관리하고, 회사의 정보는 Company.txt에 저장하여 관리합니다.<br><br>
개인의 정보는 앞에서 언급했듯이 중복이 있을 수 없습니다.(같은 사람한테 명함을 2번 받을 일도 없고, 설사 받는다고 하여도 굳이 명함을 2개씩 저장할 필요도 없죠.)<br><br>그래서 개인의 정보는 매번 입력받을 때마다 파일에 저장합니다.<br><br>
그러나 **회사 정보는 중복이 있을 수 있습니다.**<br><br> 이전에 같은 회사에 다니는 다른 누군가에게 명함을 이미 받았을 경우에 개인의 정보는 Personal.txt에 저장하지만 그 명함의 회사정보는 Company.txt에 저장하지 않습니다.<br><br>
좀 더 구체적으로 설명하겠습니다.<br><br>
즉, 개인의 정보는 중복이 없기 때문에 무조건 저장합니다.<br><br>
그래서 필터로 중복을 거르고 하는게 없어서 단순합니다. 그냥 저장합니다.<br><br>
그러나 회사는 아까 말했듯이 중복이 있을 수 있기 때문에 파일에 **저장하기 전에 중복이 있는지 없는지 확인하는 필터 작업**이 필요합니다.<br><br> 이러한 방식으로 두 개의 파일로 관리하면 앞에서 말한 문제점을 해결할 수 있습니다. 대신 두 개의 파일로 관리하기 때문에 생기는 문제점이 있습니다.
## 두개의 파일로 관리시 문제점
주소록에서는 하나의 Personal파일로 관리하기 때문에 Personal파일만 관리하면 되기 때문에 따로 연결에 관한 문제가 없었습니다.<br><br>그러나 명함철에서는 Personal과 Company 2개의 파일로 관리하다 보니 연결의 문제가 발생합니다.<br><br>즉, Personal은 개인 정보를 가지고 있고, Company는 회사 정보를 가지고 있는데, 나중에 명함철에서 load를 할 때 Personal.txt와 Company.txt의 파일에서 각각 개인정보와 회사정보를 가지고 와서 이 정보를 바탕으로 VisitingCard를 생성하여, 이를 명함철(VisitingCardBinder)에 끼우기(takeIn)를 해야 합니다.<br><br>이 때 문제가 개인이 어떤 회사정보와 연결이 되어 있는지 알 수 있는 방법이 없습니다.<br><br>
좀 더 쉽게 설명하기 위해 예시를 들어 보겠습니다.<br><br>
최초의 텅 빈 명함철에 개인정보:"홍길동",... 회사정보:"삼성전자",...를 가진 명함을 끼웠습니다.<br><br> 두번째로 개인정보:"정길동",... 회사정보:"현대자동차",...를 가진 명함을 끼웠습니다.<br><br>
세번째로 개인정보:"박길동",... 회사정보:"삼성전자",...를 가진 명함을 끼웠습니다.<br><br>
이제 명함철 파일을 종료하면 Personal.txt에는 "홍길동", "정길동", "박길동" 3명의 정보가 출력되어 있을 것이고, Company.txt에는 "삼성전자", "현대자동차" 2개의 정보(중복된 정보는 출력이 안되기 때문에)가 출력되어 있을 것입니다.<br><br>
이제 명함철 프로그램을 다시 실행시키면 load메소드가 실행되고, 이 2개의 파일을 바탕으로 명함을 만들어 명함철에 끼우기를 하여야 하는데 개인이 어떤 회사랑 연결되어 있는지 알 방법이 없습니다.<br><br>
순차적으로 연결시키자니 개인의 데이터 개수와 회사의 데이터 개수가 달라서 불가능합니다.<br><br>
### 해결책
그래서 필요한 것이 개인의 데이터와 회사의 데이터를 연결하는 방법입니다.<br><br>이를 위해 **개인의 정보를 저장할 때는 제일 앞에(개인의 성명 앞에) 회사의 코드를 저장**합니다.<br><br>
회사의 코드는 Company.txt에 들어온 순서입니다.<br><br>앞에서 든 예시를 살펴보면 "삼성전자"가 제일 먼저 들어왔기 때문에 Company.txt에는 "삼성전자"가 제일 먼저 출력이 될 것이기 때문에 이제부터 "삼성전자"의 회사코드는 1번이 됩니다.<br><br> "현대자동차"는 두번 째로 들어왔기 때문에 회사코드는 2번이 됩니다.<br><br>
3번째로 들어온 "삼성전자"는 중복이기 때문에 Company.txt에 따로 출력은 안되지만 회사코드는 1번이 됩니다.<br><br> 이 회사코드를 Personal.txt에 개인의 정보를 저장할 때 제일 앞에(개인의 성명 앞에)저장해줍니다.<br><br>
예시를 살펴보면, 제일 먼저 입력된 "홍길동"은 "삼성전자"이니까 회사코드 1번이 Personal.txt에 출력됩니다.<br><br> 두번째로 입력된 "정길동"은 "현대자동차"이니까 회사코드 2번이 Personal.txt에 출력됩니다.<br><br>
마지막으로 입력된 "박길동"은 "삼성전자"이기 때문에 회사코드 1번이 Personal.txt에 출력됩니다.<br><br>
이러한 방식으로 연결을 하면 두 개의 파일을 순조롭게 입출력하여 관리할 수 있습니다.<br><br>
앞에서 설명이 너무 길었네요;;<br><br>그러나 이 원리를 이해하면 앞으로 설명할 load와 save 구현을 쉽게 이해할 수 있을겁니다.<br><br>

# save
```java
public class VisitingCardBinder implements Cloneable
{
     //명함철에 있는 명함 정보를 외부파일에 저장하기
    public void save()
    {
        //해당위치에 있는 data를 읽어 File객체를 생성한다.
        File personalFile = new File("Personal.txt");
        File companyFile = new File("Company.txt");
        //RandomAccessFile은 무조건 이어쓰기떄문에 File의 내용을 초기화해주기 위해 삭제한다.
        //companyFile이 있으면
        if(companyFile.exists() == true)
        {
            //해당 파일을 지운다.
            companyFile.delete();
        }
        //출력을 위한 스트림을 생성한다.
        try(Writer personalWriter = new FileWriter(personalFile);
            BufferedWriter personalBufferedWriter = new BufferedWriter(personalWriter);
            RandomAccessFile companyAccessFile = new RandomAccessFile("Company.txt", "rw");)
        {
            String companyInformation = "";//외부파일에서 회사정보를 담을 공간
            String companyName = "";//회사명을 담을 임시공간
            int companyCode = 0;//회사코드로 사용
            //명함철의 마지막 명함까지 반복한다.
            for(VisitingCard visitingCard : this.visitingCards)
            {
                //외부의 회사데이터 파일을 처음으로 이동시킨다.
                companyAccessFile.seek(0);
                //코드를 초기화시킨다.
                companyCode = 1;
                //외부 회사데이터 파일이 마지막이 아닌 동안 반복한다.
                while((companyInformation = companyAccessFile.readLine()) != null)
                {
                    //한글이 깨지기 때문에 이를 안깨지게 정상화처리해줌
                    companyInformation = new String(companyInformation.
                            getBytes("iso-8859-1"), "utf-8");
                    //RandomAccess의 writeUTF는 앞에 2byte에 길이를 같이 써서 출력하기 때문에
                    //상호 앞에 이를 제외하고 읽어야함.
                    companyName = companyInformation.substring(2, companyInformation.indexOf(","));
                    // 외부 회사파일의 상호명과 명함철에서 읽은 명함의 상호명과 서로 같으면
                    if(companyName.equals(visitingCard.getCompanyName()) == true)
                    {
                        break;
                    }
                    //회사의 코드를 증가시킨다.
                    companyCode++;
                }
                //파일의 마지막이면(명함철에서 읽은 명함의 상호명이 외부 회사파일의 상호명에 없으면)
                if(companyInformation == null)
                {
                    //외부 회사파일에 중복이 안된 명함의 회사 정보를 출력한다.
                    companyAccessFile.writeUTF(new String( visitingCard.getCompanyName()+
                            "," + visitingCard.getAddress() + "," + visitingCard.getTelephoneNumber()
                            + "," + visitingCard.getFaxNumber() + ","
                            + visitingCard.getUrl() + "\n"));
                }
                //외부 개인파일에 개인 정보를 출력한다.
                personalBufferedWriter.write(new String(Integer.toString(companyCode) + "," +
                        visitingCard.getPersonalName() + "," +  visitingCard.getPosition()
                        + "," + visitingCard.getCellularPhoneNumber() + ","
                        + visitingCard.getEmailAddress() + "\n"));
            }
            personalBufferedWriter.flush();
        } catch (IOException e) { e.printStackTrace(); }
    }
}
```

## save 설명
주소록 프로그램에서 설명할 때는 load부터 먼저 설명하고, save를 설명하였으나 VisitingCardBinder에서는 save부터 먼저 설명하도록 하겠습니다.<br><br>사실 구현 순서상에서도 load는 save를 염두에 두고, 구현해야 하기 때문에 흐름상 save를 먼저 구현하는 것이 더 좋은 거 같습니다.<br><br>특히 save를 먼저 해야 load를 할 수 있기 때문입니다(저장한 게 없으면 불러올 게 없는 것은 당연한 말이죠;;)<br><br>
처음부터 코드를 설명하자면 우선 Personal.txt와 Company.txt를 상대경로로 정하는 2개의 File객체를 생성해줍니다.<br><br>
출력의 경우 해당 상대경로에 파일이 없으면 자동으로 파일을 생성해주기 때문에 File클래스의 exists 메소드로 파일이 실제 해당 상대경로에 있는지 존재 여부를 확인해줄 필요가 없습니다.<br><br>
그리고 출력을 위해서 **Personal.txt**는 **char단위 입출력에 특화되어 있는 Writer클래스를 이용**합니다.<br><br>
다형성의 원리(Writher는 부모이자 추상클래스이고, FileWriter는 자식클래스임)를 이용해 FileWriter의 생성자를 이용해서 Writer 객체를 생성해주고, 여기에 외부출력 속도 향상을 시켜줄 필터를 씌우는데 그게 바로 BufferedWriter클래스입니다.<br><br>
BufferedWriter클래스의 경우 생성할 때 매개변수로 Writer자료형의 객체를 받습니다.<br><br> Writer에 필터만 씌우는 개념이기 때문에 필터를 씌우로면 Writer가 먼저 있어야 합니다.<br><br>
그리고 **Company.txt**는 **RandomAccessFile**클래스를 이용합니다.<br><br> 왜 Company.txt는 Personal.txt와 다르게 RandomAccessFile을 이용하는지 궁굼하실겁니다.(궁굼해야만 합니다...;;그래야 설명을 하니까요...ㅋㅋ)
먼저 RandomAccessFile 클래스에 대해서 간단하게 설명하도록 하겠습니다.
### RandomAccessFile 설명
Java에서 입출력을 할 때 사용하는 InputStream, OutputStream, Reader, Writer의 경우 항상 파일의 처음부터 순차적으로 읽으면서 파일의 마지막까지 이동합니다.<br><br>
그리고 읽은 파일을 다시 처음으로 되돌아갈 수 없습니다.<br><br>
그러나 **RandomAccessFile**은 **원하는 위치부터 파일을 읽을 수 있으며, 읽은 파일을 다시 처음으로 돌아갈 수 있습니다.**<br><br>
게다가 RandomAccessFile은 입출력 기능을 둘 다 담당하기 때문에 입력(InputStream, Reader)과 출력(OutputStream, Writer)를 따로 구분할 필요없어 RandomAccessFile 하나로 이용할 수 있습니다.<br><br>
대신 생성할 때 매개변수로 읽기(read)만 할 것인지 쓰기(write)도 같이 할 것인지를 정해줘야 합니다.<br><br>
이는 마치 C/C++에서 file을 생성할 때 읽기, 쓰기를 정해주는 것과 fseek을 통해 원하는 위치로 이동하고, 파일을 다읽어도 처음으로 다시 돌아갈 수 있는 기능과 거의 흡사합니다.<br><br>
RandomAccessFile을 생성할 때 또다른 매개변수로는 InputStream, OutputStream, Reader, Writer과 마찬가지로 파일경로를 직접 입력해주거나 File객체를 입력해주면 됩니다.<br><br>
대신에 불편한 점이 하나있다면 OutputStream과 Writer의 경우에는 파일에 출력을 할 때, 이어쓰기를 할지 덮어쓰기를 할지를 매개변수로 전달하여 컨트롤이 가능한데, **RandomAccessFile의 경우 무조건 이어쓰기**만 가능하다는 점입니다.(제가 알아본 결과로는 그런데 혹시나 가능한 방법이 있으면 알려주시면 감사하겠습니다^^;;)
그래서 덮어쓰기를 하고 싶다면, 즉, 기존의 파일의 내용은 지우고, 새로 작성하고 싶다면 저 같은 경우는 File객체의 delete메소드를 이용하여 해당 파일을 지웠습니다.<br><br> 그리고 RandomAccessFile을 생성할 때 매개변수로 "rw"(read and write)을 넣어주어 새로 파일을 생성하는 방식으로 파일을 초기화시켰습니다.<br><br>

### RandomAccessFile을 사용하는 이유
**Company.txt**는 **RandomAccessFile**클래스를 사용하는 이유는 바로 **파일의 처음으로 되돌아가야 하기 때문입니다.**<br><br>
명함철(VisitingCardBinder)에서 명함(VisitingCard)의 개인(Personal) 정보는 중복이 있을 수 없기 때문에 무조건 순차적으로 Personal.txt에 출력하면 됩니다.<br><br>
그러나 명함(VisitingCard)의 **회사(Company)** 정보는 앞에서 말씀드렸듯이 **중복이 있을 수 있기 때문에** 중복이 있는지 확인 후에 중복이 있으면 넘어가고, **중복이 없는 경우에만 Company.txt에 출력합니다.**<br><br>
이를 구현하기 위한 로직을 순차적으로 말씀드리도록 하겠습니다.<br><br>
먼저 명함철에서 for each 반복을 통해 처음부터 마지막까지 반복을 돌리면서 명함(VisitingCard객체)을 구합니다.<br><br> 그 다음 for each 반복문 내에서의 처리입니다.<br><br> 둘째로 Company.txt파일에서 읽을 데이터를 파일의 처음으로 이동시킵니다.(초기화)<br><br>
셋째로 Personal.txt에 출력될 companyCode를 1로 초기화시킵니다.<br><br>
넷째로 RandomAccessFile의 readLine메소드를 통해 Company.txt 파일의 처음부터 마지막까지 한 줄단위로 읽으면서 while 반복을 돌립니다.<br><br> while 반복문 내에서 처리입니다.<br><br>
다섯째로는 반복문내에서 RandomAccessFile의 readLine메소드로 읽은 경우 한글 텍스트는 깨짐현상이 일어나는데 이 깨짐현상을 없애기 위한 처리를 해줍니다.<br><br>
여섯번째로 Company.txt에서 상호명을 추출해야 하는데 이 때 RandomAccessFile의 특성으로 인해 주의해야할 점이 있습니다.<br><br> 뒤에서 중복이 없는 회사 정보를 Company.txt에 출력을 하는데 그 때 writeUTF라는 RandomAccessFile의 메소드를 사용합니다.<br><br>
writeUTF를 사용하면 앞에 2byte(2자리)는 한 줄 문자열의 길이 정보를 저장하고 있는데, readLine을 하면 이를 같이 읽게 됩니다.<br><br>
그래서 Company.txt에서 상호명을 추출할 때는(상호명이 제일 첫번째에 출력되는 필드이기 때문에) 반드시 앞의 2자리(문자열의 길이 정보)를 제외하고 추출을 해야 정확하게 상호명으로 중복체크를 할 수 있습니다.<br><br>
그리고 문자열을 출력할 때 규칙은 각 필드를 콤마(,)로 구분하여 출력하도록 정했습니다.(제 개인적인 규칙입니다 다른 분들은 구현할 때 다른식으로 하셔도 됩니다;;)<br><br>
그래서 **substring(2, companyInformation.indexOf(","))**을 하면 앞의 문자열 길이를 저장하는 자릿수인 0번째와 1번째를 제외하고, 2번째부터 시작하여 콤마(,)가 나오기 직전까지 글자를 추출하게 되는데 그러면 정확하게 상호명을 추출할 수 있습니다.<br><br>만약에 앞의 2자리를 제외하지 않으면 명함철에서 얻은 명함의 상호명과 중복여부를 제대로 검사할 수 없습니다.<br><br>예를 들어, 현재 명함철에서 명함의 상호명이 "삼성전자"이고, Company.txt에 출력된 상호명 역시 "삼성전자"인데 이를 readLine을 통해 읽었을 때 앞의 자리수를 제외하지 않고 substring하면 앞의 자리수 2자리 역시 문자열로 인식되어 "XX삼성전자"가 되어서 명함철의 명함에서 읽은 "삼성전자"과 다른 문자열로 인식하게 됩니다.<br><br>
그래서 제대로 된 중복검사를 위해 반드시 앞의 자리정보 2자리는 제외시키고 substring시켜줘야 합니다.<br><br>
일곱번째로 Company.txt로부터 정확하게 상호명만 출력하여 명함철의 명함 상호명과 비교하여 같은 문자열이면 바로 break를 통해 바로 반복문을 빠져나오고, 다른 문자열이면 companyCode를 증가시킵니다.<br><br>
그래서 while반복문을 빠져 나왔을 때, companyInformation이 null이면 Company.txt 파일이 끝나기 전에 같은 문자열을 찾았다는 말이고, 즉, Company.txt에 현재 명함의 상호명과 일치하는 중복 회사 정보가 있다는 말입니다.<br><br> 반대로 companyInformation이 null이라면 Company.txt의 파일이 끝날때까지 명함의 상호명과 일치하는 회사명을 발견하지 못했다는 말로 중복이 없는 회사정보라는 뜻입니다.<br><br>
그래서 여덟번째로 선택구조에서 companyInformation이 null이면(회사정보가 중복이 없으면) Company.txt에 명함에서 읽은 회사정보를 writeUTF메소드를 통해 출력합니다.(이 때 아까 말했던 제일 앞의 2자리에 문자열의 길이 정보가 출력되고, 2자리 이후에 회사 필드 정보가 출력됨)<br><br>
아홉번째로 명함의 개인 정보는 절대 중복이 있을 수 없기 때문에 Writer클래스의 write메소드를 통해 Personal.txt에 바로 출력해줍니다.<br><br>이 때 제일 제일 앞에 아까 계산했던 companyCode를 써줌으로써 이제 Personal.txt와 Company.txt가 서로 연결됩니다.<br><br>왜냐하면 이 companyCode가 Company.txt에서 회사의 입력 순서를 의미하기 때문입니다.<br><br> 예를 들어, "엘지전자"가 Company.txt의 3번째에 출력이 되었으면 이 회사를 다니는 개인의 데이터를 Personal.txt에 저장할 때 제일 앞에 companyCode로 3이 출력될 것입니다.<br><br>
이 과정이 for each 반복문의 한 사이클입니다.<br><br>그러면 다시 for each 반복문의 처음으로 돌아가 명함철에서 다음 명함을 읽습니다.<br><br>
그리고 위에서 했던 작업을 또 반복하면서 명함철의 마지막 명함까지 같은 행위를 반복합니다.<br><br>
그런데 이 때 RandomAccessFile의 seek메소드를 통해 Company.txt파일의 처음으로 돌아가지 않으면 마지막으로 읽은 파일의 다음 위치부터 읽어들이기 때문에 상호명의 제대로 된 중복검사를 할 수 없습니다.<br><br>
즉, 명함의 상호명을 읽어들여 매변 Company.txt파일의 **처음부터 시작하여** 끝까지 그리고 일치하는 상호명이 있을때까지 반복하는 과정입니다.<br><br>설명이 길었는데 제대로 설명이 되었을지 모르겠네요ㅠ<br><br>다음으로는 load를 설명할 차례입니다.

# load
```java
public class VisitingCardBinder implements Cloneable
{
    //외부 파일에 있는 정보를 읽어서 VisitingCardBinder에 Load하기
    public int load()
    {
        //해당위치에 있는 data를 읽어 File객체를 생성한다.
        File personalFile = new File("Personal.txt");
        File companyFile = new File("Company.txt");
        //해당 위치에 파일이 존재하면
        if(personalFile.exists() == true && companyFile.exists() == true)
        {
            //입력을 위한 스트림을 생성한다.
            try(Reader personalReader = new FileReader(personalFile);
                BufferedReader personalBufferedReader = new BufferedReader(personalReader);
                RandomAccessFile companyAccessFile = new RandomAccessFile("Company.txt", "r");)
            {
                VisitingCard visitingCard = null;//visitingCard를 임시저장할 공간
                //줄단위(Personal 객체단위)로 읽는다.
                int beginIndex = 0;//시작위치를 저장할 임시공간
                int endIndex = 0;//콤마(,)위치를 저장할 임시공간(,단위로 필드가 구분됨)
                String personalInformation = "";//한줄단위로 읽은 개인 데이터를 저장할 임시공간
                String companyCode;//한줄단위로 읽은 데이터 중에서 개인의 회사코드를 저장할 임시공간
                int code = 0;//한줄단위로 읽은 데이터 중에서 개인흐 회사코드를 정수로 변경할 공간
                String personalName;//한줄단위로 읽은 데이터 중에서 이름을 저장할 임시공간
                String position;//한줄단위로 읽은 데이터 중에서 직책을 저장할 임시공간
                String cellularPhoneNumber;//한줄단위로 읽은 데이터 중에서 휴대폰번호를 저장할 임시공간
                String emailAddress;//한줄단위로 읽은 데이터 중에서 이메일주소를 저장할 임시공간
                String companyInformation = "";//한줄단위로 읽은 회사 데이터를 저장할 임시공간
                String companyName;//한줄단위로 읽은 데이터 중에서 상호를 저장할 임시공간
                String address;//한줄단위로 읽은 데이터 중에서 주소를 저장할 임시공간
                String telephoneNumber;//한줄단위로 읽은 데이터 중에서 전화번호를 저장할 임시공간
                String faxNumber;//한줄단위로 읽은 데이터 중에서 팩스번호를 저장할 임시공간
                String url;//한줄단위로 읽은 데이터 중에서 홈페이지주소를 저장할 임시공간
                int i = 0;//반복제어변수
                //개인데이터를 파일의 마지막까지 읽는다.
                while((personalInformation = personalBufferedReader.readLine()) != null)
                {
                    //시작위치를 초기화한다.
                    beginIndex = 0;
                    //personalInformaion에서 처음부터 시작해서 ","의 위치를 찾는다.
                    endIndex = personalInformation.indexOf(",");
                    //companyCode 문자열을 구한다.
                    companyCode = personalInformation.substring(beginIndex, endIndex);
                    //companyCode 문자열을 정수로 바꿔준다.
                    code = Integer.parseInt(companyCode);
                    //회사파일을 파일의 처음으로 이동시킨다.
                    companyAccessFile.seek(0);
                    //companyCode까지 반복하면서 그리고 회사파일이 끝이 아닐동안 반복한다.
                    i = 0;
                    while(i < code && (companyInformation = companyAccessFile.readLine()) != null)
                    {
                       i++;
                    }
                    //읽은 외부데이터로부터 개인의 정보를 구한다.
                    //","위치 다음을 시작위치로 재설정한다.
                    beginIndex = endIndex + 1;
                    //재설정한 시작위치부터 다음 ","위치를 찾는다.
                    endIndex = personalInformation.indexOf(",", beginIndex);
                    //personalName 문자열을 구한다.
                    personalName = personalInformation.substring(beginIndex, endIndex);
                    //","위치 다음을 시작위치로 재설정한다.
                    beginIndex = endIndex + 1;
                    //재설정한 시작위치부터 다음 ","위치를 찾는다.
                    endIndex = personalInformation.indexOf(",", beginIndex);
                    //position 문자열을 구한다.
                    position = personalInformation.substring(beginIndex, endIndex);
                    //","위치 다음을 시작위치로 재설정한다.
                    beginIndex = endIndex + 1;
                    //재설정한 시작위치부터 다음 ","위치를 찾는다.
                    endIndex = personalInformation.indexOf(",", beginIndex);
                    //cellularPhoneNumber를 구한다.
                    cellularPhoneNumber = personalInformation.substring(beginIndex, endIndex);
                    //","위치 다음을 시작위치로 재설정한다.
                    beginIndex = endIndex + 1;
                    //emailAddress를 구한다.
                    emailAddress = personalInformation.substring(beginIndex,
                            personalInformation.length());
                    //읽은 외부데이터로부터 회사의 정보를 구한다.
                    //RandomAcceesFile의 경우 앞의 2자리는 자리수를 나타내기 때문에 2부터 초기화하여 시작한다.
                    beginIndex = 2;
                    //한글이 깨지기 때문에 이를 안깨지게 정상화처리해줌
                    companyInformation = new String(companyInformation.
                            getBytes("iso-8859-1"), "utf-8");
                    //companyInformaion에서 처음부터 시작해서 ","의 위치를 찾는다.
                    endIndex = companyInformation.indexOf(",");
                    //comanyName 문자열을 구한다.
                    companyName = companyInformation.substring(beginIndex, endIndex);
                    //","위치 다음을 시작위치로 재설정한다.
                    beginIndex = endIndex + 1;
                    //재설정한 시작위치부터 다음 ","위치를 찾는다.
                    endIndex = companyInformation.indexOf(",", beginIndex);
                    //address 문자열을 구한다.
                    address = companyInformation.substring(beginIndex, endIndex);
                    //","위치 다음을 시작위치로 재설정한다.
                    beginIndex = endIndex + 1;
                    //재설정한 시작위치부터 다음 ","위치를 찾는다.
                    endIndex = companyInformation.indexOf(",", beginIndex);
                    //telephoneNumber 문자열을 구한다.
                    telephoneNumber = companyInformation.substring(beginIndex, endIndex);
                    //","위치 다음을 시작위치로 재설정한다.
                    beginIndex = endIndex + 1;
                    //재설정한 시작위치부터 다음 ","위치를 찾는다.
                    endIndex = companyInformation.indexOf(",", beginIndex);
                    //faxNumber 문자열을 구한다.
                    faxNumber = companyInformation.substring(beginIndex, endIndex);
                    //","위치 다음을 시작위치로 재설정한다.
                    beginIndex = endIndex + 1;
                    //url 문자열을 구한다.
                    url = companyInformation.substring(beginIndex, companyInformation.length());
                    //외부에서 읽은 개인의 정보와 회사의 정보로 명함을 생성한다.
                    visitingCard = new VisitingCard(personalName, position, cellularPhoneNumber,
                            emailAddress, companyName, address, telephoneNumber, faxNumber, url);
                    //명함을 명함철에 끼운다.
                    this.takeIn(visitingCard);
                }
            } catch (IOException e) { e.printStackTrace(); }
        }
        //load한 명함 수를 반환한다.
        return this.length;
    }
}
```
## load 설명
코드가 많이 길죠?;; 아무래도 VisitingCard를 생성하기 위해 9개의 필드가 필요하다 보니 길어졌는데, 아까 앞의 원리만 이해한다면 전혀 어렵지 않습니다.<br><br>
그리고 문자 저장 및 추출 규칙은 아까 save할 때 정했습니다.<br><br>
(**,단위로 필드를 구분하는 규칙**인데 save에서 VisitingCard객체에 있는 정보들을 각각의 txt파일에 저장할 때 이 규칙을 이용하여 저장하도록 구현하였음!)<br><br>
처음부터 설명하자면 아까 언급했듯이 Personal.txt와 Company.txt를 상대경로로 정하는 2개의 File객체를 생성해줍니다.<br><br>
다음으로 해당 파일이 있는지 확인하는데 2개 파일 모두 있어야 의미가 있기 때문에 AND로 2개의 파일이 모두 존재해야하는 경우에만 선택문에 들어가도록 합니다.<br><br>
그 다음 외부파일의 데이터를 명함철 프로그램으로 입력하기 위한 스트림을 생성해야하는데, **Personal.txt**의 경우 **처음부터 마지막까지 순차적으로 한 줄씩 읽으면 되기 때문에 char 단위 전용 입력 클래스인 Reader클래스**를 이용합니다.<br><br>
그 다음 Reader클래스에 필터를 씌우는 **BufferedReader클래스를 생성하고, BufferedReader 클래스의 readLine메소드를 통해 한 줄 단위로 입력**을 받을 수 있습니다.<br><br>
load메소드를 구현할 때도 **Company.txt**는 Personal.txt처럼 처음부터 마지막까지 순차적으로 한 줄씩 읽는 것은 맞으나 **처음으로 다시 되돌아갈 수 있는 기능이 필요**합니다.<br><br>
위에서 말하였듯이 VisitingCard에서는 Personal의 정보와 Company의 정보를 한꺼번에 관리하지만 외부데이터 파일 입출력시에는 Personal정보와 Company정보를 따로 관리하기때문에 이를 연결시켜줄 필요가 있습니다.<br><br>
그래서 아까 save를 구현할 때 **Personal.txt에 회사의 코드를 저장**했습니다.<br><br>
즉, 이 **Personal.txt에 저장되어 있는 이 회사코드를 읽어서** 이 **회사코드만큼 반복**을 돌리면서 **Company.txt에 있는 파일을 하나씩 순차적으로 읽습니다.**<br><br>
이 **코드만큼 반복**을 하여 **회사정보를 읽으면 개인이 다니는 회사정보(명함에 적힌 개인과 회사가 일치하는)를 읽게 됩니다.**<br><br>
다시 코드로 돌아가서 설명드리면 Personal.txt의 경우 char단위 입력에 특화되어 있는 Writer클래스를 그 자식인 FileWriter의 생성자로 생성합니다.<br><br>이 후 입력 속도 향상을 위한 필터 역할을 하는 BufferedReader 클래스를 생성합니다.<br><br>
Company.txt의 경우 파일의 처음으로 되돌아가는 기능이 필용하기 때문에 RandomAccessFile클래스를 생성하여 이용합니다.<br><br> 이제 while반복문의 차례인데 Personal.txt의 처음부터 마지막까지 반복합니다.<br><br>
BufferedReader클래스의 메소드인 readLine은 외부 파일에서 한 줄씩 읽는데 파일이 끝이 아니면 읽은 데이터를 반환하고, 파일의 끝이면 null을 반환합니다.<br><br>
while 반복문 내에서 **companyCode = personalInformation.substring(beginIndex, endIndex);**을 통해 Personal.txt에 저장된 companyCode를 읽습니다.<br><br>
그 다음은 현재 companyCode가 문자열이기 때문에 숫자로 바꿔주고, RandomAccessfile의 seek메소드를 통해 Company.txt를 파일의 처음으로 이동시킵니다.(초기화)<br><br>
그 다음 반복제어변수인 i를 0으로 초기화시켜주고, while반복문을 돌립니다.<br><br>
여기서 주의해야할 점은 while반복문의 반복조건입니다.<br><br>
**i < code && (companyInformation = companyAccessFile.readLine()) != null**인데 code(companyCode는 문자열인데 이를 숫자로 바꿔준게 code) 1부터 시작하는데 왜 반복제어변수는 0부터 시작하게 했을까요?<br><br>
현재 이 조건대로라면 code는 companyCode숫자보다 한번 더 반복문에 들어가게 됩니다.(code는 1부터 시작인데 i는 0부터 시작하기 때문에!)<br><br>만약에 i도 1부터 시작하게 되면 i = code가 되면 바로 반복문을 빠져나가버리기 때문에 && 뒤에 (companyInformation = companyAccessFile.readLine()) != null을 실행하지 않습니다.<br><br>
AND의 특성상 앞으 조건이 일치하지 않으면 뒤의 조건은 들어가지도 않고 바로 반복문을 빠져나옵니다.<br><br>
그렇게 되면 해당 code와 일치하는 회사 정보를 읽지 못하고, companyInformation에는 이전 회사의 정보가 저장되게 됩니다.<br><br>그래서 i는 0부터 시작하여 한번 더 반복문에 들어가게 되면 companyInformation에는 현재 회사의 정보가 저장이 되고 다음 반복문에서는 i = code이기 때문에 바로 반복문을 탈출하게 됩니다.<br><br>
예를 들어, Personal.txt에는 회사코드로 2번이 저장되어 있고, Company.txt에는 첫번째에 "삼성전자", 두번째에 "현대자동차"가 저장되어 있다고 가정합시다. 그러면 i = 0부터 시작했기 때문에 처음 반복문을 들어가면 i < code(=2)이기 때문에 다음 조건문으로 가서 Company.txt의 "삼성전자"를 읽게 됩니다. 그리고 while 반복문 내부에서 i++을 통해 1이 증가되면 이제 i=1이고 다시 조건문으로 가면 여전히 i = 1 < code(=2)이기 때문에 다음 조건문으로 가서 Company.txt의 두번째 데이터인 "현대자동차"를 읽게 됩니다.<br><br>
그리고 반복구조를 들어가서 i++을 하면 이제 i = 3이 되고, 다시 반복구조로 가면 i = 3 < code(=2)이기 때문에 다음 조건은 실행하지도 않고 바로 반복구조를 벗어나게 됩니다.<br><br>이렇게 되면 Personal.txt에 회사코드가 2번이었고, 우리는 Company.txt의 두번째인 "현대자동차"를 읽게 됩니다.<br><br>
이제 beginIndex를 콤마(,)위치 다음으로 이동시키고(각 필드는 콤마로 구분되어 있도록 save에서 저장했음) endIndex에는 다음 콤마위치를 저장합니다.<br><br>그리고 이 위치를 이용하여 제일 처음 while반복문을 시작할 때 읽은 personalInformation(개인정보 String)의 substring를 통해 다음 필드인 개인의 성명을 추출할 수 있습니다.<br><br>
이러한 방식으로 성명, 직책, 휴대폰번호까지 구하고, 마지막으로 이메일 정보를 구할 때는 더이상 뒤에 콤마가 없기 때문에(각 필드를 콤마로 구분하기 때문에 당연히 마지막 필드인 이메일 뒤에는 콤마가 없습니다.)를 substring의 매개변수로 endIndex=문자열의 길이(personalInformation.length())를 입력합니다.<br><br>
substring의 경우 beginIndex는 포함하지만 endIndex는 포함하지 않습니다.<br><br>즉, 매개변수로 입력받은 beginIdnex부터 endIndex - 1까지 문자열을 추출합니다.<br><br>
그래서 문자열의 길이를 endIndex로 입력하면 개수는 1부터 시작하고, 문자열의 위치는 배열을 기반으로 하여 0부터 시작하기 때문에 정확하게 일치합니다.<br><br>그러면 이제 Personal.txt에서 처음 읽은 한 줄의 개인 정보는 추출하였고, 다음으로는 Company.txt에서 읽은 회사정보를 추출할 차례입니다.<br><br>
Personal.txt의 경우 제일 앞에 저장된 회사코드를 읽을 때 beginIndex = 0 부터 시작하였는데, Company.txt는 제일 첫번째 필드인 상호명을 읽기 위해서 beginIndex = 2부터 시작합니다.<br><br>왜 그럴까요?<br><br>
아까 save를 설명할 떄 유심히 읽으셨으면 그 답을 이미 아실겁니다.<br><br>
save를 할 때 RandomAccessFile의 writeUTF메소드를 통해 명함철 명함의 회사 정보를 Company.txt에 출력하는데 이 때 문자열 제일 앞의 두자리(2byte)는 해당 문자열의 길이 정보를 출력한다고 말했습니다.<br><br>그래서 회사 정보를 추출할 때는 앞의 두자리를 제외하고 추출해야 오류가 생기지 않습니다.<br><br>그래서 beginIndex = 2부터 시작하여 endIndex = 콤마위치(,)를 매개변수로 하여 companyInformation의 substring을 하여야 정확하게 상호명을 추출할 수 있습니다.<br><br>
이 후 주소, 전화번호, 팩스번호는 Personal정보를 추출할 때와 똑같이 endIndex를 다음 콤마위치로 설정해주시면 되고, beginIndex는 콤마 다음 위치로 설정하면 해당 필드를 정확하게 추출할 수 있습니다.<br><br>
그리고 마지막 필드인 홈페이지주소 역시 Personal에서 했던 것과 동일하게 endIndex로 companyInformation.length()를 입력해주면 됩니다.<br><br>
이렇게 추출한 9개의 필드 정보를 통해 새로운 명함을 생성합니다.<br><br>그리고 새로 생성한 명함을 명함철에 끼웁니다.(takeIn)<br><br>
여기까지가 반복문의 한 사이클입니다.<br><br>
그럼 다시 제일 위의 while반복문의 조건으로 가서 **(personalInformation = personalBufferedReader.readLine()) != null**을 실행하고 같은 사이클을 반복합니다.<br><br> 이 후 while반복문이 끝나면 명함철의 명함 개수를 반환하면서 load메소드가 종료됩니다.<br><br>

# 마치며
잘못된 점에 대한 지적이나 궁굼한 사항이 있으면 언제든지 댓글을 남겨주시거나 메일을 보내주시면 됩니다^^<br><br>
앞으로도 공부한 것을 기록하기 위해 지속적으로 작성할 예정입니다.<br><br>
감사합니다.

