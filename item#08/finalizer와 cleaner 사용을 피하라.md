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







