# OOP에서의 protected의 역할
protected는 같은 패키지 및 그 클래스를 상속(extends) 해서 구현하는 경우 접근이 가능하다.

protected는 잠재적으로 자식 클래스가 재정의해서 바꾸어야 할 경우를 고려한 modifier이다.

완성되지 못한, 완성 될 수 없는 클래스 멤버를 의미한다.

클래스를 디자인한 개발자가 이 메서드에 대해서 앞으로 더 구현할 것이 남았다거나, 혹은 디자인 컨셉으로서 일부러 완성시키지 않은 경우 둘 다를 의미할 수 있다.

## abstract와 protected의 차이점
abstract는 그것을 상속하는 클래스는 반드시 해당 메서드를 구현해주어야 하는 방식이다.

이에 반해 protected는 자식클래스가 재정의를 해야 할 필요가 없다.

protected로 선언된 메서드는 재정의해서 처리해야 할 수도 있음을 클라이언트에게 알려주는 것이다.

즉 protected는 굉장히 문서적으로, 클래스 상속을 하는 개발자에게 주의를 주는 방식이다.

## Reflection API는 OOP를 깨버릴 만큼 강력하다.
일반적으로 protected api는 외부 패키지에서 상속을 하지 않는 이상 사용될 수 없다고 알려져 있지만, 실제로는 Reflection API를 통해 사용이 가능하다.

Reflection API에 대한 설명 -> https://tecoble.techcourse.co.kr/post/2020-07-16-reflection-api/
