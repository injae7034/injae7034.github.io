---
layout: single
title:  "반환값이 여러개인 C++의 메소드를 Java에서 HashMap을 적용하여 반환값 1개인 메소드로 바꾸기"
categories: Java
tag: [Java, C++, 프로젝트, 가계부, AccountBook, 해쉬맵, Map, HashMap, LinkedHashMap]
toc: true
toc_label: "목차"
toc_sticky: true
---

# 시작하며
저번 시간까지 가계부(AccountBook) 프로젝트를 하면서 추상클래스, 추상메소드, 부모와 자식간의 상속의 개념을 배울 수 있었습니다.<br>
<a href="https://injae7034.github.io/java/twentieth/"
target="_blank">Java프로젝트하면서 추상클래스, 추상메소드, 다형성, 부모와 자식간의 상속 개념 배우기</a>

<br><br>

또한 오버로딩, 다형성 그리고 오버라이딩을 적용했을 때 어떻게 코드를 효율적으로 짤 수 있는지에 대해서도 배웠습니다.<br>
<a href="https://injae7034.github.io/java/twentyFirst/"
target="_blank">Java프로젝트하면서 오버로딩 그리고 다형성, 오버라이딩을 적용하여 코드 효율성 올리기</a>

<br><br>

이번 시간에는 가계부(AccountBook)클래스의 마지막 메소드인 계산하기(calculate)를 구현하려고 합니다.<br><br>
가계부(AccountBook) 프로그램은 예전에 C++로 구현을 하였습니다.<br><br>
이를 Java로 구현하면서 예전에는 미처 생각하지 못했던 부족한 점들을 보완하였습니다.<br><br>
그리고 마지막으로 calculate 메소드를 Java로 옮기려고 하는데 약간의 문제(?)가 생겼습니다.<br><br>
이제부터 문제가 무엇인지 이를 어떻게 해결하였는지를 차차 설명하도록 하겠습니다.

# C++에서 calculate 메소드 구현
```c++
//Calculate
void AccountBook::Calculate(Date one, Date other, Currency* totalIncome,
	Currency* totalOutgo, Currency* totalBalance)
{
	//1. 계산할 기간을 입력받는다.
	//2. one이 other보다 작거나 같은동안에 반복한다.
	*totalIncome = 0;
	*totalOutgo = 0;
	*totalBalance = 0;
	Long(*indexes) = 0;
	Long count = 0;
	Long i;
	Account* account;
	while (one <= other)
	{
		//2.1 one날짜의 account들을 찾는다.
		this->accounts.LinearSearchDuplicate(&one, &indexes, &count, CompareDates);
		//2.2 count보다 작은동안에 반복한다.
		i = 0;
		while (i < count)
		{
			//2.2.1 account를 구한다.
			account = this->accounts.GetAt(indexes[i]);
			//2.2.2 수입이면
			if (dynamic_cast<Income*>(account))
			{
				//2.2.2.1 totalIncome을 구한다.
				(*totalIncome) += account->GetAmount();
			}
			//2.2.3 지출이면
			else if (dynamic_cast<Outgo*>(account))
			{
				//2.2.3.1 totalOutgo를 구한다.
				(*totalOutgo) += account->GetAmount();
			}
			i++;
		}
		//2.3 indexes배열을 할당해제한다.
		if (indexes != 0)
		{
			delete[] indexes;
		}
		one++;
	}
	//3. totalBalance를 구한다.
	*totalBalance = (*totalIncome) - (*totalOutgo);
	//4. totalIncome, totalOutgo, totalBalance를 출력한다.
	//5. 끝내다.
}
```
## C++로 구현한 calculate 메소드 설명
이 코드는 제가 2020년 12월경에 작성한 코드입니다.(Visual Studio에 그렇게 써져 있네요;;)<br><br>
시간이 참 빨리가네요;;<br><br>
각설하고 다시 설명으로 돌아가면 매개변수로 총 5개가 있습니다.<br><br>
그 중 2개인 Date one, Date other는 값을 입력 받습니다.<br><br>
즉, 기간(one부터 other까지)동안 계산을 하라는 말이죠.<br><br>
나머지 3개의 매개변수는 포인터(주소)값입니다.<br><br>
즉, 값이 아니라 Main에 있는 totalIncome, totalOutgo, totalBalance를 가리키고 있는 포인터(주소값)입니다.<br><br>
이 포인터들을 이용하면 calculate 메소드 내부에서 Main에 있는 totalIncome, totalOutgo, totalBalance의 값들을 변경시킬 수 있습니다.<br><br>
즉, totalIncome, totalOutgo, totalBalance는 calculate 내부의 로컬변수가 아니라 Main에 위치한 변수입니다.<br><br>
혹시나 포인터에 대해 잘모르신다면 제가 예전에 작성한 글을 처음부터 끝까지 한번 천천히 읽어보시면 도움이 될 것입니다.<br>

<a href="https://injae7034.github.io/java/eleventh/#c-find%EB%A9%94%EC%86%8C%EB%93%9C-%EB%A9%94%EB%AA%A8%EB%A6%AC%EB%A7%B5"
target="_blank">C++와 Java의 차이점에 대한 고찰 - 참조변수와 포인터, 메소드 반환(리턴) 개수</a>

<br><br>

C++에서는 이 포인터를 활용해서 출력값을 무제한으로 늘릴 수 있습니다.<br><br>
반환형은 void로 정하고, 출력할 값을 매개변수에서 포인터(주소)값으로 받으면 됩니다.<br><br>
그래서 C++에서 구현한 calculate는 일정기간(one, other)을 매개변수로 입력 받아 포인터를 활용하여 총수입(totalIncome), 총지출(totalOutgo), 총잔액(totalBalance를), 이렇게 3개를 반환하는 메소드입니다.<br><br>
먼저 포인터(*)를 활용해 Main에 있는 totalIncome, totalOutgo, totalBalance의 값을 0으로 초기화시켜줍니다.<br><br>
가계부(AccountBook)에서 같은 기간동안의 Account 객체의 배열첨자 위치를 저장할 indexes 배열을 0(null)으로 초기화시킵니다.<br><br>
그리고 indexes 배열의 개수를 세는데 이용할 count 변수 역시 0으로 초기화시킵니다.<br><br>
다음은 while 반복구조로 이동하는데 반복문의 조건이 one\<=other인 것을 알 수 있습니다.<br><br>
이는 C++의 특성인데 C++의 경우 연산자함수를 정의할 수 있습니다.<br><br>
연산자함수란 지금보시다시피 \<= 이라는 연산기호가 나오고 사용자가 이에 대해 정의를 했다면 해당 연산자함수가 호출됩니다.<br><br>
저는 C++에서 Date 클래스를 구현할 때 연산자함수로 bool operator\<=(const Date& other)를 선언 및 정의하였기때문에 one과 other 둘다 Date 클래스이고, 해당 연산자함수의 조건과 일치하기 때문에 연산자함수가 호출됩니다.<br><br>
연산자함수 \<=의 로직은 Java에서 Date를 구현할 때 이용한 isLowerThan과 같습니다.<br>
<a href="https://injae7034.github.io/java/nineteenth/#islowerthan-%EB%A9%94%EC%86%8C%EB%93%9C"
target="_blank">Java의 LocalDate를 활용해서 나만의 Date클래스 만들기 - isLowerThan 메소드</a>

<br><br>
즉, Date one의 날짜가 Date other의 날짜보다 작거나 같은동안 while반복이 성립됩니다.<br><br>

while반목문 내부에서는 **LinearSearchDuplicate**라는 메소드를 호출하는데 이는 제가 개인적으로 미리 구현해놓은 라이브러리 메소드입니다.<br><br>
여기서 자세하게 설명하기는 힘들지만 **LinearSearchDuplicate의 기능**을 간략하게 설명하자면 **가계부에서 one(Date)과 일자가 같은 Account 객체들의 배열첨자 위치를 찾아서 indexes에 그 배열첨자 위치를 저장**해주는 역할입니다.<br><br>
java에서 구현한 AccountBook의 find(date)와 동일한 기능을 수행합니다.<br> 
<a href="https://injae7034.github.io/java/twentyFirst/#finddate"
target="_blank">AccountBook 클래스 - find(date) 메소드</a>

<br><br>

LinearSearchDuplicate 메소드 호출이후에 포인터를 통해 count에는 one과 같은 일자를 가진 Account 객체들의 개수가 저장되어 있습니다.<br><br>
이후 반복제어변수 i = 0으로 초기화시키고, count 개수보다 작은동안 반복을 돌립니다.<br><br>
이 while반복문내에서 account 객체를 구하는데 이 때 구하는 위치를 indexes[i]로 하는 이유에 대해 설명하겠습니다.<br><br>
i는 0부터 시작해서 count보다 작은동안 계속 순차적으로 증가하는 반복제어 변수이고, indexes의 각 배열요소는 가계부에서 일자가 서로 같은 Account 객체들의 배열첨자 위치를 가지고 있는 정수배열이기 때문에 indexes[i]를 통해 indexes의 i번째 배열요소의 값에 접근하면 가계부에서 Account 객체의 위치가 저장되어 있습니다.<br><br>
그래서 이 위치를 통해 GetAt(indexes[i])메소드를 호출하면 해당 위치(one과 같은 일자를 가진)의 Account 객체를 얻을 수 있습니다.<br><br>
이렇게 구한 Account 객체가 Income인지 Outgo인지 dynamic_cast를 통해 체크하고, Income이면 포인터를 이용하여 Main에 위치한 변수 totalIncome에 누적하여 더해주고, Outgo이면 마찬가지로 포인터를 이용해 Main에 위치한 변수 totalOutgo에 누적하여 더해줍니다.<br><br>
이 후 반복제어변수 i를 1증가시켜주면서 다시 반복조건(i\<count)으로 갑니다. <br><br>
이 반복이 끝나면 one과 같은 일자를 가진 Account 객체들의 총수입과 총지출을 구할 수 있습니다.<br><br>
indexes배열은 이제 할 일을 다했기 때문에 메모리누수를 방지하기 위해 할당해제를 해줍니다.<br><br>
이제 one을 다음 날짜로 이동시켜줘야 하는데 이 때 다시 연산자함수(++)를 사용합니다.<br><br>
연산자함수++을 호출하면 one의 날짜를 1일 증가시켜줍니다.<br><br>
이후 다시 처음의 반복조건(one\<=other)로 이동해서 조건이 충족하면 while 반복을 빠져나가고, 충족하지 못하면 다시 반복문으로 들어와 위의 과정을 반복합니다.<br><br>
반복문(one\<=other)을 벗어나게 되면 일정기간동안(one부터 other까지)의 총수입과 총지출이 누적되어 구해졌고, 이를 바탕으로 **총잔액(= 총수입 - 총지출)**을 구하고 포인터를 통해 이를 Main에 있는 변수 totalBalance에 그 값을 저장해주고 메소드가 종료됩니다.<br><br>
**C++**에서는 이렇게 **포인터를 이용**하여 **Main에 위치한 변수 totalIncome, totalOutgo, totalBalance의 값을 변경시켜줌**으로써 마치 **3개의 변수 값을 출력**한 것과 같은 효과를 볼 수 있습니다.

# 반환값이 여러개인 C++의 메소드를 Java에서 어떻게 표현할 것인가?
Java에서 어떻게 한번에 totalIncome, totalOutgo, totalBalance를 한번에 출력할 수 있을지 고민을 해봤습니다.<br><br>
고심 끝에 두 가지 방법을 생각해냈습니다.
## 첫번째 방법 - calculate 메소드 오버로딩
처음에는 저번 글에서 correct에서 오버로딩(금액만 수정하는 correct, 비고만 수정하는 correct, 금액과 비고 둘 다 수정하는 correct)했던 것을 생각해봤습니다.<br>
<a href="https://injae7034.github.io/java/twentyFirst/#%ED%95%B4%EA%B2%B0%EC%B1%852---correct-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%ED%95%98%EA%B8%B0"
target="_blank">AccountBook 클래스 - correct 메소드 오버로딩하기</a>

<br><br>

correct에서 했던 것처럼 **calculate도 오버로딩**시켜서 **총수입만 계산하는 calculate, 총지출만 계산하는 calculate** 이렇게 **2개의 메소드를 별도로 정의**하여 **따로 구한 총수입과 총지출을 통해 총잔액을 구하는 방식**에 대해 생각해봤습니다.<br><br>
메소드에서 한 번에 여러가지를 다 처리하는 것보다는 이렇게 기능을 잘게 쪼개놓으면 이 메소드가 어떤 기능을 하는지 명확하게 이해할 수 있기 때문에 나중에 유지 보수 관리 차원에서도 수월하기 때문입니다.
### calculate 메소드 오버로딩의 한계점
그러나 결정적으로 망설여진게 현재 로직에서는 가계부에서 일정기간동안의 account항목을 Income, Outgo 따지지 않고 모두 구하여서 반복을 돌리면서 총수입과 총지출을 구하고 있기 때문입니다.<br><br>
즉, 총수입만 구하는 쪽으로 로직을 수정한다고 하더라도 account가 Income일수도 있고, Outgo일수도 있기 때문에 항상 Income인지 확인하여 Income이면 총수입을 누적시켜야 합니다.<br><br>
총지출을 구하는 경우는 Outgo인지 확인하여 Outgo이면 총지출을 누적시켜야합니다.<br><br>
즉, **총수입을 구하는 calculate와 총지출을 구하는 calculate가 똑같은 처리과정**인데 이 과정을 **2개로 분리**하면 이 **똑같은 처리과정을 중복해서 2번**하기 때문에 시간낭비가 발생합니다.<br><br>
그럴바에 차라리 총수입을 구할 때 어차피 Income인지 Outgo인지 확인하여 Income이면 총수입에 누적을 시켜주고, Outgo이면 총지출에 누적시킨 다음, 마지막에 메소드 종료 전에 총수입과 총지출을 바탕으로 총잔액을 구해주면 시간적인 면에서 훨씬 효율적입니다.

## 두번째 방법 - Map\<K, V\>을 반환값으로 사용
다시 원점(calculate 메소드 하나를 이용하는 방법)으로 돌아와 생각해봤습니다.<br><br>
그 때 떠오른 생각이 바로 해쉬맵(HashMap)을 이용하는 방법이었습니다.<br><br>
먼저 HashMap\<K, V\>에서 Key값을 String(문자열)으로 하고 Value값을 Integer(int자료형의 wrapping클래스)로 설정합니다.<br><br>
"totalIncome"이라는 문자열을 key값으로 설정하고, int totalIncome 변수를 value값으로 설정합니다.<br><br>
마찬가지로 "totalOutgo"라는 문자열을 key값으로 설정하고, int totalOutgo 변수를 value값으로, "totalBalance"라는 문자열을 key값으로 설정하고, int totalBalance 변수를 value값으로 설정합니다.<br><br>
좀 더 자세한 설명을 위해 java에서 HashMap을 사용하여 구현한 calculate 메소드의 코드를 첨부합니다.

### java에서 구현한 calculate 메소드
```java
public class AccountBook
{
    //Calculate
    public Map<String, Integer> calculate(Date fromDate, Date toDate)
    {
        //반환할 해쉬맵을 생성한다.
        Map<String, Integer> totalOutcomes = new LinkedHashMap<>();
        //같은 date들의 위치를 저장할 임시공간인 ArrayList 생성하기
        ArrayList<Integer> sameDates = null;
        int totalIncome = 0;//총수입을 저장할 공간
        int totalOutgo = 0;//총지출을 저장할 공간
        Account account = null;//Account객체를 담을 임시공간
        //fromDate가 toDate보다 작거나 같은동안 반복한다.
        while(fromDate.isGreaterThan(toDate) == false)
        {
            //가계부(AccountBook)에서 fromDate와 같은 date를 가진
            //Account객체들의 위치를 찾아서 그 위치를 저장한 배열을 반환한다.
            sameDates = this.find(fromDate);
            //같은 dates의 위치를 저장한 배열의 처음부터 마지막까지 반복한다.
            for(int i = 0; i < sameDates.size(); i++)
            {
                //account를 구한다.
                account = this.getAt(sameDates.get(i));
                //account가 수입(Income)이면
                if(account instanceof Income)
                {
                    totalIncome += account.getAmount();
                }
                //account가 지출(Outgo)이면
                else
                {
                    totalOutgo += account.getAmount();
                }
            }
            //fromDate를 1일 증가시켜준다.
            fromDate = Date.nextDate(fromDate, 1);
        }
        //총잔액(totalBalance)을 구한다.
        int totalBalance = totalIncome - totalOutgo;
        //해쉬맵에 결과를 저장한다.
        totalOutcomes.put("totalIncome", totalIncome);
        totalOutcomes.put("totalOutgo", totalOutgo);
        totalOutcomes.put("totalBalance", totalBalance);
        //해쉬맵을 반환한다.
        return totalOutcomes;
    }
}
```
메소드 반환형을 Map\<String, Integer\>로 설정하고, 매개변수로 Date클래스를 2개(일정기간을 의미함)를 받습니다.<br><br>
메소드 내부에서 반환할 Map\<String, Integer\> totalOutcomes를 LinkedHashMap의 생성자로 생성합니다.<br><br>
LinkedHashMap을 사용하는 이유는 HashMap은 그 특성상 입력 순서와 출력 순서가 일치하지 않지만, LinkedHashMap은 입력 순서와 출력 순서가 동일하게 관리되기 때문입니다.<br><br>
이후 같은 가계부에서 같은 date를 가진 Account 객체들의 위치를 저장할 임시공간인 ArrayList\<Integer\> sameDates를 null로 초기화시켜줍니다.<br><br>
그리고 총수입을 저장할 int totalIncome과 총지출을 저장할 int totalOutgo를 0으로 초기화시켜줍니다.<br><br>
while반복문에서 Date 클래스의 isGreaterThan 메소드를 호출하여 fromDate가 toDate보다 작거나 같은동안 반복을 합니다.<br>
<a href="https://injae7034.github.io/java/nineteenth/#isgreaterthan-%EB%A9%94%EC%86%8C%EB%93%9C"
target="_blank">Date 클래스 - isGreaterThan 메소드</a>

<br><br>
반복문 내부에서 AccountBook의 find(fromDate)메소드를 호출하여 sameDates(가계부에서 fromDate와 같은 일자를 가진 Account객체들의 배열첨자 위치를 저장하고 있는 ArrayList\<Integer\>)를 반환받습니다.<br>
<a href="https://injae7034.github.io/java/twentyFirst/#finddate"
target="_blank">AccountBook 클래스 - find(date) 메소드</a>

<br><br>


for 반복문에서는 반복제어변수 i = 0부터 sameDates의 size만큼 반복을 돌면서 순차적으로 가계부에서 fromDate와 같은 일자를 가진 Account 객체를 구해서 Account 객체가 Income이면 totalIncome에 amount값을 누적시켜 주고, Outgo이면 totalOutgo에 amount값을 누적시켜줍니다.<br><br>
for 반복문이 끝나면 fromDate를 1일 증가시켜주기 위해 Date클래스의 nextDate 메소드를 호출합니다.<br>
<a href="https://injae7034.github.io/java/nineteenth/#%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-2%EA%B0%9Cdate%EA%B0%9D%EC%B2%B4-%EC%B0%B8%EC%A1%B0%EB%B3%80%EC%88%98%EA%B0%92-int-%EC%9E%90%EB%A3%8C%ED%98%95-%EB%B3%80%EC%88%98%EB%A5%BC-%EA%B0%80%EC%A7%80%EB%8A%94-nextdate"
target="_blank">Date 클래스 - nextDate(Date date, int days) 메소드</a>

<br><br>

이후 다시 while반복문의 조건으로 가서 fromDate와 toDate의 대소비교를 한다음 여전히 fromDate가 toDate보다 작거나 같으면 반복문을 다시 실행하고, 그게 아니면 반복문을 빠져나옵니다.<br><br>
이후 누적된 totalIncome에 누적된 totalOutgo를 뺀 값을 int totalBalancer변수에 저장합니다.<br><br>
그리고 드디어 해쉬맵을 이용하는데 Map의 객체인 totalOutcomes에 put메소드를 이용하여 key값이 "totalIncome", value값이 totalIncome변수인 Entry를 추가합니다.<br><br>
같은 방식으로 totalOutgo와 totalBalance를 put합니다.<br><br>
LinkedHashMap이기 때문에 입력순서와 동일하게 저장됩니다.<br><br>
이제 이 **Map을 반환**하면서 **calculate메소드가 종료**되는데, 나중에 **Main에서 이 Map에 저장된 정보를 활용**하면 **총수입(totalIncome)과 총지출(totalOutgo), 총잔액(totalBalance)의 값을 알아낼 수 있습니다.**
<br><br>
Map의 메소드인 get에 매개변수로 Key값을 전달하면 원하는 Value값을 얻을 수 있습니다.<br><br>
예를 들어, Main에서 가계부를 생성한 다음에 13개의 account항목을 record한 다음에 calculate 메소드를 호출해보도록 하겠습니다.<br><br>
calculate로 반환 받은 Map의 객체에 각각 Key값을 get메소드에 전달하여 원하는 Value값을 얻을 수 있습니다.<br><br>
코드는 아래와 같습니다.

```java
import java.util.Map;

public class Main
{
    public static void main(String[] args)
    {
		//가계부를 만든다.
        AccountBook accountBook = new AccountBook();
        //1. 수입을 기재한다.
        int index = accountBook.record(new Date("20211125"), "월급",
                2370000, "회사");
        System.out.print("1. 수입 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //2. 지출을 기재한다.
        index = accountBook.record(new Date(2021, 11, 25), "식사",
                -9000, "혼자");
        System.out.print("2. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //3. 지출을 기재한다.
        index = accountBook.record(new Date("20211125"), "식사",
                -12000, "친구");
        System.out.print("3. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //4. 지하철을 기재한다.
        index = accountBook.record(new Date("20211125"), "교통비",
                -55000, "지하철");
        System.out.print("4. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //5. 지출을 기재한다.
        index = accountBook.record(new Date("20211126"), "회식",
                -30000, "친구");
        System.out.print("5. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //6. 수입을 기재한다.
        index = accountBook.record(new Date("20211126"), "캐쉬백",
                7500, "체크카드");
        System.out.print("6. 수입 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //7. 지출을 기재한다.
        index = accountBook.record(new Date("20211127"), "통신비",
                -58000, "핸드폰");
        System.out.print("7. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //8. 지출을 기재한다.
        index = accountBook.record(new Date("20211128"), "쇼핑",
                -49000, "신발");
        System.out.print("8. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //9. 수입을 기재한다.
        index = accountBook.record(new Date("20211129"), "중고판매",
                23000, "신발");
        System.out.print("9. 수입 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //10. 지출을 기재한다.
        index = accountBook.record(new Date("20211129"), "식사",
                -11000, "혼자");
        System.out.print("10. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //11. 지출을 기재한다.
        index = accountBook.record(new Date("20211130"), "식사",
                -13500, "동생");
        System.out.print("11. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //12. 지출을 기재한다.
        index = accountBook.record(new Date("20211130"), "식사",
                -6000, "혼자");
        System.out.print("12. 지출 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //13. 수입을 기재한다.
        index = accountBook.record(new Date("20211130"), "중고판매", 33000, "후라이팬");
        System.out.print("13. 수입 기재 -> ");
        System.out.println(accountBook.getAt(index));
        //14. 전체기간동안 가계부를 계산한다.
        System.out.print("14. 전체기간동안 가계부 계산하기 : ");
        Map<String, Integer> totalOutcomes = accountBook.calculate(new Date("20211125"),
                new Date(2021, 11, 30));
        System.out.println(totalOutcomes);
        //15. 총수입을 구해서 출력한다.
        System.out.println("15. 총수입 : " + totalOutcomes.get("totalIncome"));
        //16. 총지출을 구해서 출력한다.
        System.out.println("16. 총지출 : " + totalOutcomes.get("totalOutgo"));
        //17. 총잔액을 구해서 출력한다.
        System.out.println("17. 총잔액 : " + totalOutcomes.get("totalBalance"));
	}
}
```
<br>
![calculate결과](../../images/2022-01-21-twentySecond/calculate결과.JPG)
<br><br>

즉, 이 **Map의 Key값을 활용**하면 C++에서 포인터를 이용하여 반환값이 3개인 calculate를 구현한 것처럼 **Java에서도 비록 반환값은 Map 1개이지만 마치 3개의 출력값을 얻는 효과(?)**를 누릴 수 있습니다.
