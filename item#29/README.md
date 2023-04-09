1. [한정적 타입 매개변수](#한정적-타입-매개변수)
# 한정적 타입 매개변수
![image](https://user-images.githubusercontent.com/92290312/230756088-9e1d5e1e-6616-41f4-b092-bde73f78e619.png)
![image](https://user-images.githubusercontent.com/92290312/230756102-fe385547-83d4-49f4-97f4-4a3ea72005d0.png)
<br/>
제네릭은 컴파일이 되면 소거되고 제네릭 타입은 Object로 치환됨.<br/>
이 타입을 한정짓고 싶다면?<br/><br/>
한정적 타입 매개변수를 사용하면됨.<br/>
![image](https://user-images.githubusercontent.com/92290312/230756480-0051c2ca-22bc-47b3-871e-638cb3311ad9.png)
<br/>
이렇게만 하면 아래 처럼 오류가 뜸.
```java
Exception in thread "main" java.lang.ClassCastException: class [Ljava.lang.Object; cannot be cast to class [Ljava.lang.Number; ([Ljava.lang.Object; and [Ljava.lang.Number; are in module java.base of loader 'bootstrap')
	at me.whiteship.chapter05.item29.bounded_type.Stack.<init>(Stack.java:21)
	at me.whiteship.chapter05.item29.bounded_type.Stack.main(Stack.java:48)
```
왜냐하면 한정적 타입 매개변수를 사용하면 Object 의 배열인데 
![image](https://user-images.githubusercontent.com/92290312/230756284-16adab29-0e16-4e5e-a612-50b8430a1a86.png)<br/>
매개변수 타입의 배열을 사용하기 위해 캐스팅 하는 과정(E[]로 캐스팅)에서 타입 안전하지 않기때문에 오류를 뱉음.<br/>
![image](https://user-images.githubusercontent.com/92290312/230756494-c831bf24-190e-41dc-b204-e7e76f8b7088.png)<br/>
위와 같이 해주면 잘 됨.<br/>

![image](https://user-images.githubusercontent.com/92290312/230756619-1818b080-f1a4-4687-85f9-318cba581339.png)<br/>
위와같이 여러 클래스와 인터페이스로 제한하고 있는 매개변수 타입이라면 제한하는 모든것들을 구현하고 있어야함.<br/>
인터페이스와 클래스를 둘다 제한에 놓는다면 클래스를 먼저 넣어야함.
