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

