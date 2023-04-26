1. [ThreadLocal](#ThreadLocal)
2. [ThreadLocalRandom](#ThreadLocalRandom)

# ThreadLocal
멀티 스레드 환경에서는 신경써야할 것들이 아주 많음.<br/>
스레드에 안전하게 코드를 짜는것, 교착 상태, lock lock 등등<br/>
이때 ThreadLocal을 사용하면 스레드 전용 지역변수를 만들어서 해결할 수 있다.<br/>

![image](https://user-images.githubusercontent.com/92290312/234446005-344d7d6d-1ca1-4cde-8f69-b63236b5eef9.png)<br/>

# ThreadLocalRandom
Random 의 next를 쓸때는 결과적으로 하나의 next를 사용하는데 AtomicLong이라는 클래스를 사용한다.<br/>
AtomicLong 은 멀티 스레드 환경에서 lock을 사용하지 않고 안전하게 사용할 수 있는 클래스임.<br/>
lock을 기다리고 주고 받는 시간때문에 lock은 성능에 영향을 미친다.<br/>

* 염세적인 lock : lock을 기다리고 주고받음<br/>
* 낙관적인 lock : 일단 메서드 안으로 들어가고 사용중이면 실패하거난 재시도함.<br/>

![image](https://user-images.githubusercontent.com/92290312/234447462-2110baf9-8b48-4c09-ba8a-0fa67b5195fb.png)<br/>

Random 안의 compareAndSet이 이런 방식으로 작동 되다보니 <br/>
여러 스레드에서 next를 많이 호출하면 어느하나는 꼭 실패 및 재시도가 발생한다.<br/>
=> 성능에 문제가 생김
<br/>

이럴거면 ThreadLocalRandom 을 쓰자.
