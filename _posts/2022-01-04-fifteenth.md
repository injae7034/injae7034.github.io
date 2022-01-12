---
layout: single
title:  "Java프로젝트하면서 LinkedList에서 얕은 복사 깊은 복사 설명하기"
categories: Java
tag: [Java, 명함철, 프로젝트, VisitingCardBinder, Cloneable, toString, equals, 오버라이딩, 얕은 복사, 깊은 복사, 메모리맵]
toc: true
toc_label: "목차"
toc_sticky: true
---
# 도메인 용어 정의
저번 시간에 만들었던 VisitingCard클래스를 관리하는 control인 명함철, **VisitingCardBinder를 구현하면서 LinkedList에서 얕은 복사 깊은 복사를 설명**하도록 하겠습니다.<br><br>
명함철을 구현할 때는 저번에 주소록과는 다른 도메인 용어를 사용하려고 합니다.<br><br>
주소록에서는 주소록이라는 용지에 개인의 정보를 기재하는 것이 맞습니다.<br><br>
하지만 명함철의 경우 명함철에 명함의 내용을 기재하는 것이 아니라 개인과 회사의 정보가 이미 담긴 명함을 명함철에 끼워서 관리하는 것이기 때문에 새로운 명함을 명함철에 추가할 때는 **끼우기(takeIn)**라는 용어를 사용합니다.<br><br>
**찾기(find)**와 **정리하기(arrange)**는 맥락이 같기 때문에 그대로 사용합니다.<br><br>
그리고 주소록에서 '지우기'대신에 명함철에서는 해당 명함을 **꺼내기(takeOut)**으로 대체합니다.<br><br>
# ArrayList(주소록)와 LinkedList(명함철)의 비교하여 설계하기
ArrayList를 이용한 주소록은 배열의 특성을 살려 표(table)처럼 나타냈습니다.<br><br>
그래서 표에서 몇 번(배열첨자)에 누가 있다(Personal객체) 이렇게 쉽게 찾았습니다.<br><br>
그러나 연결리스트의 경우 배열이 아니기 때문에 index로 접근하는 것은 조금 어색한 일입니다.<br><br>
(물론 LinkedList<E>의 경우 index로도 접근을 할 수 있지만 최대한 되는 선에서는 index로 접근하는 것을 피해서 구현해볼 생각입니다.)<br><br>
연결리스트, 특히, 이중연결리스트는 Node가 item(내용, 여기선 VistingCard)를 가지고 있고, 자신의 이전 Node와 자신의 다음 Node 주소를 가지고 이를 이용해 순차적으로 이동하면서 검색을 합니다.<br><br>
(이런 방식이기 때문에 확실히 ArrayList에 비해서 검색 속도가 늦을 수 밖에 없습니다.<br><br>
ArrayList의 경우 배열첨자로 바로 접근을 할 수 있으나 LinkedList의 경우 처음부터 시작해서 주소로 이동을 하면서 접근을 해야하기 때문입니다.)<br><br>
(물론 ArrayList의 경우 처음 또는 배열요소 사이에 삽입 및 삭제를 할 경우 뒤에 있는 배열요소들의 배열첨자들이 한칸씩 뒤로 밀리기 때문에 이를 반영하려면 엄청나게 시간이 소모됩니다.<br><br>
반면 LinkedList의 경우 처음 또는 사이에 삽입 및 삭제를 하더라도 관계되는 Node의 이전, 다음 주소값만 변경해주기 때문에 손쉽게 삽입 및 삭제를 할 수 있습니다.)<br><br>
아무튼 이런 LinkedList의 특성을 살려 VisitingCardBinder의 경우 나중에 CUI를 구성할 때 배열처럼 테이블형식은 지양하고 하나의 명함만 보여주는 방식으로 구성하려고 합니다.<br><br>
명함철에서 현재의 명함을 보여주도록 구성할건데 그러기 위해서 VisitingCardBinder의 필드멤버로 **현재 명함을 저장할 current라는 참조변수**를 만들었습니다.<br><br>
또한 명함철에서 현재 명함을 이동시키면서 명함을 볼 수 있도록 구현하기 위해 **현재 명함을 이동시키는 메소드**들을 구현하였습니다.<br><br>

# VisitingCardBnider 필드
VisitingCardBinder클래스의 필드로는 LinkedList<VistingCard>의 주소를 저장하는 참조변수 visitingCards, 현재 명함의 주소를 저장하고 있는 참조변수 current, 현재 명함철에 끼워진 명함의 개수를 나타내는 length가 있습니다.<br>
```java
public class VisitingCardBinder
{
    //인스턴트 필드멤버
    private LinkedList<VisitingCard> visitingCards;
    private VisitingCard current;//명함철에서 현재 카드
    private int length;
}
```

# 생성자
생성자로는 매개변수가 없는 디폴트 생성자 한 개를 정의했습니다.<br><br>
```java
public class VisitingCardBinder
{
    //디폴트 생성자
    public VisitingCardBinder()
    {
        this.visitingCards = new LinkedList<VisitingCard>();
        this.current = null;
        this.length = 0;
    }
}
```

# takeIn
```java
public class VisitingCardBinder
{
    //takeIn
    public VisitingCard takeIn(VisitingCard visitingCard)
    {
        //LinkedList에 매개변수로 입력 받은 VisitingCard객체를 순차적으로 끼운다.
        this.visitingCards.add(visitingCard);
        //개수를 증가시킨다.
        this.length++;
        //현재 명함의 위치를 저장한다.
        this.current = this.visitingCards.getLast();
        //끼운 명함을 출력한다.
        return this.current;
    }
}
```
## takeIn 설명
우선 명함철(VisitingCardBinder)에 끼울 명함(visitingCard)를 매개변수로 입력 받습니다.<br><br>
입력 받은 명함을 LinkedList<VisitingCard>의 add 메소드를 호출하면서 매개변수로 전달합니다.<br><br>
그리고 명함철의 명함 개수를 늘려주고, 현재 명함의 위치를 마지막 명함으로 설정합니다.<br><br>
그 이유는 LinkedList가 add를 시킬 때 순차적으로 추가하기 때문에 가장 최근에 들어온 visitingCard가 마지막으로 설정되기 때문입니다.<br><br>
그 다음 현재 명함의 위치를 반환합니다.

### LinkedList의 add
LinkedList<E>가 어떻게 돌아가는지 보기 위해 add의 코드를 보면 다음과 같습니다.<br>
```java
public boolean add(E e) {
        linkLast(e);
        return true;
    }
```
여기서 E는 VisitingCard로 정해졌으니 매개변수로 VisitingCard의 객체를 입력받아 내부에서 다시 linkLast라는 메소드를 호출하면서 매개변수로 VisitingCard객체를 전달합니다.

### LinkedList의 linkLast
그럼 이제 linkLast는 어떻게 돌아가는지 보기 위해 작성된 코드를 보면 다음과 같습니다.<br>
```java
void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
```
linkLast를 설명하기 전에 LinkedList의 멤버롤 먼저 살펴보도록 하겠습니다.<br><br>
왜냐하면 LinkedList의 멤버를 정확하게 알아야 linkLast의 코드를 제대로 이해할 수 있기 때문입니다.
### LinkedList<E>의 멤버
```java
ublic class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    transient int size = 0;

    /**
     * Pointer to first node.
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     */
    transient Node<E> last;

     private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
```
size는 LinkedList<E>에서 연결된 Node<E>의 개수, first는 LinkedList에 연결된 Node<E>중에서 첫번째 Node<E>, last는 마지막 Node<E>를 의미합니다.<br><br>
LinkedList<E>의 이너클래스인 Node<E>는 내부에 자료형이 E인 item과 자신의 다음 Node<E>의 주소, 이전 Node<E>의 주소를 가지고 있습니다.<br><br>
E를 현재 VisitingCard로 정했기 때문에 item이 VisitingCard의 객체를 저장하는 주소입니다.
### LinkedList<E>의 linkLast 설명
VisitingCard객체를 매개변수로 입력 받아서 내부에서 새로운 Node객체인 newNode를 생성합니다.<br><br>
newNode를 생성할 때 매개변수로 LinkedList<VisitingCard>의 현재 마지막 노드의 주소를 prev로 입력받습니다.(마지막 위치에 추가되기 때문에 현재 LinkedList의 마지막 위치가 자연스럽게 마지막 전의 위치가 되기 때문)<br><br>
Node의 생성자에서 next의 매개변수로는 null을 입력받습니다.(LinkedList의 마지막에 추가되기 때문에 다음이 있을 수 없음.)<br><br>
Node의 생성자에서 item으로는 linkLast에서 매개변수로 입력받은 VisitingCard객체를 입력받습니다.<br><br>
LinkedList의 last는 새로 생성한 newNode로 변경하여주고, 현재 LinkedList에 node가 newNode하나뿐이면 first도 newNode를 가리키도록 설장하고, 그게 아니면 newNode를 생성하기 전까지 마지막 node였던 'l'의 next를 null에서 newNode로 변경해줍니다.<br><br>
이 방향(주소)설정으로 인해 새로 생성한 newNode는 이제 LinkedList와 연결이 되었기 때문에 LinkedList에 연결된 Node의 size(개수)를 증가시켜줍니다.<br><br>

### LinkedList의 getLast
```java
public E getLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return l.item;
    }
```
코드를 보면 LinkedList의 last(마지막 Node)가 가지고 있는 item(VisitingCard객체)을 반환하는 것을 알 수 있습니다.<br><br>
저희가 이용할 때는 항상 add를 통해 먼저 새로운 Node가 추가된 뒤에 getLast를 호출하기 때문에 last가 null인 경우는 발생하지 않기 때문에 NoSuchElementException은 걱정하지 않아도 됩니다.<br><br>
이제 여기까지의 코드를 이해하기 쉽게 제가 이해하려고 그렸던 메모리맵을 첨부하여 설명하도록 하겠습니다.

### takeIn 메모리맵
![LinkedList add 메모리맵](../../images/2022-01-04-fifteenth/LinkedList add 메모리맵.jpg)
스택영역의 경우 사실 아래에서 위로 쌓이는 것이 맞는데 힙영역과 같이 그리다보니 편의상 위에서 아래로 쌓이게 그렸습니다.<br><br>
이점 양해 부탁드립니다<br><br>
그리고 String역시 객체이기 때문에 저렇게 기본 자료형처럼 작성하면 안되지만 String의 특성상 기본자료형과 같이 작성해도 크게 문제가 되지 않을 것 같아서 편의상 기본 자료형처럼 바로 저장공간에 내용을 작성하였습니다.<br><br>
이제 설명을 해보자면 먼저 main에 있는 참조변수 origin은 힙영역에 있는 VisitingCardBinder객체의 주소를 저장하고 있습니다.<br><br>
우리는 이 origin을 통해 힙영역에 할당된 VisitingCardBinder에 접근할 수 있습니다.<br><br>
VisitingCardBinder는 LinkendList<VistingCard>의 참조변수인 visitingCards를 가지고 있고, 현재명함의 주소값을 저장하는 참조변수인 current와 현재 명함철에 끼워진 명함의 개수를 의미하는 length를 멤버로 가지고 있습니다.<br><br>
현재 명함철에 끼운 명함이 하나도 없기 때문에 current는 null을, length는 0을 저장하고 있습니다.<br><br>
LinkedList<VisitingCard>의 멤버인 size도 역시 끼워진 Node가 없기 떄문에 size는 0, first와 last는 null을 저장하고 있습니다.<br><br>
이 상태에서 Main에서 VisitingCard 객체를 생성합니다.<br><br>
객체는 필드가 너무 많아서 간추려서 Personal의 경우 name = "홍길동", Compamy의 경우 name = "삼성전자"를 가지고 있는 것만 표시하고 나머지는 생략합니다.<br><br>


여기서 중요한 점이 **VisitingCard는** Personal과 Company의 내용을 필드멤버로 직접 가지고 있는 것이 아니라 **위의 메모리맵처럼 Personal객체의 참조변수(주소)값과 Company객체의 참조변수(주소)값을 멤버로 가지고 있다**는 점입니다.<br><br>
이게 제대로 잡히지 않으면 깊은 복사와 얕은 복사를 할 때 헷갈리게 됩니다.<br>

<a href="https://injae7034.github.io/java/fourteenth/#clone-%EB%A9%94%EC%86%8C%EB%93%9C-%EB%AC%B8%EC%A0%9C%EC%A0%90--%EC%96%95%EC%9D%80-%EB%B3%B5%EC%82%AC" target="_blank">VisitingCard의 clone구현시 문제점 : 얕은 복사</a>

<br><br>
새로 생성한 이 VisitingCard객체를 매개변수로 하여 origin에서 takeIn메소드를 호출합니다.<br><br>
takeIn은 다시 add메소드를 호출하면서 VisitingCard객체의 매개변수를 전달해줍니다.<br><br>
add는 다시 내부에서 linkLast라는 메소드를 호출하면서 VisitingCard객체의 매개변수를 전달해줍니다.<br><br>
이제 linkLast의 내부에서 'l'은 마지막 Node의 위치를 저장하라고 하는데 현재 LinkedList<VisitingCard>의 객체인 visitingCards에는 아무것도 연결된 Node가 없기 때문에 last 값이 null입니다.<br><br>
그래서 'l'은 처음에 null이 저장됩니다.<br><br>
그리고 새로운 노드(newNode)를 생성하는데 이때 prev값으로 l(=null)이 들어가기 때문에 새로 생성되는 newNode는 prev와 next는 모두 null이고, item은 visitingCard의 참조변수값을 가집니다.<br><br>
newNode를 생성했으니 이제 이를 LinkedList<VisitingCard>객체와 연결시키기 위해 

**last = newNode**를 하는데 이로써 새로 생성한 newNode를 LinkedList의 마지막 node로 설정됩니다.<br><br>
그리고 현재 l=null이기 때문에 선택문으로 들어가서 first역시 newNode를 가리키게 됩니다.<br><br>
즉, LinkedList에 처음으로 Node를 연결시키면 first와 last가 동일한 Node객체를 가리키게 됩니다.<br><br>
이제 LinkedList에 연결된 Node가 한 개 생겼기 때문에 size는 1로 변경해줍니다.<br><br>
이제 linkLast가 종료되었으니 linkLast스택이 사라지고, 다음으로 이를 호출한 add 스택이 사라지고, 다음에 takeIn으로 다시 돌아갑니다.<br><br>
돌아가서 VisitingCardBinder에 이제 명함이 끼워졌으니 length를 증가시켜 주고, current를 구하기 위해 getLast메소드를 호출합니다.<br><br>
getLast에서 l=last를 대입하는데, last는 아까 새로 생성한 newNode를 가리키고 있습니다.<br><br>
그럼 l.item은 아까 Main에서 새로 생성한 VisitingCard객체의 주소를 저장하고 있기 때문에 이 참조변수값을 getLast에서 return합니다.<br><br>
그러면 getLast스택이 종료되고, 종료되면서 반환 받은 VisitingCard객체의 참조변수값이 current에 저장됩니다.<br><br>그리고 takeIn은 이 현재 명함의 주소를 반환하면서 종료됩니다.<br><br>

## 두번째 끼울 때 takeIn메모리맵
이제 두번째 명함을 끼울 때는 어떻게 되는지 보도록 하겠습니다.<br><br>
![LinkedList add 메모리맵2](../../images/2022-01-04-fifteenth/LinkedList add 메모리맵2.jpg)
VisitingCardBinder는 이제 아까 새로 추가된 내용을 반영하여 length는 1이고 current도 아까 추가된 VisitingCard객체를 가리키고 있습니다.<br><br>
LinkedList역시 이제 size는 1이고 first와 last는 동일한 Node를 가리키고 있습니다.<br><br>
Node는 item으로 VisitingCard객체의 주소를 가리키고 있고, next와 prev는 현재 null인 상태입니다.<br><br>
이 상황에서 다시 Main애서 personalName이 "정길동"이고, companyName이 "엘지전자"인 VisitingCard객체를 생성합니다.<br><br>
이 VisitingCard객체의 참조변수값을 아까처럼 매개변수로 넘겨주어 linkLast까지 호출됩니다.<br><br>
linkLast에서 l = last인데 last는 현재 LinkedList의 첫번째이자 마지막인 단 한개의 Node를 가리키고 있습니다.<br><br>
Node<E> newNode = new Node<>(l, e, null);을 통해 'l'은 prev위치인데 여기에 첫번째 노드의 주소값이 들어가고, 'e'는 item으로 VisitingCard객체의 주소값, null은 next의 위치인데 next는 null을 가지는 newNode가 생성됩니다.<br><br>
last = newNode를 대입함으로써 last는 newNode를 가리키게 됩니다.<br><br>
그리고 first는 더이상 null이 아니라 else구문으로 들어가는데 여기서 l은 last가 바뀌지 전에 가리켰던 첫번째 노드를 가리키고 있습니다.<br><br>
그래서 l.next = newNode를 대입하면 이제 첫번째 노드의 next는 새로 생성한 newNode를 가리키게 됩니다.<br><br>
그러면 이제 메모리맵에서 보시다시피 연결리스트의 first와 last는 각자 다른 노드를 가리키고 있고, 첫번째 노드의 prev는 여전히 null이지만 next는 새로 생성한 노드를 가리키고 있으며, 두번째이자 마지막 노드의 prev는 첫번째 노드를 가리키고, next는 null인 상태이며, item은 Personal("정길동")과 Company("엘지전자")의 참조변수값을 가지는 VisitingCard객체의 참조변수값을 가지고 있습니다.<br><br>
이렇게 되면 LinkedList에 이제 연결된 Node가 2개이기 때문에 size는 2가 되고, VisitingCardBinder에도 끼워진 명함이 2개이기 때문에 length는 2가 되고, current는 가장 최근에 추가된 VisitingCard객체의 참조변수값을 가지게 됩니다.

## 세번째 끼울 때 takeIn메모리맵
세번째의 경우 두번째와 과정이 동일하기 때문에 설명은 생략하고 메모리맵 그림만 첨부하도록 하겠습니다.
![LinkedList add 메모리맵3](../../images/2022-01-04-fifteenth/LinkedList add 메모리맵3.jpg)

# clone
이제 takeIn을 다 설명했으니 위의 takeIn에서 이용한 메모리맵을 이용하여 이와 연관된 clone을 설명하도록 하겠습니다.<br><br>
그리고 저번 글에서 설명했던 Personal과 Company, VisitingCard의 clone코드도 여기에 다시 첨부하여 같이 설명하도록 하겠습니다.
## VisitingCardBinder 깊은 복사 clone
```java
public class VisitingCardBinder implements Cloneable
{
    //clone 메소드 오버라이딩하기
    @Override
    public VisitingCardBinder clone() throws CloneNotSupportedException
    {
        //복사할 대상 생성
        VisitingCardBinder visitingCardBinder = new VisitingCardBinder();
        //for each문으로 VisitingCard를 마지막까지 반복하기
        for (VisitingCard visitingCard : this.visitingCards)
        {
            //VisitingCard를 clone한 반환값(VisitingCard복사본)을 명함철(복사본)에 추가하기
            visitingCardBinder.visitingCards.add(visitingCard.clone());
            //명함철(복사본)의 현재 위치를 마지막 명함위치로 설정하기
            visitingCardBinder.current = visitingCardBinder.visitingCards.getLast();
            //명함철(복사본)의 길이를 증가시키기
            visitingCardBinder.length++;
        }
        //명함철(복사본) 반환
        return visitingCardBinder;
    }
}
```
## VisitingCard의 얕은 복사 clone
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
## VisitingCard의 깊은 복사 clone
```java
public class VisitingCard implements Cloneable
{
    //Cloneable 의 clone 메소드 오버라이딩하기(구현하기)
    @Override
    public VisitingCard clone() throws CloneNotSupportedException
    {
        VisitingCard visitingCard = new VisitingCard(this.personal.clone(), this.company.clone());
        return  visitingCard;
    }
}
```
## Personal의 clone
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
## Company의 clone
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

### clone 첫번째 메모리맵
![VisitingCardBinder clone 메모리맵1](../../images/2022-01-04-fifteenth/VisitingCardBinder clone 메모리맵1.jpg)
먼저 Main에 VisitingCardBinder의 객체인 origin이 있고, 아까 메모리맵처럼 현재 takeIn을 3번하여, 명함이 3개 끼워져 있는 상태입니다.<br><br>
이 상태에서 **copy = origin.clone()**을 호출하면 먼저 VisitingCardBinder의 clone메소드가 호출됩니다.<br><br>
여기서 **VisitingCardBinder visitingCardBinder = new VisitingCardBinder();**을 통해 새로운 VisitingCardBinder객체를 생성하여 힙에 할당합니다.<br><br>현재 새로 생성된 이 VisitingCardBinder는 메모리맵에서 보시다시피 current는 null이고 length는 0이며 LinkedList<VisitingCard>객체인 visitingCards역시 size는 0이고 first와 last 모두 null인 상태입니다.<br><br>
다음으로 for each 반복문을 통해 origin의 LinkedList<VisitingCard>에서 VisitingCard객체를 하나씩 가져오면서 여기서 VisitingCard의 clone메소드를 호출합니다.<br><br>
그러면 스택영역에 VisitingCard clone메소드 영역이 생성되고, VisitingCardBinder의 clone은 잠시 멈춥니다.<br><br>
VisitingCard clone내부에서 VisitingCard visitingCard = new VisitingCard(this.personal.clone(), this.company.clone());를 통해 먼저 personal의 clone메소드가 호출되고 다음으로 company의 메소드가 호출됩니다.<br><br>
Personal의 clone메소드는 return (Personal)super.clone();한문장인데 이를 통해 자신과 똑같은 내용의 Personal객체가 깊은 복사가 되어(모든 필드가 String이기 때문에) 그 참조변수값이 반환됩니다.<br><br>
Company의 clone메소드 역시 Personal과 똑같고, 다시 VisitingCard의 clone메소드로 돌아가면 새로운 VisitingCard객체를 생성하는데 생성자의 매개변수로 깊게 복사된 Personal과 Company객체의 참조변수가 들어갑니다.<br><br>
그렇게 되면 위의 그림에서 보다시피 새로 생성한 VisitingCard는 복사본과는 별개의 Personal과 Company객체를 가지게 됩니다.<br><br>
이제 이 값을 반환하면 VisitingCard의 clone 메소드는 종료됩니다.<br><br>
VisitingCard의 clone메소드가 종료되었으니 다시 VisitingCardBinder의 clone메소드로 돌아가면 이 깊게 복사된 VisitingCard객체를 새로 생성한 비어있는 VisitingCardBinder의 LinkedList에 add시킵니다.<br><br>
그리고 current는 새로 추가한 VisitingCard객체의 참조변수값을 설정하고, length를 증가시켜줍니다.<br><br>
이렇게 for each 반복을 돌면 3번을 반복하게 되는데 반복의 결과 메모리맵을 첨부합니다.<br>

### clone 두번째 메모리맵
![VisitingCardBinder clone 메모리맵2](../../images/2022-01-04-fifteenth/VisitingCardBinder clone 메모리맵2.jpg)

### clone 세번째 메모리맵
![VisitingCardBinder clone 메모리맵3](../../images/2022-01-04-fifteenth/VisitingCardBinder clone 메모리맵3.jpg)

### VisitingCard 얕은 복사 시 메모리맵
위에서 언급한대로 VisitingCard의 얕은 복사 코드를 이용하면 어떻게 되는지 메모리맵으로 알아보도록 하겠습니다.<br><br> 얕은 복사의 경우 **return (VisitingCard) super.clone();** 이 한문장으로 VisitingCard clone메소드가 종료됩니다.
![VisitingCardBinder 얕은 복사 clone 메모리맵](../../images/2022-01-04-fifteenth/VisitingCardBinder 얕은 복사 clone 메모리맵.jpg)
이렇게 되면 메모리맵에서 보시다시피 Personal과 Company의 clone메소드는 호출되지 않습니다.<br><br>
그저 Object의 clone메소드가 호출되어 비어있는 복사본 VisitingCard를 새로 생성한 뒤에 원본의 참조변수값과 똑같은 참조변수값을 복사하여 Personal과 Company객체에 저장합니다.<br><br>
그럼 위의 메모리맵에서 보시다시피 복사본인 VisitingCard객체의 Personal과 Company는 원본의 Personal과 Company를 가리키고 있습니다.<br><br> 이런 상황에서 복사본의 Personal 또는 Company 객체의 필드값을 변경해버리면 당연히 원본의 내용도 변경되게 됩니다.<br><br>
즉, VisitingCard에서 단순히 **return (VisitingCard) super.clone();**를 하면 **Personal과 Company의 clone메소드는 호출되지 않는다는 점!** 자동으로 호출해주는 것은 없습니다.<br><br>
저도 처음에는 이렇게 하면 자동으로 Personal과 Company의 clone도 호출되어 깊은 복사가 되는 줄 알았는데 그런 건 없습니다.<br><br>
프로그래머가 직접 앞에서 정의한 Personal과 Company의 clone메소드를 여기서 호출하여야 합니다. 그래서 코드를 VisitingCard의 깊은 복사 코드처럼 짜야 합니다.<br><br>

# find
길었던 takeIn과 clone메소드 설명이 끝났으니 이제 find메소드로 넘어가도록 하겠습니다.<br>
```java
public class VisitingCardBinder implements Cloneable
{
    //find(개인 이름을 기준으로 명함철에서 명함찾기)(개인의 이름은 중복이 있을 수 있음)
    public ArrayList<VisitingCard> find(String personalName)
    {
        //찾고자 하는 이름과 같은 이름을 가진 명함의 참조변수값을 저장할 ArrayList 생성하기
        ArrayList<VisitingCard> indexes = new ArrayList<VisitingCard>();
        //for each 구문을 통해 처음부터 끝까지 반복을 돌리면서 찾고자 하는 이름과
        //같은 이름을 가진 명함의 참조변수값을 indexes에 저장하기
        for (VisitingCard visitingCard : this.visitingCards)
        {
            if(personalName.compareTo(visitingCard.getPersonalName()) == 0)
            {
                indexes.add(visitingCard);
            }
        }
        //결과를 반환한다.
        return indexes;
    }
}
```
## find 설명
명함철에서 개인의 이름을 기준으로 명함을 찾고 싶을 때 사용하는 메소드입니다.<br><br>
이름을 매개변수로 하고, 내부에서는 ArrayList<VisitingCard>의 객체인 indexes를 생성합니다.<br><br>
예를 들어, "홍길동"이라는 이름으로 명함을 찾을 때 "홍길동"이라는 개인이름(personalName)을 가진 명함(VisitingCard)을 발견되는대로 순차적으로 ArrayList<VisitingCard>에 저장합니다.<br><br>
즉, **indexes는 VisitingCard의 참조변수(주소)값을 배열요소로 하는 ArrayList**입니다.<br><br>
for each 반복문을 통해 VisitingCardBinder의 LinkedList<VisitingCard>객체인 visitingCards의 visitingCard를 하나씩 가져와서 매개변수로 입력 받은 personalName과 비교하여 같으면 indexes에 저장하도록 반복을 돌린 다음 반복문이 종료되면 indexes를 반환합니다.

# takeOut
명함철에서 명함을 꺼낼 때 takeOut메소드를 활용합니다.<br>
```java
public class VisitingCardBinder implements Cloneable
{
     //takeOut
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
}
```

## takeOut 설명
꺼내려고 하는 VisitingCard 객체의 참조변수값을 매개변수로 입력받습니다.<br><br>
원래 웬만하면 LinkedList에서 배열처럼 첨자 개념을 안쓰려고 했으나 제가 만든 라이브러리가 아니다 보니 사용에 제한이 있어서 어쩔 수 없이 첨자개념을 사용하게 됬습니다;;<br><br>
먼저 말씀드리는 것이 다음의 처리과정은 명함을 꺼냈을 때 현재 명함의 위치를 설정해주기 위한 과정입니다.<br><br>
앞에서 언급드렸듯이 CUI프로그래밍을 할 때 한 화면에 하나의 현재 명함을 보여주는 방식으로 구성하려고 하는데, 이 때 명함을 꺼내게 되면 현재 명함이 꺼내지기 때문에 꺼내진 명함을 대체할 명함을 현재 명함으로 설정해줄 필요가 있습니다.<br><br>
그래서 **항상 takeIn을 하거나 takeOut을 할 때는 현재 명함을 재설정해줘야 한다**는 것을 기억해주시면 됩니다.<br><br>
일단 명함을 명함철에서 꺼내기 전에 LinkedList의 **indexOf** 메소드를 통해 꺼내려는 명함의 첨자위치를 알아냅니다.<br><br>
반환 받은 index 값이 0보다 크다면 꺼내려는 명함이 처음이 아니라는 뜻이기 때문에 현재 명함의 위치를 꺼내려는 명함의 이전 위치로 이동시킵니다.<br><br>
index = 0 이면 현재 꺼내려는 명함이 처음 명함이기 때문에 꺼내려는 명함의 이전 위치로 이동하면 안되고, 다음 위치로 이동시켜줘야합니다.<br><br>
이 때, 현재 꺼내려는 카드가 명함철의 마지막 카드일 수 있기 때문에 이를 확인하여 현재 명함철의 명함 개수가 2개 이상인 경우 다음 위치로 이동시킵니다.<br><br>
현재 명함철의 명함 개수가 1개라면 현재 카드를 꺼내면 명함철에 더이상 카드가 없기 때문에 현재 카드의 참조변수값 current는 null로 설정해줍니다.<br><br>
그리고 LinkedList의 remove메소드를 통해 해당 명함을 꺼내고 명함철의 명함개수를 감소시켜 줍니다.<br><br>
명함철에 꺼내려는 명함이 없을 경우는 없기 때문에(현재 명함을 꺼내기 때문에)항상 꺼낸 명함의 참조변수값을 반환합니다.

# first
```java
public class VisitingCardBinder implements Cloneable
{
    //first(현재 명함의 위치를 처음으로 이동시킨다.)
    public VisitingCard first()
    {
        //현재 위치를 처음으로 이동시키기
        this.current = this.visitingCards.getFirst();
        //현재 위치를 출력하기
        return this.current;
    }
}
```

## first 설명
LinkedList의 getFirst를 통해 처음 명함의 참조변수 값을 반환받습니다.<br><br>
getFirst의 코드를 보면
```java
 public E getFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return f.item;
    }
```
처음 위치에 있는 Node의 item(VisitingCard)를 반환하는 것을 알 수 있습니다.<br><br>
이 반환 받은 VisitingCard객체의 주소를 현재 명함의 위치(current)로 설정하고, 이 참조변수값을 반환합니다.

# last
```java
public class VisitingCardBinder implements Cloneable
{
    //last(현재 명함의 위치를 마지막으로 이동시킨다.)
    public VisitingCard last()
    {
        //현재 위치를 마지막으로 이동시키기
        this.current = this.visitingCards.getLast();
        //현재 위치를 출력하기
        return this.current;
    }
}
```

## last 설명
LinkedList의 getLast를 통해 마지막 Node의 item인 VisitingCard객체의 값을 반환하는 것을 알 수 있습니다.<br><br>getLast의 경우 takeIn에서 이미 코드를 봤기 때문에 여기서는 따로 코드를 보지는 않겠습니다.<br><br>
아무튼 getLast를 통해 반환 받은 VisitingCard의 참조변수값을 현재 명함의 위치로 설정하고, 이 값을 반환합니다.

# previous
```java
public class VisitingCardBinder implements Cloneable
{
     //previous(현재 명함의 위치 이전으로 이동시킨다.)
    public VisitingCard previous()
    {
        //현재 위치 index를 구하기
        int index = this.visitingCards.indexOf(this.current);
        //현재 위치가 첫번째 명함이 아니면
        if(index > 0)
        {
            //현재 카드를 이전으로 이동시킨다.
            this.current = this.visitingCards.get(index - 1);
        }
        //현재 카드 위치를 출력한다.
        return this.current;
    }
}
```

## previous 설명
현재 명함의 이전 위치로 이동시키는 기능인데 우선 내부에서 LinkedList의 indexOf을 통해 현재 명함의 이전 위치를 배열첨자로 반환 받습니다.<br><br>
이 반환 받은 배열첨자가 0보다 크면, 즉, 현재 명함이 처음 명함이 아니면 현재 명함을 이전 명함으로 이동시키고, 현재 명함이 처음 명함이면 이동시키지 않습니다.<br><br>그런 다음 현재 명함의 값을 출력합니다.

# next
```java
public class VisitingCardBinder implements Cloneable
{
    //next(현재 명함의 위치를 다음으로 이동시킨다.)
    public VisitingCard next()
    {
        //현재 위치 index 구하기
        int index = this.visitingCards.indexOf(this.current);
        //현재 위치가 마지막 명함이 아니면
        int lastIndex = this.visitingCards.size() - 1;
        if(index < lastIndex)
        {
            //현재 카드를 다음으로 이동시킨다.
            this.current = this.visitingCards.get(index + 1);
        }
        //현재 카드 위치를 출력한다.
        return this.current;
    }
}
```

## next 설명
내부에서 현재 명함의 위치를 먼저 구합니다.<br><br>
그리고 마지막 명함의 index 위치를 구하는데 이는 현재 명함의 개수 - 1을 해주면 됩니다.<br><br>
첨자는 항상 0부터 시작하기 때문에 개수 - 1을 하면 마지막 첨자의 위치를 구할 수 있습니다.<br><br>
현재 명함의 위치가 마지막 첨자 위치보다 작으면 현재 명함이 마지막 명함이 아니라는 뜻이기 때문에 현재 명함을 다음으로 이동시켜줍니다.<br><br>
현재 명함의 위치가 마지막 명함의 위치와 같으면 이동시키지 않습니다.<br><br>
그런 다음 현재 명함의 위치를 반환합니다.

# arrange
```java
public class VisitingCardBinder implements Cloneable
{
    //개인성명을 기준으로 오름차순으로 정렬하기
    public void arrange()
    {
        Collections.sort(this.visitingCards, new Comparator<VisitingCard>() {
            @Override
            public int compare(VisitingCard one, VisitingCard other)
            {
                return one.getPersonalName().compareTo(other.getPersonalName());
            }
        });
        //현재 명함을 정렬한 후 제일 처음 명함으로 설정한다.
        this.current = this.visitingCards.getFirst();
    }
}
```

## arrange 설명
개인 성명을 기준으로 오름차순으로 설명하는데 이때 Collections 클래스의 스태틱 메소드인 sort함수를 이용합니다.<br><br>sort의 매개변수로 List와 Comparator를 입력해줘야 하는데, List는 자료형이 LinkedList<VisitingCard>인 visitingCards를 넘겨 주면 되고, 비교 기준인 Comparator의 경우 익명클래스로 Comparator를 정의하여 내부애서 compare를 오버라이딩한 익명클래스를 넘겨줍니다.<br><br>
compare에서는 개인의 성명으로 비교할 수 있게 개인의 성명으로 접근할 수 있도록 getPersonalName메소드를 호출해줍니다.<br><br>반환되는 name은 String이기 때문에 String의 메소드인 compareTo를 통해 같은 이름인지 아닌지를 판별합니다.<br><br>
그런 다음 정렬이 끝나면 현재 카드의 위치를 제일 처음 카드 위치로 변경합니다.<br>

# getter 메소드
명함철의 현재 명함의 위치를 구하는 getCurrent와 명함철의 명함개수를 구하는 getLength를 정의해줍니다.
```java
public class VisitingCardBinder implements Cloneable
{
     //현재 명함 위치 구하기
    public VisitingCard getCurrent() { return this.current; }
    //현재 개수 구하기
    public int getLength() { return this.length; }
}
```

# printAllVisitingCards
나중에 Main클래스에서 VisitingCardBinder의 전체 명함을 출력할 때 중복되는 코드를 줄이기 위해 정의했습니다.
```java
public class VisitingCardBinder implements Cloneable
{
    //명함철에 끼운 전체 명함 출력하기(테스트에서 중복을 줄이기 위해)
    public void printAllVisitingCards()
    {
        int index = 1;
        for (VisitingCard visitingCard: this.visitingCards)
        {
            System.out.printf("%s%d","< ", index).println("번째 명함: " + visitingCard + " >");
            index++;
        }
    }
}
```

# 테스트
```java
public class Main
{
    public static void main(String[] args) throws CloneNotSupportedException 
    {
        //VisitingCardBinder 클래스 테스트
        System.out.println("VisitingCardBinder 클래스 테스트");
        //VisitingCardBinder 디폴트 생성자로 생성하기
        VisitingCardBinder originalVcb = new VisitingCardBinder();
        //명함철에 끼울 명함생성하기
        VisitingCard one = new VisitingCard("정길동", "대리",
                "01024367967", "Jung@naver.com",
                "삼성전자", "서울시 서초구", "023692447",
                "023692448", "Samsung.com");
        VisitingCard two = new VisitingCard("홍길동", "대리",
                "01036937428", "Hong@naver.com",
                "신한은행", "서울 중구",
                "023347714", "023347715", "Shinhan.com");
        VisitingCard three = new VisitingCard("차길동", "과장",
                "01036925571", "Cha@naver.com",
                "엘지전자", "서울시 강서구",
                "022397821", "022397822", "LG.com");
        VisitingCard four = new VisitingCard("김길동", "부장",
                "01036901127", "Kim@naver.comn",
                "현대자동차", "울산 북구",
                "0524379702", "0524379701", "Hyundai.com");
        VisitingCard five = new VisitingCard("나길동", "사원",
                "01036928827", "Na@naver.com",
                "SK하이닉스", "경기도 이천",
                "-0313692248", "0313692249", "SK.com");
        VisitingCard six = new VisitingCard("장길동", "전무",
                "01044287990", "Jang@naver.com",
                "노랑풍선", "서울 중구",
                "029912970", "029912971", "Yellow.com");
        VisitingCard seven = new VisitingCard("홍길동", "이사",
                "01098712341", "Hong@gmail.com",
                "아시아나항공", "서울 종로구",
                "028711297", "028711298", "Asiana.com");
        //명함철에 명함 끼우기
        originalVcb.takeIn(one);
        originalVcb.takeIn(two);
        originalVcb.takeIn(three);
        originalVcb.takeIn(four);
        originalVcb.takeIn(five);
        originalVcb.takeIn(six);
        originalVcb.takeIn(seven);
        //1. 명함철에 끼운 명함들 출력하기
        System.out.println("1. 명함철에 끼운 명함들 출력하기");
        originalVcb.printAllVisitingCards();
        System.out.println();
        //복사하기
        VisitingCardBinder copyVcb = originalVcb.clone();
        //2. 복사본 출력하기
        System.out.println();
        System.out.println("2. 복사본 출력하기");
        copyVcb.printAllVisitingCards();
        //복사본에 끼운 명함내용 변경하기
        copyVcb.getVisitingCards().getLast().setPosition("회장");
        copyVcb.getVisitingCards().getFirst().setPosition("부장");
        //3. 복사본에 명함 내용 변경한 후에 복사본 출력하기
        System.out.println();
        System.out.println("3. 복사본에 명함 내용 변경한 후에 복사본 출력하기");
        copyVcb.printAllVisitingCards();
        System.out.println();
        //4. 복사본에 명함 내용 변경한 후에 원본 출력하기
        System.out.println("4. 복사본에 명함 내용 변경한 후에 원본 출력하기");
        originalVcb.printAllVisitingCards();
        //복사본에서 홍길동으로 명함 찾기
        ArrayList<VisitingCard> indexes = copyVcb.find("홍길동");
        //5. 복사본에서 홍길동으로 찾은 명함 출력하기
        System.out.println();
        System.out.println("5. 복사본에서 홍길동으로 찾은 명함 출력하기");
        for (int i = 0; i < indexes.size(); i++)
        {
            System.out.printf("%s%d","< ", i).println("번째 명함: " + indexes.get(i) + " >");
        }
        //복사본에서 첫번째 홍길동 명함꺼내기
        VisitingCard visitingCard = copyVcb.takeOut(indexes.get(0));
        //6. 복사본에서 꺼낸 첫번째 홍길동 명함 출력하기
        System.out.println();
        System.out.println("6. 복사본에서 꺼낸 첫번째 홍길동 명함 출력하기");
        System.out.println(visitingCard);
        //7. 복사본에서 첫번째 홍길동 명함 꺼낸 후에 복사본 출력하기
        System.out.println();
        System.out.println("7. 복사본에서 첫번째 홍길동 명함 꺼낸 후에 복사본 출력하기");
        copyVcb.printAllVisitingCards();
        //복사본 정렬하기
        copyVcb.arrange();
        //8. 정렬한 복사본 출력하기
        System.out.println();
        System.out.println("8. 정렬한 복사본 출력하기");
        copyVcb.printAllVisitingCards();
        //9. 원본에서 현재 명함 출력하기
        System.out.println();
        System.out.println("9. 원본에서 현재 명함 출력하기");
        System.out.println(originalVcb.getCurrent());
        //10. 원본에서 다음으로 이동한 후 출력하기
        System.out.println();
        originalVcb.next();
        System.out.println("10. 원본에서 다음으로 이동한 후 출력하기");
        System.out.println(originalVcb.getCurrent());
        //11. 원본에서 마지막으로 이동한 후 출력하기
        System.out.println();
        originalVcb.last();
        System.out.println("11. 원본에서 마지막으로 이동한 후 출력하기");
        System.out.println(originalVcb.getCurrent());
        //12. 원본에서 이전으로 이동한 후 출력하기
        System.out.println();
        System.out.println("12. 원본에서 이전으로 이동한 후 출력하기");
        originalVcb.previous();
        System.out.println(originalVcb.getCurrent());
        //13. 원본에서 처음으로 이동한 후 출력하기
        System.out.println();
        originalVcb.first();
        System.out.println("13. 원본에서 처음으로 이동한 후 출력하기");
        System.out.println(originalVcb.getCurrent());
        //14. 원본에서 이전으로 이동한 후 출력하기
        System.out.println();
        originalVcb.previous();
        System.out.println("14. 원본에서 이전으로 이동한 후 출력하기");
        System.out.println(originalVcb.getCurrent());
        //15. 원본에서 처음으로 이동한 후 출력하기
        System.out.println();
        System.out.println("15. 원본에서 처음으로 이동한 후 출력하기");
        originalVcb.first();
        System.out.println(originalVcb.getCurrent());
        //16. 원본에서 다음으로 이동한 후 출력하기
        System.out.println();
        System.out.println("16. 원본에서 다음으로 이동한 후 출력하기");
        originalVcb.next();
        System.out.println(originalVcb.getCurrent());
        //17. 원본에서 마지막으로 이동한 후 출력하기
        System.out.println();
        System.out.println("17. 원본에서 마지막으로 이동한 후 출력하기");
        originalVcb.last();
        System.out.println(originalVcb.getCurrent());
     
    }
}       
```

## 테스트 결과
위 코드를 실행해보면 다 정상적으로 원하는 결과가 나옴을 확인할 수 있었습니다.<br><br>
나머지 결과는 생략하고 깊은 복사가 되었는지 확인하기 위해 1. 원본 출력하기, 2. 복사본 출력하기, 3. 복사본에 명함 내용 변경한 후에 복사본 출력하기, 4. 복사본에 명함 내용 변경한 후에 원본 출력하기 결과만 확인해보도록 하겠습니다.<br><br>
명함 7개를 명함철에 끼운 후 원본 출력하기<br>
![원본출력하기](../../images/2022-01-04-fifteenth/원본출력하기.JPG)
<br><br>
복사본 출력하기<br>
![복사본출력하기](../../images/2022-01-04-fifteenth/복사본출력하기.JPG)
<br><br>
복사본에 명함 내용을 변경한 후에 복사본 출력하기<br>
![복사본내용변경후출력하기](../../images/2022-01-04-fifteenth/복사본내용변경후출력하기.JPG)
<br><br>
복사본에 명함 내용 변경한 후에 원본 출력하기<br>
![복사본내용변경후원본출력하기](../../images/2022-01-04-fifteenth/복사본내용변경후원본출력하기.JPG)
<br><br>
위 결과를 보아 깊은 복사가 제대로 이뤄졌음을 알 수 있습니다.

# 마치며
이번 시간에는 명함철 프로젝트에서 control역할을 하는 **VisitingCardBinder 클래스를 구현하면서 LinkedList의 얕은 복사와 깊은 복사에 대해서 배울 수 있었습니다.**<br><br>
다음 시간에는 자바입출력과 관련하여 **"Java프로젝트하면서 입출력에서 RandomAccessFile을 쓰는 이유와 사용 방법 설명하기"** 작성하도록 하겠습니다.<br><br>  
궁굼한 사항이나 틀린 사항은 댓글을 남겨주시거나 이메일을 보내주시면 확인하고, 답하도록 하겠습니다.<br><br>
긴 글 읽어주셔서 감사합니다^^