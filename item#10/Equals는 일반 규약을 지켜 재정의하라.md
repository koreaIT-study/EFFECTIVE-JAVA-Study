# eqauls는 일반 규약을 지켜 재정의하라.
**핵심은 equals를 재정의 하지 않는 것이 최선이다.**

Object의 메소드 중 overridng 가능한 것
equals, hashcode, toString, clone, finalize

언제 equals를 재정의 해야하나?

## equals 재정의가 필요없는 경우
* 각 인스턴스가 본질적으로 고유하다. (싱글턴 패턴이면 그 오브젝트는 그 자체로 고유할 수 밖에 없다. 굳이 equals가 필요한가??) (Enum도 근본적으로 단 하나다. 그러므로 Enum에도 equals재정의 필요 X)
* 인스턴스의 '논리적 동치성'을 검사할 필요가 없다. ex) String
* 상위 클래스에서 재정의한 equlas가 하위 클래스에도 적절하다.
* 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.
 
## equals 재정의 해야 하는 경우
equlas를 재정의 해야 하는 경우는 객체 동일성(식별성, Object Identity)를 확인해야 하는 경우가 아니라

논리적 동치성(동등성, Logical Equality)를 비교하도록 재정의 되지 않았을 경우이다.

동일성(==) 비교는 객체가 참조하고 있는 주소 값을 비교한다.

그래서 속성과 타입이 같아도 참조 값이 다르면 다른 객체로 구분한다.

이를 해결할 수 있는 건, 동등성 비교(equals)이다.

```java
// Object 클래스 
// 재정의 되지 않은 Object의 equals도 동일성(==) 비교를 한다.
public boolean equals(Object obj) {
    return (this == obj);
}

public class Point {
	int x;
	int y;
	
	public Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
}

```
![image](https://user-images.githubusercontent.com/82895809/220510324-b91e9d47-8ceb-43b5-aeb6-f7fe2e7ed6e9.png)

근데 equals를 재정의하면 속성과 타입이 같을때 같은 객체로 비교할 수 있도록 할 수 있다.

String은 equals가 재정의되어있다.

```
String a = "b";
String b = "a";

System.out.println(a.qeuals(b)); // TRUE
```
String도 똑같이 객체인데 이런 결과를 가져다 주는 것은 equals가 재정의 되어있기 때문이다.

```java
// String 내부에서 재정의된 equals
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

![image](https://user-images.githubusercontent.com/82895809/220511354-84a3403e-9419-4f2b-9a74-c15b8ecae72b.png)


> ## String은 동일성(==) 비교를 해도 True다.
> JVM에서는 String을 조금 특별히 관리하기 때문에, 객체이지만 new연산자가 아니라 리터럴("")을 이용해서 String 을 생성 할 수 있다. 
> 
> 이때 JVM은 객체의 영역인 heap 영역이 아니라, constant pool 영역으로 찾아간다. 
> 
> 그리고 constant pool 영역에 이전에 같은 값을 가지고 있는 String 객체가 있다면, 그 객체의 주소값을 반환하여 참조하도록 한다.

## equals 규약
### 반사성
**null이 아닌 모든 참조 값 x에 대해 x.equals(x)를 만족해야한다.**
* ```A.equals(A) == true```
### 대칭성
**null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)가 true이면, y.equals(x)가 true를 만족해야 한다.**
* ```A.equals(B) == B.equals(A)```
```java 
// 대칭성이 깨지는 코드
public final class CaseInsensitiveString {
  private final String s;

  public CaseInsensitiveString(String s) {
    this.s = Objects.requireNonNull(s);
  }

  @Override
  public boolean equals(Object o) {
    if(o instanceof CaseInsensitiveString) {
      return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
    }

    if(o instanceof String) { //한 방향으로만 작동!!
      return s.equalsIgnoreCase((String) o);
    }
    return false;
  }
}

/////////
CaseInsensitiveString caseInsensitiveString = new CaseInsensitiveString("Test");
String test = "test";
System.out.println(caseInsensitiveString.equals(test)); //true
System.out.println(test.equals(caseInsensitiveString)); //false
// String 클래스에서는 CaseInsensitiveString의 존재를 모르기 때문에 False
```
### 추이성
**null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true이고, y.equals(z)가 true이면 x.equals(z)도 true가 되야 한다는 조건이다.**
* ```A.equals(B) && B.equals(C), A.equals(C)```
```java
ColorPoint a = new ColorPoint(1, 2, Color.RED);
Point b = new Point(1, 2);
ColorPoint c = new ColorPoint(1, 2, Color.BLUE);

// 인스턴스 a, b, c가 있을 때 a.equals(b)와 a.equals(c)일 때, a.equals(c)가 되는 과정
```

1. 대칭성 위반
```java
class Point {
  private final int x;
  private final int y;

  public Point(int x, int y) {
    this.x = x;
    this.y = y;
  }

  @Override
  public boolean equals(Object o) {
    if(!(o instanceof Point)) return false;
    Point p = (Point) o;
    return this.x == p.x && this.y == p.y;
  }
}
/////////////////////////////
class ColorPoint extends Point {
  
  private final Color color;

  @Override
  public boolean equals(Object o) {
    if(!(o instanceof ColorPoint)) return false;
    
    return super.equals(o) && this.color == ((ColorPoint) o).color;
  }
}
```
ColorPoint 클래스의 equals 메서드가 위처럼 재정의 했다면
```java
ColorPoint a = new ColorPoint(1, 2, Color.RED);
Point b = new Point(1, 2);

System.out.println(a.equals(b)); //false
System.out.println(b.equals(a)); //true
```

2. 추이성 위반
```java
class ColorPoint extends Point {
  
  private final Color color;

  @Override
  public boolean equals(Object o) {
    if(!(o instanceof Point)) return false;

    //o가 일반 Point이면 색상을 무시햐고 x,y정보만 비교한다. 하지만 이것은 아주 위험한 코드다.
    if(!(o instanceof ColorPoint)) return o.equals(this);
    
    //o가 ColorPoint이면 색상까지 비교한다.
    return super.equals(o) && this.color == ((ColorPoint) o).color;
  }
}
```
위처럼 ColorPoint 클래스의 equals를 재정의 했다면

```java
ColorPoint a = new ColorPoint(1, 2, Color.RED);
Point b = new Point(1, 2);
ColorPoint c = new ColorPoint(1, 2, Color.BLUE);

System.out.println(a.equals(b)); //true
System.out.println(b.equals(c)); //true
System.out.println(a.equals(c)); //false
```

3. 리스코프 치환 원칙 위반
```java
class CounterPointTest {
  private static final Set<Point> unitCircle = Set.of(new Point(0, -1),
   new Point(0, 1),
   new Point(-1, 0),
   new Point(1, 0)
  );

  public static boolean onUnitCircle(Point p) {
    return unitCircle.contains(p);
  }

// Point에서 재정의된 equals
  @Override
  public boolean equals(Object o) {
    if(o == null || o.getClass() != this.getClass()) {
      return false;
    }

    Point p = (Point) o;
    return this.x == p.x && this.y = p.y;
  }
}

//////////////
CounterPoint cp = new CounterPoint(1, 0);
System.out.println(Point.onUnitCircle(cp)); //false
```
상위 클래스 타입으로 동작하는 어떤 코드가 있으면 하위클래스 타입의 인스턴스를 주더라도 그대로 동작해야한다.
하지만 위의 코드는 CounterPoint를 넘겼을 때 True가 나오지 않는다.

Point에서 재정의된 equals를 봤을 때 getClass로 비교하기 때문에 False를 나타낸다.

```java
@Override
public boolean equals(Object o) {
  if(o == null || !(o instanceof Point)) {
    return false;
  }

  Point p = (Point) o;
  return this.x == p.x && this.y = p.y;
}
/////////////
CounterPoint cp = new CounterPoint(1, 0);
System.out.println(Point.onUnitCircle(cp)); //true
```
만약 위처럼 equals가 재정의 되었다면 True가 나타났을 것이다.

하지만 ColorPoint처럼 필드를 추가했을 때 추이성, 대칭성을 위반하지 않는(equals 규약을 지키는) equals를 재정의 할 수 있는 방법이 없다.

이미 Java안에서도 equals규약이 지켜지지 않은 코드가 존재하는데

```java
// Timestamp는 Date를 상속받아서 만든 클래스다.
long time = System.currentTimeMillis();
Timestamp timestamp = new Timestamp(time);
Date date = new Date(time);

//대칭성 위배
System.out.println(data.equals(timestamp)); // true
System.out.println(timestamp.equals(data)); // false
```

그래서 Composition을 사용하는게 좋다. (상속을 하지 않고 새로운 클래스를 정의)

```java
public ColorPoint {
  private Point point;
  private Color color; // Enum

  public ColorPoint(int x, int y, Color color) {
    point = new Point(x, y);
    this.color = Objects.requireNonNull(color);
  }

  public Point asPoint() {
    return point;
  }

  @Override
  public boolean equals(Object o) {
    if(!(o instanceof ColorPoint)) {
      return false;
    }
    ColorPoint cp = (ColorPoint) o;
    return cp.point.equals(point) && cp.color.equals(color);
  }
}
///////////////////
Point p1 = new Point(1, 0);
Point p2 = new me.whiteship.chapter02.item10.composition.ColorPoint(1, 0, Color.RED).asPoint();

System.out.println(onUnitCircle(p1)); // true
System.out.println(onUnitCircle(p2)); // ture
```
> Enum에 있는 equals는 객체의 동일성만 비교한다.
위처럼 코드를 구성하는 것이 안전하다.


### 일관성
**null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.**
* ```A.equals(B) == A.equals(B)```
객체 안에 들어있는 값이 바뀌는 경우엔 일관성이 깨질 수 있다.

하지만 불변 객체면 일관성은 항상 보장된다.

같은 도메인이라도 그때그때 나오는 IP정보는 다를 수 있다.
### null-아님  
* ```A.equals(null) == false```
객체.equals에 null을 넘겼을 때 false가 나오면 됨.

## equals 구현 방법
* ```==``` 연산자를 사용해 자기 자신의 참조인지 확인.
* ```instanceof``` 연산자로 올바른 타입인지 확인.
* 입력된 값을 올바른 타입으로 형변환.
* 입력 객체와 자기 자신의 대응 되는 핵심 필드가 일치하는지 확인. // 중요!
* 구글의 AutoValue또는 Lombok을 사용
* IDE의 코드 생성 기능 사용.
* JDK17이상 -> Record 사용
  * Record는 Java가 알아서 Equals, Hashcode, toString을 재정의해줌.

* float, double을 제외한 기본타입은 ```==```을 통해 비교.
* float, double은 Float.compare(float, float)와 Double.compare(double, double)로 비교.
* 배열의 모든 원소가 핵심 필드라면 Arrays.equals를 사용.
* null이 의심되는 필드는 Objects.equals(obj, obj)를 이용해 NullPointerException을 예방.
* 성능을 올리고자 한다면
  * 다를 확률이 높은 필드부터 비교한다.
  * 비교하는 비용(시간복잡도)이 적은 비교를 먼저 수행

> ### Record
> 불변 객체를 쉽게 생성할 수 있도록 하는 새로운 유형의 클래스
> 
> 기존엔 모든 필드에 final을 사용하여 명시적으로 정의하고, 생성자, 필드에 대한 접근자 메서드(getter), 
> 상속 방지를 위해클래스 자체를 final로 선언하기도하고, equals, toString, hashCode등 재정의를 하였다.
> 
> 레코드 클래스를 사용하면 훨씬 간결한 방식으로 동일한 불변 데이터 객체 정의할 수 있다.
> 
> 컴파일러는 헤더를 통해 내부 필드를 추론 ```Point(int x, int y)```
> 
> 생성자를 작성하지 않아도 되고 toString, equals, hashCode 메서드에 대한 구현을 자동으로 제공
> 
> 레코드는 암묵적으로 final 클래스(상속불가)이고, abstract 선언 불가
> 다른 클래스를 상속 받을 수 없음, 인터페이스 구현

# Value 기반의 클래스(Value Object)
**클래스처럼 생겼지만 int처럼 동작하는 클래스**
```java.util.Optional```및 ```java.time.LocalDateTime```같은 값 기반의 클래스들
https://docs.oracle.com/javase/8/docs/api/java/lang/doc-files/ValueBased.html

위 코드의 Point처럼 필드에 final을 걸어준다.

Record로 생성한다.

# StackOverflowError
## Stack
한 쓰레드마다 쓸 수 있는 메모리 공간.
* 메서드 호출 시, 스택에 스택 프레임이 쌓인다.
  * 스택 프레임에 들어있는 정보 : 메서드에 전달하는 매개변수, 메서드 실행 끝내고 돌아갈 곳, 힙에 들어있는 객체에 대한 레퍼런스...
  * 더이상 스택 프레임을 쌓을 수 없다면 StackOverFlowError 발생.
* 스택의 사이즈를 조정하고 싶다면? -Xss1M

재귀호출을 이용한 알고리즘은 스택 프레임이 많이 쓰인다. <무한루프 ex)ColorPoint.equlas(SmellPoint)>

<img width="688" alt="스크린샷 2023-02-21 오후 10 12 42" src="https://user-images.githubusercontent.com/82895809/220354189-975c06c7-87f1-45a1-a6cf-c39e1edda54f.png">

## Heap
객체들이 있는 공간.

가비지 컬렉터가 일을 해서 정리해주는 공간.

인스턴스들을 가르키는 레퍼런스는 스택공간에 존재한다.

# 리스코프 치환 원칙
'하위 클래스의 객체'가 '상위 클래스 객체'를 대체하더라도 소프트웨어의 기능을 꺠트리지 않아야 한다.
(semantic over syntacic, 구문 보다는 의미)
