``` java
public class Account {
	private String accountId;

	public Account(String accountId) {
		this.accountId = accountId;

		if (accountId.equals("푸틴")) {
			throw new IllegalArgumentException("푸틴은 계정을 막습니다");
		}
	}

	public void transfer(BigDecimal amount, String to) {
		System.out.printf("transfer %f from %s to %s\n", amount, accountId, to);
	}

}


``` 

``` java
public class BrokenAccount extends Account {

	public BrokenAccount(String accountId) {
		super(accountId);
	}

	@Override
	protected void finalize() throws Throwable {
		this.transfer(BigDecimal.valueOf(10000000), "hong");
	}

}

```  

``` java
public class FinalizeAttackTest {
	public static void main(String[] args) throws InterruptedException {
		Account account = null;
		try {
			account = new BrokenAccount("푸틴");
		} catch (Exception e) {
			System.out.println("이러면??");
		}
		System.gc();
		Thread.sleep(3000L);
		
		// 이러면??
		// transfer 10000000.000000 from 푸틴 to hong

	}
}

```  


## Finalizer 공격 막는 방법
1. Account를 상속불가능 하게 final class로 만든다.  
2. Account에서 아무것도 하지않는 finalize()를 재정의 하고 상속을 하지 못하게 final을 붙인다.  
``` java
public class Account {
	private String accountId;

	public Account(String accountId) {
		this.accountId = accountId;

		if (accountId.equals("푸틴")) {
			throw new IllegalArgumentException("푸틴은 계정을 막습니다");
		}
	}
	
	@Override
	protected final void finalize() throws Throwable {
	}
	
	public void transfer(BigDecimal amount, String to) {
		System.out.printf("transfer %f from %s to %s\n", amount, accountId, to);
	}

}

```  
