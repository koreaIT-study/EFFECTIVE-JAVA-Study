# Adapter 패턴(Wrapper 패턴)
* 기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꿔주는 패턴
  * 클래스의 인터페이스를 사용자가 원하는, 사용하고자 하는 다른 인터페이스로 변환하는 패턴으로, 서로 호환성이 전혀 없는 인터페이스를 사용하는 클래스들이 상호 호환되게끔 하여 함께 동작할 수 있도록 해준다.

꽤 많이쓰이는 디자인 패턴 중 하나.

![image](https://user-images.githubusercontent.com/82895809/230008792-36e2b2eb-184b-4dc8-b7e6-b7d55362e706.png)

## Adapter 패턴의 구성요소
### Target
* 클라이언트가 사용하길 원하는 인터페이스

### Adaptee
* 클라이언트가 갖고 있는 인터페이스 (어댑터에서 사용하고자 하는 인터페이스)

### Adapter
* Target 인터페이스를 구현하는 클래스로, 이 때 Adaptee의 함수 사용

### Client
* Target 인터페이스를 사용하는(사용하고 싶어하는) 주

```java
public interface Target {
	public void operation();
}
/////////////////// Target.java

public interface Adaptee {
	public void specificOperation();
}
/////////////////// Adaptee.java

public class SampleAdaptee implements Adaptee{

	@Override
	public void specificOperation() {
		System.out.println("This is Adapter");
	}
}
/////////////////// SampleAdaptee.java

public class SimpleAdapter implements Target {

	private Adaptee adaptee;
	
	public SimpleAdapter(Adaptee adaptee) {
		this.adaptee = adaptee;
	}

	@Override
	public void operation() {
		adaptee.specificOperation();
	}

}
/////////////////// SimpleAdapter.java

public class Client {
	Target targetAdapter = new SimpleAdapter(new SampleAdaptee());
	
	public void execute() {
		targetAdapter.operation();
	}
	
	public static void main(String[] args) {
		Client client = new Client();
		client.execute(); // This is Adapter
	}
}
//////////////////// Client.java
```

백준에서 많이 썼음.

![image](https://user-images.githubusercontent.com/82895809/230015763-310ec46f-3902-4122-9ff1-4a47478db5dd.png)


## Adapter 패턴의 두 가지 방식
어댑터 패턴은 두 가지 방식으로 구현할 수 있는데, 위 예제 코드는 그 중 하나인 'Object Adapter' 방식을 사용한다. Adaptee 를 구현 객체를 넘겨줬기 때문이다. 두 가지 방식에 대해 아래에 소개한다.

* Class Adapter
  * 상속 사용 : Adaptee를 상속하여 호환되게 하려는 메소드 사용.
  * 장점 : 어댑터 전체를 다시 구현할 필요가 없어 빠름.
  * 단점 : 상속을 활용하기 때문에, 결코 유연하지 못함.

![image](https://user-images.githubusercontent.com/82895809/230016609-45194e33-c600-4a57-bab5-c72560c77e1a.png)

* Object Adapter
  * 인터페이스 사용 : Adaptee 구현체를 넘겨받아 호환되게 하려는 메소드 사용.
  * 장점 : Composition을 사용하기 때문에 더욱 유연함.
  * 단점 : 대부분 코드를 구현해야 하기 때문에 비효율적일 수 있음.

![image](https://user-images.githubusercontent.com/82895809/230016783-5226137c-e556-4034-b23a-cb2644849255.png)


## Adapter 패턴의 궁극적인 사용 이유
어댑터 패턴은 변경할 수 없는 내부 구현, 라이브러리 등에 추가적인 기능을 만들고 싶을 때 유용하게 활용할 수 있다. 특히 기존에 사용하던 라이브러리의 동작을 바꿔야 하는 상황에서, 라이브러리의 구현을 바꾸는 것은 꽤나 위험한 선택일 것이다. 어떤 곳에서 사이드 이펙트가 발생할 지 모르기 때문이다.

따라서, 어댑터 패턴을 활용하여 기존 코드를 건드리지 않고 새로운 동작을 구현하면 훨씬 안정적이고 재활용성이 우수하다. 또한 폭넓은 확장 가능성을 고려해볼 수 있다.

다만, 어댑터 패턴을 구현하기 위해 필요한 구성요소들로 인해 클래스 자체가 많아지므로 복잡도가 증가할 수 있다. 하지만 용도에 맞게 적절히 사용한다면 충분히 아름다운 패턴이다.


출처 : https://velog.io/@haero_kim/%EC%9A%B0%EB%A6%AC%EB%8A%94-%EC%9D%B4%EB%AF%B8-%EC%96%B4%EB%8C%91%ED%84%B0-%ED%8C%A8%ED%84%B4%EC%9D%84-%EC%95%8C%EA%B3%A0-%EC%9E%88%EB%8B%A4
