* equals 비교에 사용하는 정보가 변경되지 않았다면 hashCode는 매번 같은 값을 리턴해야한다.(변경되거나, 애플리케이션을 다시 실행했다면 달라질 수 있다.)
* 두 객체에 대한 equals가 같다면, hashCode의 값도 같아야한다.
* 두 객체에 대한 equals가 다르더라도, hashCode의 값은 같을 수 있지만 해시테이블 성능을 고려해 다른 값을 리턴하는 것이 좋다. 
* hashCode를 구현할 떄는 equals에서 사용하는 모든 필드들을 사용해서 hashCode를 반영해야한다.  

## 같은 인스턴스인데 다른 hashCode  
``` java
public class PhoneNumber {
	private final short areaCode, prefix, lineNum;

	public PhoneNumber(int areaCode, int prefix, int lineNum) throws IllegalAccessException {
		this.areaCode = rangeCheck(areaCode, 999, "area code");
		this.prefix = rangeCheck(prefix, 999, "prefix");
		this.lineNum = rangeCheck(lineNum, 9999, "lineNum");
	}

	private static short rangeCheck(int val, int max, String arg) throws IllegalAccessException {
		if (val < 0 || val > max) {
			throw new IllegalAccessException(arg + ": " + val);
		}
		return (short) val;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;

		if (!(obj instanceof PhoneNumber))
			return false;
		PhoneNumber pn = (PhoneNumber) obj;
		return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
	}

	public static void main(String[] args) throws IllegalAccessException {
		Map<PhoneNumber, String> map = new HashMap<>();
		PhoneNumber number1 = new PhoneNumber(123, 456, 7890);
		PhoneNumber number2 = new PhoneNumber(123, 456, 7890);

		// TODO 같은 인스턴스인데 다른 hashCode
		System.out.println(number1.equals(number2));
		System.out.println(number1.hashCode());
		System.out.println(number2.hashCode());

		map.put(number1, "hong");
		map.put(number2, "min");

		System.out.println(map.get(number1));
		System.out.println(map.get(number2));
    
    // true
    // 1694819250
    // 1365202186
    // min
    // hong

	
	}

}
```  

###  HashMap에 get, put과정 이해
* put을 할때 hashcode라는 메서드를 실행해서 어느 버켓에 넣을지 정하고 넣음
* get을 할때도 key에 대한 hashcode값을 먼저 가져와서 해시에 해당하는 버켓에 해당하는 object를 꺼내는 과정

##  다른 인스턴스인데 같은 hashCode를 쓴다면?
``` java

@Override
public int hashCode() {
	return 42; //  무조건 같은 hashCode를 return 하도록 함.
}

public static void main(String[] args) throws IllegalAccessException {
	Map<PhoneNumber, String> map = new HashMap<>();
	PhoneNumber number1 = new PhoneNumber(123, 456, 7890);
	PhoneNumber number2 = new PhoneNumber(456, 789, 1111);

	// TODO 같은 인스턴스인데 다른 hashCode
	System.out.println(number1.equals(number2));
	System.out.println(number1.hashCode());
	System.out.println(number2.hashCode());

	map.put(number1, "hong");
	map.put(number2, "min");

	System.out.println(map.get(number2));
	
	// false
	// 42
	// 42
	// min

  
```   

#### HashMap 내부의 LinkedList
1. hash collision이 일어남
2. 해시 버켓에 들어있는 object를 LinkedList로 만듬
3. 버켓에서 꺼내면 LinkedList로 나옴
4. 해시코드가 같으면 모두 같은 버켓으로 들어감
5. 그 버켓안에 들어있는 LinkedList에 쭉 들어감
6. 그 LinkedList에서 꺼내서 equals로 비교를 하게됨
7. hashmap을 쓰는 장점이 없어진다.  




# hashCode 구현 방법
```  java
@Override
public int hashCode() {
	int result = Short.hashCode(areaCode);
	result = 31 * result + Short.hashCode(prefix);
	result = 31 * result + Short.hashCode(lineNum);
	return result;

}
```  
1. 핵심 필드 하나의 값의 해쉬값을 계산해서 result값을 초기화한다.
2. 기본 타입은 Type.hashCode(해당하는 Type), 참조 타입은 해당 필드의 hashCode  
배열은 모든 원소를 재귀적으로 위의 로직을 적용하거나, Array
result = 31 * result + 해당 필드의 hashCode 계산값
3. result를 리턴한다. 
![image](https://user-images.githubusercontent.com/67637716/221396458-a6494d6e-9670-4ee7-8051-6e5717ae0b32.png)  

31을 곱해주는 이유??  
1. 짝수를 가지고 연산을 하면 끝이 0으로 채워지면서 숫자가 왼쪽으로 밀리면서 날아갈수 있다.
2. 해싱을 할 때 해싱충돌이 가장 적은 숫자가 31이다.  
3. 별다른 이유가 없다면 일반적으로 31을 쓰는것이 좋다.  


위와 같이 쓰지않고 아래와 같이 사용할 수 있다.  
``` java
@Override
public int hashCode() {
	return Objects.hash(areaCode, prefix, lineNum);
}
	
===> Objects Class
 public static int hash(Object... values) {
        return Arrays.hashCode(values);
    }
    
===> Arrays.hashCode()
public static int hashCode(Object a[]) {
if (a == null)
    return 0;

int result = 1;

for (Object element : a)
    result = 31 * result + (element == null ? 0 : element.hashCode());

return result;
}

```  

#### hash 캐싱
해싱을 하려는 instance가 불변 객체이고, 해싱을 하는 연산이 많다면 밑에와 같이 캐싱을 할 수 있다.  
지연초기화 기법을 사용할 때, 주의할점은 멀티스레드 안정성을 고려해야한다.  

``` java
private final short areaCode, prefix, lineNum;
private int hashCode;

@Override
public int hashCode() {
	int result = hashCode;
	if (result == 0) { // 여러 스레드가 동시에 들어올 수 있음.  
		result = Objects.hash(areaCode, prefix, lineNum);
	}
	return result;
}
```  

* 가장 좋은 방법은 롬북의 @EqualsAndHashCode를 사용하는 것이 사용편의점 관점에서 좋다. 
* equals를 직접 작성하면, test도 해야함. equals test도 마찬가지.
* 어떻게 hashCode를 작성하는지를 외부로 나타내지 않을수 있다.   






