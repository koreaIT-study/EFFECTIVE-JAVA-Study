# public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라.

```java
// 이처럼 퇴보한 클래스는 public이어서는 안된다.
public class Point {
    public double x;
    public double y;
}

private static void doSomething(Point point) {
    Point localPoint = new Point();
    localPoint.x = point.x;
    localPoint.y = point.y;
}

```

퍼블릭 필드를 사용하면 ```Point.x = 10;``` 이런식으로 필드에 접근이 가능하다. -> 좋지 못한 방법.

필드가 가변적이라면 복사를 해서 써야한다.

위처럼 사용하면 캡슐화의 장점을 제공하지 못한다.

접근자와 변경자 메서드를 활용해 데이터를 캡슐화한다.

필드에 접근할 때 부수 작업을 가능하게 해준다.

```java
public double getX() {
    // 부가 작업
    return x;
}

public void setX(double x) {
    // 부가 작업
    this.x = x;
}
```

package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출해도 상관없다.

사이드 이펙트가 제한적임.

```java
// private 중첩 클래스
public class TopPoint {

    private static class Point {
        public double x;
        public double y;
    }

    public Point getPoint() {
        Point point = new Point();  // TopPoint 외부에선
        point.x = 3.5;              // Point 클래스 내부 조작이
        point.y = 4.5;              // 불가능 하다.
        return point;
    }

}
```

영상에선 메서드를 통해서 접근하는게 조금 더 안전하다고 말함.

클래스가 표현하려는 추상 개념만 올바르게 표현해주면 된다.

## 불변 필드를 노출한 public 클래스
```java
public final class Time {
    private static final int HOURS_PER_DAY    = 24;
    private static final int MINUTES_PER_HOUR = 60;

    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        if (hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("Hour: " + hour);
        if (minute < 0 || minute >= MINUTES_PER_HOUR)
            throw new IllegalArgumentException("Min: " + minute);
        this.hour = hour;
        this.minute = minute;
    }

    // 나머지 코드 생략
}
```
직접 노출할 때의 단점이 조금은 줄어들지만, 여전히 결코 좋은 생각이 아니다.

여전히 API를 변경하지 않고는 표현 방식을 바꿀 수 없다.

여전히 필드를 읽을 때 부수 작업을 수행할 수 없다.

단, 불변식은 보장할 수 있게 된다.

## 타산지석
```java
public class DimensionExample {

    public static void main(String[] args) {
        Button button = new Button("hello button");
        button.setBounds(0, 0, 20, 10);

        Dimension size = button.getSize(); // 호출할 때 마다 인스턴스 생성
        System.out.println(size.height); // 10
        System.out.println(size.width); // 20
    }

}
```
<img width="513" alt="스크린샷 2023-03-16 오후 8 52 44" src="https://user-images.githubusercontent.com/82895809/225608786-5639ef4a-ee6b-44b4-8646-56a04fb6ce4a.png">

> 질문 : 1:50초쯤 나중에 같이 보면서 알려주셈...

이렇게 쓰면 안됨.

내부를 노출한 Dimension 클래스다.

내부 필드를 public하게 노출시킴.

Dimension을 사용하려면 복사를해서 사용해야 하는데 수백만개의 인스턴스를 생성한다고 생각하면 성능 문제가 생긴다.

## 결론
**public 클래스는 절대 가변 필드를 직접 노출해서는 안된다.**

**불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다.**

**하지만 package-private 클래스나 private 중첩 클래스에서는 종종(불변이든 가변이든) 필드를 노출하는 편이 나을 때도 있다.**
