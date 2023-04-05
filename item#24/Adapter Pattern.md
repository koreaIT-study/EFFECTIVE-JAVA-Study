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

















https://velog.io/@haero_kim/%EC%9A%B0%EB%A6%AC%EB%8A%94-%EC%9D%B4%EB%AF%B8-%EC%96%B4%EB%8C%91%ED%84%B0-%ED%8C%A8%ED%84%B4%EC%9D%84-%EC%95%8C%EA%B3%A0-%EC%9E%88%EB%8B%A4
