# 한정적 와일드카드를 사용해 API 유연성을 높이라
매개 변수화 타입은 불공변(invariant)이다. => List\<Type1\> 와 List\<Type2\>은 서로 하위, 상위 타입이 아님.<br/>
> List\<Object\>에는 어떤 객체든 넣을 수 있지만 List\<String\>에는 문자열만 넣을 수 있다.<br/>
> List\<String\>은 List\<Object\>의 일을 수행하지 못하니 하위타입이 될 수 없다.(리스코프 치환 원칙에 어긋남.)
<br/>
때로는 불공변 방식보다 유연한 무언가가 필요함.<br/>

```java
public class Stack<E>{
  public Stack();
  public void push(E e);
  public E pop();
  public boolean isEmpty();
}
```
위와 같은 Stack 클래스에 아래와 같은 메서드를 추가해보자.<br/>
```java
public void pushAll(Iterable<E> src){
  for(E e : src)
    push(e);
}
```
이 메서드는 깔끔하게 컴파일 되지만 결함이 있다.<br/>
src의 원소타입이 Stack의 E와 같으면 작동 되지만 다르면 작동하지 않는다.<br/>
> Stack\<Number\> 에 Number 의 하위 클래스인 Integer를 넣으려고 해도 <br/>
> 불공변 방식이기 때문에 오류가 난다.
> ```
> incompatible types: Iterable<Integer>
> cannot be converted to Iterable<Number>
> ```
<br/>
  
### 한정적 와일드카드 타입
  
pushAll의 매개변수 타입은 E의 Iterable이 아니라 E의 하위타입의 Iterable 이어야함.<br/>
  => Iterable\<? extends E\>가 정확하게 이런 뜻임.<br/>
```java
public void pushAll(Iterable<? extends E> src){
  for(E e : src)
    push(e);
}
```
pushAll과 반대되는 popAll을 만들어 보자.<br/>
```java
public void popAll(Collection<E> dst){
  while(!isEmpty())
    dst.add(pop());
}
```
위와 같이 작성하면 예상하듯이 컴파일은 되지만 작동할 때에 문제가 생길 수 있다.<br/>
> ```java
>  Stack<Number> numStack = new Stack<>();
>  Collection<Object> objects - ...;
>  numberStack.popAll(objects);
> ```
이런 예시가 있다면 Collection<Object>는 Collection<Number>의 하위타입이 아니라는 오류가 뜸<br/>

<br/>

이런 상황에서는 아래와 같이 E의 상위 타입의 Collection 이라고 한정지어줘야한다.<br/>
=> ? super E<br/>
```java
public void popAll(Collection<? super E> dst){
  while(!isEmpty())
    dst.add(pop());
}
```
**유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입을 사용하라.**<br/>
<br/>
### 공식? 꿀팁?
입력 매개변수가 생산자와 소비자 역할을 동시에 한다면 와일드카드 타입을 써도 좋을게 없다.<br/>
타입을 정확히 지정해야하는 상황이므로 이때는 와일드카드 타입을 쓰지 말아야함.<br/>
다음의 공식을 외우면 기억하는데에 도움이 될 듯.<br/>
> 펙스(PECS) : producer-extends, consumer-super
<br/>
이해가 안되면 위의 pushAll 과 popAll 을 다시 한번 보자.<br/>
나프탈린(Naftalin)과 와들러(Wadler)는 이를 겟풋원칙(Get and Put Principle)으로 부른다.<br/>
제대로만 사용한다면 클래스 사용자는 와일드카드 타입이 쓰였다는 사실조차 의식하지 못함.<br/>
받아들여야하는 매개변수는 받고 거절해야하는 매개변수는 거절하는 작업이 알아서 이루어짐.<br/>
<b>
클래스 사용자가 와일드카드 타일을 신경써야한다면 그 API는 무슨 문제가 있을 가능성이 큼
</b>
<br/><br/>

### 자바 7까지의 타입 추론 능력
자바 7까지는 타입 추론 능력이 부족해서 문맥에 맞는 반환타입(혹은 목표타입)을 명시해야했음.<br/>

```java
Set<Number> numbers = union(integers, doubles); // 자바 7에서는 오류가 났음
Set<Number> numbers = Union.<Number>union(integers, doubles); // 명시적타입인수를 사용
```
<br/>

### PECS 공식 2번 적용

컬렉션에서 최댓값을 반환하는 max 메서드(아이템30 에 있음)를 변경해보자.<br/>
```java
public static <E extends Comparable<E>> E max(List<E> list)
=>
public staticc <E extends Comparable<? super E>> E max)(List<? extends E> list)
```
1. 입력 매개변수에서는 E 인스턴스를 생산하므로 원래의 List<E>를 List\<? extends E\> 로 수정
2. 타입 매개변수 E는 Compable\<E\>가 E 인스턴스를 소비하기에 Comparable<? super E>로 수정<br/>

Comparable 은 언제나 소비자 이므로 Comparable\<E\>보다 Comparable\<? super E\>가 났고<br/>
일반적으로도 그렇다.<br/>
> 이렇게 복잡하게 만들 가치가 있나 => ㅇㅇ<br/>
> List\<ScheduledFuture\<?\>\> scheduledFutures = ...;<br/>
> 라는 소스가 있다면 수정 전 max는 처리를 못할 것이다.<br/>
> ScheduledFuture 이 Comparable\<ScheduledFuture\>를 구현하지 않았기 때문<br/>
> ScheduledFuture의 상위타입은 Delayed가 Comparable을 구현하고 있음.<br/>
> 그렇기에 ScheduledFuture의 인스턴스는 ScheduledFuture 뿐만 아니라 상위인 Delayed 와도 비교할 수 있음.<br/>

**일반화 해서 말하면 직접 구현하지 않고, "직접 구현한 다른다입을 확장한 타입"을 지원하기 위해 와일드카드가 필요함.**
<br/>
<br/>

### 타입 매개변수 vs 와일드카드

타입 매개변수와 와일드카드에 중에 어떤 것을 사용하던 상관없을 때가 많다.<br/>

```java
public static <E> void swap(List<E> list, int i, int j); // 타입매개변수
public static void swap(List<?> list, int i, int j); // 와일드 카드
```

public API라면 2번째가 났다.<br/>
  => 신경써야할 매개변수 타입이 없어서.<br/>
기본규칙이 매서드 선언에 타입 매개변수가 한번만 나오면 와일드 카드로 대체하라고함.<br/>
하지만 여기에는 문제가 있는데 <br/>
```java
public static void swap(List<?> list, int i, int j){
  list.set(i, list.set(j, list.set(i)));
}
```
위와 같이 구현하고 컴파일 하면 오류가 난다.<br/>
=> 와일드 카드를 사용한 List에는 null 이외에는 넣을 수 없기 때문.<br/>
```java
public static void swap(List<?> list, int i, int j){
  swapHelper(list, i, j);
}

  // 와일드카드 타입을 실제 타입으로 바꿔주는 private 도우미 메서드
private static <E> void swapHelper(List<E> list, int i, int j){
  list.set(i, list.set(j, list.get(i)));
}
```
실제 타입을 알아내려면 위와같이 도우미 메서드는 제네릭 메서드여야 함.<br/>
  다시 한번 말하지만 와일드카드를 사용하는 이유는 사용자의 입장에서 복잡하지 않기 때문임.
