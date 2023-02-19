객체의 소멸자.  

* finalizer와 cleaner(java 9)는 즉시 수행된다는 보장이 없다.
* finalizer와 cleaner는 실행되지 않을 수도 있다.
* finalizer 동작 중에 예외가 발생하면 정리 작업이 처리되지 않을 수도 있다.
* finalizer와 cleaner는 심각한 성능 문제가 있다.
* finalizer는 보안 문제가 있다.
* 반납할 자원이 있는 클래스는 `AutoCloseable`을 구현하고 클라이언트에서 close()를 호출하거나 `try-with-resource`를 사용해야한다.  

``` java
public class FianlizerIsBad {
	 @Override
	protected void finalize() throws Throwable {
		// TODO Auto-generated method stub
		super.finalize();
	}
}

```   

![image](https://user-images.githubusercontent.com/67637716/219944003-59f69733-464e-48ad-9997-e20eb2584b11.png) 

java9 부터 deprecate되었고, PhantomReference나 WeekReference, 또는 Clenaer를 사용하라고 되어있다.  
가장 적절한 것은 AutoCloseable을 구현하고 try-with-resource를 사용하는 것.  

## Finalize
![image](https://user-images.githubusercontent.com/67637716/219945363-9088ae59-6b34-435f-8cbe-6b58b74b8cb1.png)  

리소스 leak을 방지하기 위해 JVM이 실행하는 GC가 수행될 때 더 이상 사용하지 않는 자원에 대한 정리 작업을 진행하기 위해  호출되는 종료자 메서드.  
GC가 객체의 공간을 회수할 때, 객체의 finalize()메서드를 호출한다.  
![image](https://user-images.githubusercontent.com/67637716/219945777-3c1d7e3f-3c1c-4089-91fa-c3c95150e49d.png)   

### 단점
finalize가 언제 실행될지모르고, 실행이 안될수도 있다.  
JVM 튜닝의 많은 부분이 stop-the-world를 줄이기 위함인데, GC과정중 finalize() 메서드의 할일이 늘어나면 전체적인 성능 저하가 발생할 수 있음.  
finalize()에서 발생하는 예외는 무시가 됨. 따라서 로직이 불완전한 상태로 종료되어 문제를 발생할 수 있음.  

이러한 단점으로 java9에서부터 finalize()메서드가 Deprecated되었음.  


## cleaner
``` java
public class BigObject {
	private List<Object> resource;

	public BigObject(List<Object> resource) {
		this.resource = resource;
	}

	// finalize에서 하는 일을 runnable에서 한다.
	// 주의할점은 static class로 만들어야 하고,
	// 절대로 BigObject에 대한 reference가 있으면 안된다.
	public static class ResourceCleaner implements Runnable {
		private List<Object> resourceToClean;

		public ResourceCleaner(List<Object> resourceToClean) {
			this.resourceToClean = resourceToClean;
		}

		@Override
		public void run() {
			resourceToClean = null;
			System.out.println("cleaned up.");
		}
	}
}

public class CleanerIsNotGoot {
	public static void main(String[] args) throws InterruptedException {
		// Cleaner는 Phantom Reference로 만들어졌기 때문에 비슷하다.
		Cleaner cleaner = Cleaner.create();

		List<Object> resourceToCleanUp = new ArrayList<>();
		BigObject bigObject = new BigObject(resourceToCleanUp);

		// 어떤 object가 gc가 될때 runnable을 사용해서 정리작업을 해라
		cleaner.register(bigObject, new BigObject.ResourceCleaner(resourceToCleanUp));

		bigObject = null;
		System.gc();
		Thread.sleep(3000L);
	}
}

#### 정적이 아닌 중첩클래스는 자동으로 바깥 객체의 참조를 갖는다.
``` java
public class OuterClass {
	private void hi() {

	}

	class InnerClass {
		public void hello() {
			OuterClass.this.hi();
		}
	}

	private void printField() {
		Field[] declaredFields = InnerClass.class.getDeclaredFields();
		for (Field field : declaredFields) {
			System.out.println("field type : " + field.getType());
			System.out.println("field name : " + field.getName());
		}
	}

	public static void main(String[] args) {
		OuterClass outerClass = new OuterClass();
		InnerClass innerClass = outerClass.new InnerClass();
		System.out.println(innerClass);
		// com.example.demo.chaper01.item08.outerclass.OuterClass$InnerClass@3d012ddd

		outerClass.printField();
//		field type : class com.example.demo.chaper01.item08.outerclass.OuterClass
//		field name : this$0

	}
}
```  
#### 람다 역시 바깥 객체의 참조를 갖기 쉽다.
``` java
public class LambdaExample {
	private int value = 10;

	private Runnable instanceLambda = () -> {
	// 바깥 객체의 필드를 참조한 경우에만 참조가 생김.
		System.out.println(value);
	};

	public static void main(String[] args) {
		LambdaExample example = new LambdaExample();
		Class<? extends Runnable> class1 = example.instanceLambda.getClass();
		Field[] declaredFields = class1.getDeclaredFields();
		for (Field field : declaredFields) {
			System.out.println("field type : " + field.getType());
			System.out.println("field name : " + field.getName());
		}

//		field type : class com.example.demo.chaper01.item08.outerclass.LambdaExample
//		field name : arg$1

	}
}

```  

```  


# 권장 하는 방법 : AutoCloseable
AutoCloseable interface java doc내용을 보면 닫을 때까지 리소스(file or socket handles)를 보유할 수 있는 개체,  
close()메서드는 try-with-resource블럭에서 자동적으로 호출된다고 정의되어있다.  

``` java
public class AutoClosableIsGood implements AutoCloseable {
	private BufferedInputStream inputStream;

	@Override
	public void close() {
		try {
			inputStream.close();
		} catch (IOException e) {
			throw new RuntimeException("failed to close " + inputStream);
		}
	}
}

public class App {
	public static void main(String[] args) {
		try (AutoClosableIsGood good = new AutoClosableIsGood()) {
			// TODO 자원 반납 처리가 됨.
		}
	}
}

```  

## Cleaner를 언제 사용할까?
정답: 안전망.  

=> AutoCloseable을 이용해 자원반납을 하도록 만들어 놓았지만, 사용하는 client쪽에서 try-with-resource를 사용하지 않았을 경우, gc를 할 떄 자원이 반납될 수 있는 `기회`를 주도록 하는 방법이다.(안전망)  
=> native method(C또는 C++로 된 OS에 접근할 수 있는 메서드) 자원을 해제할 때 사용할 수 있다.


``` java
public class Room implements AutoCloseable {
	private static final Cleaner cleaner = Cleaner.create();

	// 청소가 필요한 자원. 절대 Room을 참조해서는 안됨!
	private static class State implements Runnable {
		int numJunkPiles; // 방 안의 쓰레기 수

		State(int numJunkPiles) {
			this.numJunkPiles = numJunkPiles;
		}

		@Override
		public void run() {
			System.out.println("방 청소");
			numJunkPiles = 0;
		}
	}

	// 방의 상태, cleanable과 공유
	private final State state;

	// cleanable 객체, 수거 대상이 되면 방을 청소.
	private final Cleaner.Cleanable cleanable;

	public Room(int numJunkPiles) {
		state = new State(numJunkPiles);
		cleanable = cleaner.register(this, state);
	}

	@Override
	public void close() throws Exception {
		cleanable.clean();
	}

}


// 잘 사용한 방법
public class Adult {
	public static void main(String[] args) throws Exception {
		try (Room myRoom = new Room(7)) {
			System.out.println("안녕~");
		}
	}
}

// 안전망
public class Teenager {
	public static void main(String[] args) {
		new Room(99);
		System.out.println("아무렴");

//		System.gc();
	}
}

```  



