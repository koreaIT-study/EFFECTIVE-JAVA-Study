임의의 객체를 불변 객체로 만들어주는 기능.  
javascript에 있는 메서드.  
* Object.freeze()에 전달한 객체는 그 뒤로 변경될 수 없다.
  * 새 프로퍼티를 추가하지 못함
  * 기존 프로퍼티를 제거하지 못함
  * 기존 프로퍼티 값을 변경하지 못함
  * 프로토타입을 변경하지 못함
* strict 모드에서만 동작함
* 비슷한 류의 function으로 Object.seal()과 Object.preventExtention() 이있음.

![image](https://user-images.githubusercontent.com/67637716/216811509-139c14cb-46b2-426e-b4ac-1c95f92065bc.png)  
자바스크립트에서는 객체를 Update, Delete시키는 것이 가능하다. (const로 선언해도 변경 가능)  
java도 마찬가지로, final이나 const도 객체를 바꾸는것이 불가능하고, 안의 필드는 변경 가능하다.  
``` java
public class Person {
	private String name;
	private int birthYear;

	public Person(String name, int birthYear) {
		this.name = name;
		this.birthYear = birthYear;
	}

	public static void main(String[] args) {
		final Person person = new Person("hong", 1995);
		person.name = "hong22";

	}
}

```  

![image](https://user-images.githubusercontent.com/67637716/216811742-fb251bfc-29a3-4915-9af4-0e02b6033040.png)  
freeze() 메서드를 사용하면 객체 필드 변경 불가능!  

``` java
public class Person {
	private String name;
	private int birthYear;

	public Person(String name, int birthYear) {
		this.name = name;
		this.birthYear = birthYear;
	}

	public void setName(String name) {
		checkIfObjectIsFrozen();
		this.name = name;
	}

	private void checkIfObjectIsFrozen() {
		if (this.frozen()) {
			throw new IllegalStateException();
		}
	}

	public void setBirthYear(int birthYear) {
		checkIfObjectIsFrozen();
		this.birthYear = birthYear;
	}

	public static void main(String[] args) {
		final Person person = new Person("hong", 1995);

	}

}
```  
java에서도 이런식으로 freeze를 사용할 수 있지만 side-effect가 발생할 가능성이 높고 코딩이 너무 복잡해짐으로 거의 사용하지 않는다. 
final로 필드를 만들어도 Reference가 변경되지 않는것이지, 안에 있는 값은 바뀔 수 있음.  

``` java
public class Person {
	private final String name;
	private final int birthYear;
	private final List<String> hobby;

	public Person(String name, int birthYear) {
		this.name = name;
		this.birthYear = birthYear;
		this.hobby = new ArrayList<>();
	}

	public static void main(String[] args) {
		final Person person = new Person("hong", 1995);
		person.birthYear = 1999; // 불가
		person.hobby.add("coding"); // 가능
		
	}

}

```  

 
