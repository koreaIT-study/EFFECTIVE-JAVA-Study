1. [final과 자바 메모리 모델](#final과-자바-메모리-모델)
2. [CountDownLatch 클래스](#CountDownLatch-클래스)

# final과 자바 메모리 모델
JMM : JVM 메모리 구조 X<br/>
  적합한 프로그램 실행을 알려주는 규칙.<br/>
  한 스레드 내에서 작동이 유효한지<br/>
  https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.4 (JSL 17.4)<br/>
  <br/>
  ![image](https://user-images.githubusercontent.com/92290312/225039962-7abb1454-5b1b-4d0c-927f-1da49ecea3c6.png)<br/>
메모리 모델 규정상 스레드마다 위에처럼 실행될지 아래처럼 실행될지 모름<br/>
그래서 멀티 스레드에서는 차마 할당되기 전에 참조를 하게 될 수도 있음(이론상)<br/>
<br/>
**하지만 final 필드는 할당이 되어야 사용할 수 있음을 보장함**

# CountDownLatch 클래스
* 병행(Concurrency)과 병렬(Parallelism)의 차이
  병행은 프로세스를 시분할 하여 빠르게 번갈아 실행해 동시에 실행되는 것처럼 보이는 것.<br/>
  병렬은 실제로 동시에 실행되는 것.(멀티코어에서만 가능함)<br/>
![image](https://user-images.githubusercontent.com/92290312/225052299-4b6e7142-7cc8-46a8-a4a7-306cc3bf0f8c.png)
