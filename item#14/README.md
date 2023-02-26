1. [규약의 예시](#규약의-예시)
2. [하위 클래스 compare](#하위-클래스-compare)
3. [컴파일 타임? 런타임?](#컴파일-타임?-런타임?)
4. [자바의 타입 추론능력](#자바의-타입-추론능력)
5. [정수 오버플로잉](#정수-오버플로잉)
6. [부동소수점](#부동소수점)


# 규약의 예시
```java
public class CompareToConvention {

    public static void main(String[] args) {
        BigDecimal n1 = BigDecimal.valueOf(23134134);
        BigDecimal n2 = BigDecimal.valueOf(11231230);
        BigDecimal n3 = BigDecimal.valueOf(53534552);
        BigDecimal n4 = BigDecimal.valueOf(11231230);

        // p88, 반사성
        System.out.println(n1.compareTo(n1));

        // p88, 대칭성
        System.out.println(n1.compareTo(n2));
        System.out.println(n2.compareTo(n1));

        // p89, 추이성
        System.out.println(n3.compareTo(n1) > 0);
        System.out.println(n1.compareTo(n2) > 0);
        System.out.println(n3.compareTo(n2) > 0);

        // p89, 일관성
        System.out.println(n4.compareTo(n2));
        System.out.println(n2.compareTo(n1));
        System.out.println(n4.compareTo(n1));

        // p89, compareTo가 0이라면 equals는 true여야 한다. (아닐 수도 있고..)
        BigDecimal oneZero = new BigDecimal("1.0");
        BigDecimal oneZeroZero = new BigDecimal("1.00");
        System.out.println(oneZero.compareTo(oneZeroZero)); // Tree, TreeMap
        System.out.println(oneZero.equals(oneZeroZero)); // 순서가 없는 콜렉션
    }
}
```
![image](https://user-images.githubusercontent.com/92290312/221402635-d33d6ce1-8e09-4ff7-a5dd-3d0b7394b873.png)<br/>
![image](https://user-images.githubusercontent.com/92290312/221402779-08d5ac8a-b861-4643-b202-a52ef71f5324.png)<br/>
<br/><br/>

# 하위 클래스 compare
하위 클래스에서 필드 값이 추가되면 규약이 깨지면서 compare을 재정의 할 수가 없음.<br/>
Comparable 을 다시 지정받을 수도 없고 오버라이딩은 할 수 있어도 사용되지 않음.<br/>
그래도 아래처럼은 할 수 있음.<br/>
![image](https://user-images.githubusercontent.com/92290312/221403315-d3417e1c-fce8-439d-92a7-0c4efdc00924.png)
![image](https://user-images.githubusercontent.com/92290312/221403335-d808d8de-e573-474a-8e46-8833bf15527c.png)<br/>
위는 추천하지 않는 방법임.<br/>
이럴 바엔 따로 composition을 나누는게 나음.<br/>
![image](https://user-images.githubusercontent.com/92290312/221403494-56346aeb-a8a0-4936-a262-33759ff88c46.png)<br/>
<br/><br/>
# 컴파일 타임? 런타임?
컴파일 타임 : 처음 코드가 컴파일 될때 <br/>
런타임 : 코드가 실행중 일때 <br/>
웬만하면 컴파일 타임에 발생하는 오류가 차라리 났다. => 바로 오류를 찾고 해결할 수 있기 때문.
<br/><br/>
# 자바의 타입 추론능력
![image](https://user-images.githubusercontent.com/92290312/221404101-6b7ed877-968c-4c2d-b3ea-7fb383651a2b.png)<br/>
앞에 있는 comparingInt로 인해 타입 정해진 순간 타입은 인지할 수 있음.<br/>
제네릭이나 람다에서 타입추론이 잘 사용됨.<br/>
<br/><br/>
# 정수 오버플로잉
![image](https://user-images.githubusercontent.com/92290312/221404277-d1204eb9-c3e9-4b5a-9522-1575c179f62f.png)<br/>
<br/><br/>
# 부동소수점
![image](https://user-images.githubusercontent.com/92290312/221404357-0377e4fa-4e15-41e1-9ead-6ccd3b2732a6.png)<br/>
가수부의 메모리 할당량을 넘어가는 부분은 생략되기 때문에 정확하지 않음<br/>
BigDecimal 을 사용하여 계산하면 더 정확히 계산 가능
