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
* 일관성 : null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다. ```A.equals(B) == A.equals(B)
* 추이성 : null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true이고, y.equals(z)가 true이면 x.equals(z)도 true가 되야 한다는 조건이다. ```A.equals(B) && B.equals(C), A.equals(C)
* null-아님 : A.equals(null) == false


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
