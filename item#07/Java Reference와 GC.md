https://d2.naver.com/helloworld/329631
설명 굿

# Java Reference와 GC
Java의 GC는 공통적으로 크게 2가지 작업을 수행한다고 볼 수 있다.
1. Heap내의 객체 중에서 가비지를 찾아낸다.
2. 찾아낸 가비지를 처리해서 Heap의 메모리를 회수한다.

최초의 Java에서는 GC작업에 애플리케이션의 사용자 코드가 관여하지 않도록 구현되어 있었다.

그러나 위 2가지 작업에서 좀 더 다양한 방법으로 객체를 처리하려는 요구가 있었고, JDK1.2부터는 java.lang.ref 패키지를 추가해 제한적이나마 사용자 코드와 GC가 상호작용할 수 있게 하고 있다.

java.lang.ref 패키지는 전형적인 객체 참조인 strong reference 외에도 soft, weak, phantom 3가지의 새로운 참조 방식을 각각의 Reference 클래스로 제공한다.

이 3가지 Reference 클래스를 애플리케이션에 사용하면 앞서 설명하였듯이 GC에 일정 부분 관여할 수 있고, LRU(Least Recently Used) 캐시 같이 특별한 작업을 하는 애플리케이션을 더 쉽게 작성할 수 있다.

## GC와 Reachability
GC는 객체가 가비지인지 판별하기 위해 reachability라는 개념을 사용한다.

어떤 객체에 유효한 참조가 있으면 'reachable'로, 없으면 'unreachable'로 구별하고, unreachable 객체를 가비지로 간주해 GC를 수행한다.

한 객체는 여러 다른 객체를 참조하고, 참조된 다른 객체들도 마찬가지로 또 다른 객체들을 참조할 수 있으므로 객체들은 참조 사슬을 이룬다. 

이런 상황에서 유효한 참조 여부를 파악하려면 항상 유효한 최초의 참조가 있어야 하는데 이를 객체 참조의 root set이라고 한다.

힙에 있는 객체들에 대한 참조는 4가지 종류 중 하나다.
 * 힙 내의 다른 객체에 의한 참조
 * Java 스택, 즉 Java 메서드 실행 시에 사용하는 지역 변수와 파라미터들에 의한 참조
 * 네이티브 스택, 즉 JNI(Java Native Interface)에 의해 생성된 객체에 대한 참조
 * 메서드 영역의 정적 변수에 의한 참조

이들 중 힙 내의 다른 객체의 의한 참조를 제외한 나머지 3개가 root set으로, reachability를 판가름하는 기준이 된다.

root set으로부터 시작한 참조 사슬에 속한 객체들은 reachable 객체이고, 이 참조 사슬과 무관한 객체들이 unreachable 객체로 GC 대상이다.
