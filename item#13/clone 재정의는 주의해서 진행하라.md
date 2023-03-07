# clone 재정의는 주의해서 진행하라.
* clone 규약
    * x.clone() != x 반드시 true (clone은 반드시 원본과는 다른 인스턴스여야 한다.)
    * x.clone().getClass() == x.getClass() 반드시 true
    * x.clone().equals(x) true가 아닐 수도 있다.

> ### 마커 인터페이스
> 일반적인 인터페이스와 동일하지만 사실상 아무 메서드도 선언하지 않은 인터페이스를 말한다.

## Clonealble 인터페이스
복제해도 되는 클래스임을 명시하는 믹스인 인터페이스
> ### 믹스인 인터페이스
> 프로그래머가 일부 코드를 클래스에 주입할 수 있도록 하는 언어 개념.
>
> 믹스인 프로그래밍은 기능 단위가 클래스에서 생성된 다음 다른 클래스와 혼합되는 소프트웨어 개발 스타일.
>
> A라는 클래스가 있다면 Cloneable과 같은 mixin interface를 구현하게 하면
>
> A클래스가 가진 본연의 기능에 다른 기능을 덧붙이기 때문에 (mix-in) 믹스인이라고 한다.
>
> 자바 8 이후로 인터페이스에서 메서드 구현이 가능하므로 믹스인 인터페이스가 존재할 수 있다.
>
> 그럼에도 Cloneable은 정상적인 믹스인 인터페이스라고 할 수 없는데
>
> clone() 메서드를 직접 제공하는게 아니라 Object가 가진 clone()의 동작 방식을 결정하기 때문이다.
>
> 자바 디자인 실패 사례 중 하나로써 따라하지 말아야 할 방식 중 하나라고 한다

Cloneable 인터페이스는 마커 인터페이스라서 Cloneable을 구현했다고 clone()을 호출할 수 있는 것이 아니다.

clone() 메서드는 따로 재정의 해줘야 한다. 또 clone()은 접근지시자가 protected라서 같은 패키지에서만 접근 가능하다.

clone()으로 만들어진 인스턴스는 생성자를 통해서 만들어지지 않는다. (아마 메모리 복사)


### Cloneable을 쓰는 이유
* clone()은 모든 클래스에서 재정의 가능하지만, Cloneable 인터페이스를 구현하지 않은 클래스의 clone()은 예외를 던진다.
* Cloneable은 상위 클래스에 정의된 clone()의 동작방식을 변경한다는 의미.

## 객체의 필드가 전부 불변이면

```java
@Override
public PhoneNumber clone() {
    try{
        return (PhoneNumber) super.clone(); // 재정의 할 때 타입캐스팅을 해주는게 좋다. -> 공변 변환 타이핑
    } catch (CloneNotSupportedException e) {
        throw new AssertionError(); 
    }
}

@Override
protected Object clone() throws CloneNotSupportedException {
    return super.clone();
}
```
만약 ```super.clone()```을 쓰지 않고 새로운 생성자로 clone을 재정의한다면 규약이 깨진다.

왜? 하위 타입은 상위 타입으로 변환해서 쓸 수 있지만 상위 타입은 하위 타입으로 변환할 수 없다.

항상 반드시 ```super.clon()```을 호출해야한다.

필드가 전부 불변이면 여기까지만 지켜줘도 충분하다.

## 가변객체
객체의 필드에 배열이 존재하고 clone을 하게 되면 원본과 복사본은 둘 다 동일한 배열을 바라본다.

가변 상태를 참조하는 클래스용 clone 메서드를 재정의 해야한다.

```java
@Override
public Stack clone() {
    try {
        Stack result = (Stack) super.clone();
        result.elements = elements.clone();
        return result;
    } catch(CloneNotSupportedException e) {
        throw new AssertionError();
    }
}

// stack -> elementsS[0, 1]
// copy -> elementsC[0, 1]
// elementsS[0] == elementsC[0]  true
```
위처럼 하는건 배열은 다르지만 인스턴스는 같다 (얕은 복사 Shallow Copy)

```java
public class HashTable implements Cloneable{
    private Entry[] buckets = new Entry[10];

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        Entry(Object key, Object value, Entry next){
            this.key = key;
            this.value = value;
            this.next = next;
        }


        Entry deepCopy(){
            // 해당 엔트리가 가리키는 연결 리스트를 재귀적으로 복사
            // 재귀 호출이기 때문에, 리스트의 원소 수 만큼 스택 프레임을 소비해 리스트가 길면 스택 오버플로우 발생 위험 있음.
            // return new Entry(key, value, next == null ? null : next.deepCopy());

            // 스택오버플로우 문제를 피하기 위해 반복자를 사용
            Entry result = new Entry(key,value, next);
            for(Entry p = result; p.next != null; p = p.next)
                p.next = new Entry(p.next.key, p.next.value, p.next.next);
            return result;
        }
    }

    /**
     * 잘못된 Clone
     * 원본과 같은 연결 리스트를 참조 해 원본과 복제본 모두 예기치 않게 동작할 가능성 생김
     * @return
     * @Override
     *     public  HashTable clone(){
     *         try{
     *             HashTable result = (HashTable) super.clone();
     *             result.buckets = buckets.clone();
     *             return result;
     *         }catch (CloneNotSupportedException e){
     *             throw new AssertionError();
     *         }
     *     }
     */


    /**
     *
     * @return
     */
    @Override
    public  HashTable clone(){
        try{
            HashTable result = (HashTable) super.clone();
            result.buckets = new Entry[buckets.length];
            for(int i =0 ; i < buckets.length; i++){
                if(buckets[i] != null){
                    result.buckets[i] = buckets[i].deepCopy();
                }
            }
            return result;
        }catch (CloneNotSupportedException e){
            throw new AssertionError();
        }
    }
}
```
Deepcopy를 해야 달라진다.

Cloneable 아키텍처는 가변 객체를 참조하는 필드는 final로 선언하라는 일반 용법과 충돌한다. 

원본과 복제된 객체가 가변 객체를 공유해도 된다면 상관없지만, 복제할 수 있는 클래스를 만들기 위해 일부 필드에서 final 한정자를 제거해야할 수도 있다.

clone() 메서드는 재정의될 수 있는 메서드를 호출해서는 안된다. 

만약 clone()이 하위 클래스에서 재정의한 메서드를 호출하면, 하위 클래스는 복제 과정에서 자신의 상태를 교정할 수 있는 기회를 일게되어 원본과 상태가 달라질 가능성이 커지게된다.

Cloneable을 구현하는 모든 클래스는 clone()을 재정의해야 한다. 

이때 접근 제한자는 public으로, 반환 타입은 클래스 자신으로 변경한다. 

가장 먼저 super.clone 을 호출한 후 필요한 필드를 전부 적절히 수정해줘야 한다. 

일반적으로 '깊은 구조'에 숨어 있는 모든 가변 객체를 복사하고, 복제본이 가진 객체 참조 모두가 복사된 객체들을 가리키게 해야한다. 

기본 타입 필드와 불변 객체 참조만을 갖는 클래스라면 아무 필드도 수정할 필요는 없다.

## 복사 생성자와 복사 팩터리
Cloneable을 구현하지 않은 클래스라면 복사 생성자와 복사 팩터리라는 더 나은 객체 복사 방식을 제공할 수 있다.

배열만이 clone() 을 재대로 사용하는 유일한 예이다. 복사하는 대상이 배열이 아니면 복사 생성자, 복사 팩터리가 좋다.

```java
// 복사 생성자
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack(Stack s) {
        this.elements = s.elements.clone();
        this.size = s.size;
    }
}
```

```java
// 복사 팩터리
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public static Stack newInstance(Stack s) {
        return new Stack(s.elements, s.size);
    }
}
```

복사 생성자란 단순히 자신과 같은 클래스의 인스턴스를 인수로 받는 생성자를 말한다.

복사 생성자/팩터리는
* 생성자를 쓰지 않는 방식의 객체 생성 매커니즘을 사용하지 않는다.
* 엉성하게 문서화된 규약에 기대지 않고, 정상적인 final 필드 용법과도 충돌하지 않는다.
* 불필요한 검사 예외를 던지지 않고, 형변환도 필요치 않는다.
* 해당 클래스가 구현한 인터페이스 타입의 인스턴스를 인수로 받을 수 있다.

## 비검사 예외 (UncheckedException)
런타임예외를 상속받은 예외를 비검사 예외라고 하고, Error를 상속받은 클래스들도 비검사 예외라고 부를 수 있다.

* 비검사 예외를 선호하는 이유는 컴파일 에러를 신경쓰지 않아도 되며, try-catch로 감싸거나 메서드 선언부에 선언하지 않아도 된다.

### 검사 예외 존재 이유
클라이언트가 해당 예외 상황을 복구할 수 있다면 검사 예외를 사용하고, 해당 예외가 발생했을 때 아무것도 할 수 없다면, 비검사 예외로 만든다.

## TreeSet
AbstractSet을 확장한 **정렬된** 컬렉션

* 원소가 지닌 **자연적인 순서**에 따라 정렬한다.
* 오름차순으로 정렬한다.
* 스레드 안전하지 않다.

핸드폰 번호와 같이 정렬하기 어려운 것들은 사용자가 직접 기준을 정해줘야한다.

## HashSet -> 레드 블랙 트리
https://blogshine.tistory.com/102

