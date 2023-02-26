# Comparable을 구현할지 고려하라
### compareTo 와 equals으이 차이
둘은 2가지의 차이가 있다.<br/>
1. compareTo는 동치성 비교에 더해 순서까지 비교할 수 있음
  Compareable을 구혀했다는 건 그 클래스의 객체에는 자연적인 순서가 있음을 의미함(natural order)
    ```java
      // String이 Compareable 을 구현했기 때문에 중복 제거도 되고 알파벳 순으로 출력함.
      publicc class WordList{
        public static void main(String[] args){
          Set<String> s = new TreeSet<>();
          Collections.addAll(s, args);
          System.out.println(s);
        }
      }
    ```

2. 제네릭함.
<br/><br/>
### 순서가 명확한 값 클래스를 작성한다면 반드시 Comparable 을 구현하자.
<br/>

> compareTo 메서드의 일반 규약<br/>
> 이 객체와 주어진 객체의 순서를 비교.<br/>
> 주어진 객체보다 작으면 음의 정수, 같으면 0, 크면 양의정수를 반환.<br/>
> 비교할 수 없는 타입의 객체가 주어지면 ClassCastException 을 던짐.<br/>
>
> sgn 함수는 부호를 나타내는 함수.<br/>
>
> * Compareable을 구현한 클래스는 모든 x,y에 대해 sgn(x.compareTo(y)) == -sgn(y.compareTo(x)) 여야함.<br/>
>   따라서 둘중 하나가 예외를 던진다면 나머지도 예외를 던져야함<br/>
>   
> * Comparable을 구현한 클래스는 추이성을 보장해야함.<br/>
>   x.compareTo(y) > 0 이고 y.compareTo(z) > 0 면 x.compareTo(z) > 0 여야함.<br/>
>   
> * Comparable 을 구현한 클래스 z 에 대해 x.compareTo(y) == 0 이면 sgn(x.compareTo(z)) == sgn(y.compareTo(z)) 여야함.<br/>
> 
> 아래 권고는 필수는 아니지만 지키는게 좋음<br/>
> * (x.compareTo(y) == 0) == (x.equals(y)) 여야함.<br/>
>   Comparable을 구현하고 위 권고를 지키지 않은 모든 클래스는 그 사실을 명시해야함<br/>
>   예) "이 클래스의 순서는 equals 와 일관되지 않음"<br/>

하지만 기존 클래스를 확장한 구체 클래스에서 새로운 값 컴포넌트를 추가 했다면 compareTo 규약을 지킬 방법이 없다.<br/>

마지막 권고는 지키지 않아도 작동은 하지만 정렬된 컬렉션에 넣으면 해당 컬렉션이 구현한 인터페이스에 정의된 동작과 엇박자를 낸다.<br/>
정렬된 컬렉션들이 동치성을 비교할 때는 equals 대신 compareTo를 사용하기 때문.<br/>
<br/><br/>
### 비교자 사용
compareTo 는 필드의 순서를 비교 한다.<br/>
그럼 객체 참조 필드를 비교하려면? => compareTo 메서드를 재귀적으로 호출해야함.<br/>
이때 Comparable 을 구현하지 않은 필드나 표준이 아닌 순서로 비교해야 하면 비교자(comparator)를 대신 사용함<br/>
```java
public final class CaseInsensitiveString implements Comparable<CaseInsensitiveString>{
  public int compareTo(CaseInsensitiveString cis){
    return String.CASE_INSENSITIVE_ORDER.compare(s, cis.s);
  }
}
```
<br/><br/>
### 박싱 클래스의 정적메서드 compare 사용
자바 7부터 compareTo 메서더에서 관계 연산자 < 와 > 를 사용하는 이전 방식은 거추장스럽고 오류를 유발하니 이제는 추천하지 않음.<br/>
대신 박싱된 기본 타입 클래스들에 compare 라는 정적 메서드가 추가됨.<br/>
<br/><br/>
### 핵심 필드가 여러개라면?
가장 핵심적인 필드부터 비교해나가자.<br/>
비교해 나가면서 순서가 결정되면(결과가 0이 아니라면) 거기서 끝임. => 결과를 반환<br/>
```java
  public int compareTo(PhoneNumber pn){
    int result = Short.compare(areaCode, pn.areaCode);
    if(result == 0){
      result = Short.compare(prefix, pn.prefix);
      if(result == 0){
        result = Short.compare(lineNum, pn.lineNum);
      }
    }
    return result;
  }
```
<br/><br/>
### 비교자 생성 메소드 활용
자바 8부터는 Comparator 인터페이스가 비교자 생성 메서드(comparator construction method) 와 메서드 연쇄방식으로 비교자를 생성할 수 있게됨.<br/>
코드가 간결해지지만 성능 저하가 뒤따름.<br/>
```java
private static final Comparator<PhoneNumber> COMPARATOR =
          comparingInt((PhonNumber pn) -> pn.areaCode)
          .thenComparingInt(pn -> pn.prefix)
          .thenComparingInt(pn -> lineNum);

public int compareTo(PhoneNumber pn){
  return COMPARTOR.compare(this, pn);
}
```
<br/><br/>
### 값의 차를 기준으로 정렬할 때
값의 차를 기준으로 정렬하는 메서드를 마주할 경우가 많다.
```java
static Comparator<Object> hashCoderOrder = new Comparator<>(){
  public int compare(Object o1, Object o2){
    return o1.hashCode() - o2.hashCode();
  }
}
```
위 방식은 정수 오버플로를 일으키거나 IEEE 754 부동소수점 계산방식 에 따를 오류를 낼 수 있다.<br/>
(IEEE 754 부동소수점 계산방식 : https://codetorial.net/articles/floating_point.html)<br/>
대신 아래의 두가지 방법 중 하나를 사용하자.<br/>
```java
static Comparator<Object> hashCodeOrder = new Comparator<>(){
  public int compare(Object o1, Object o2){
    return Integer.compare(o1.hashcode(), o2.hashCode());
  }
}
```
```java
static Comparator<Object> hashCodeOrder = Comparator.comparingInt(o -> o.hashCode());
```
