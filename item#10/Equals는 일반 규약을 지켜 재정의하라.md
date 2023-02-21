# eqauls는 일반 규약을 지켜 재정의하라.
**핵심은 equals를 재정의 하지 않는 것이 최선이다.**

Object의 메소드 중 overridng 가능한 것
equals, hashcode, toString, clone, finalize

언제 equals를 재정의 해야하나?

### equals 재정의가 필요없는 경우
* 각 인스턴스가 본질적으로 고유하다. (싱글턴 패턴이면 그 오브젝트는 그 자체로 고유할 수 밖에 없다. 굳이 equals가 필요한가??) (Enum도 근본적으로 단 하나다. 그러므로 Enum에도 equals재정의 필요 X)
* 인스턴스의 '논리적 동치성'을 검사할 필요가 없다. ex) String
* 상위 클래스에서 재정의한 equlas가 하위 클래스에도 적절하다.
* 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.

### equals 규약
* 반사성 : null이 아닌 모든 참조 값 x에 대해 x.equals(x)를 만족해야한다. ```A.equals(A) == true;```
* 대칭성 : null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)가 true이면, y.equals(x)가 true를 만족해야 한다. ```A.equals(B) == B.equals(A);```
* 일관성
* 추이성 : null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true이고, y.equals(z)가 true이면 x.equals(z)도 true가 되야 한다는 조건이다.
