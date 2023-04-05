# 멤버 클래스는 되도록 static으로 만들라.
중첩 클래스(nested class)란 다른 클래스 안에 정의된 클래스를 말한다.   
중첩 클래스는 자신을 감싼 바깥 클래스에서만 쓰여야 하며, 그 외의 쓰임새가 있다면 톱레벨 클래스로 만들어야 한다.

* 중첩 클래스 종류
  * 정적 멤버 클래스
  * 비정적 멤버 클래스
  * 익명 클래스
  * 지역 클래스

정적 멤버 클래스를 제외한 나머지는 전부 내부 클래스(inner class)에 해당한다.

## 정적 멤버 클래스
클래스 내부에 static으로 선언되고, 바깥 클래스의 private 멤버에도 접근할 수 있다는 점만 제외하고는 일반 클래스와 똑같다.   
정적 멤버 클래스는 다른 정적 멤버와 똑같은 접근 규칙을 적용받는다.   
예컨대 private으로 선언하면 바깥 클래스에서만 접근할 수 있는 식이다.

정적 멤버 클래스는 흔히 바깥 클래스와 함께 쓰일 때만 유용한 public 도우미 클래스로 쓰인다.   

**멤버 클래스에서 바깥 인스턴스에 접근할 일이 없으면 무조건 static을 붙여서 정적 멤버 클래스로 만들자.**

**바깥 클래스의 인스턴스를 필요로하지 않는다.**

![image](https://user-images.githubusercontent.com/82895809/230024378-281fac49-def2-4397-8aa1-0b14d1b28209.png)

static을 생략하면 바깥 인스턴스로의 숨은 참조가 생기고, 참조를 저장하기 위해 시간과 공간이 소비된다.   
또한 가비지 컬렉션이 바깥 클래스의 인스턴스를 수거하지 못한다.

## 비정적 멤버 클래스
static이 붙지 않은 멤버 클래스다.   
비정적 멤버 클래스의 인스턴스는 바깥 클래스의 인스턴스와 암묵적으로 연결된다.   
그래서 비정적 멤버 클래스의 인스턴스 메서드에서 정규화된 this(```클래스명.this```)를 사용해 바깥 인스턴스의 메서드를 호출하거나 바깥 인스턴스의 참조를 가져올 수 있다.   
바깥 인스턴스 없이 생성 불가능하다.

**비정적 멤버 클래스는 어댑터를 정의할 때 자주 쓰인다.** 즉, 어떤 클래스의 인스턴스를 감싸 마치 다른 클래스의 인스턴스처럼 보이게 하는 뷰로 사용하는 것이다.

```java
public class OuterTestClass {

    private final String name;
    
    public OuterTestClass(String name) {
        this.name = name;
    }
    
    public String getName() {
    	NonStaticClass nonStaticClass = new NonStaticClass("Inner Class + ");
    	return nonStaticClass.getNameWithOuter();
    }
    
    private class NonStaticClass {
        private final String innerName;
        
        public NonStaticClass(String innerName) {
            this.innerName = innerName;
        }
        
        public String getNameWithOuter() {
            return innerName + OuterTestClass.this.name;
        }
    }
}
///////////////////////////////////////////

public class TestMain {
	public static void main(String[] args) {

		OuterTestClass a = new OuterTestClass("김동진");
		System.out.println(a.getName()); // Inner Class + 김동진

		int apply = Calculator.Operation.PLUS.apply(1, 2);
		System.out.println(apply); // 3
	}
}
```

## 익명 클래스
익명 클래스는 바깥 클래스의 멤버도 아니다.    
쓰이는 시점과 동시에 인스턴스가 만들어진다.   
정적 문맥에서라도 상수 변수 이외의 정적 멤버는 가질 수 없다.

익명 클래스는 여러 인터페이스를 구현할 수 없고, 인터페이스를 구현하는 동시에 다른 클래스를 상속할 수도 없다.

즉석에서 작은 함수 객체나 처리 객체를 만드는 데 주로 사용했지만 람다 등장 이후로 람다가 이 역할을 대체했다.

```java
List<Integer> list = Arrays.asList(10, 5, 6, 7, 1, 3, 4);

// 익명 클래스 사용
Collections.sort(list, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return Integer.compare(o1, o2);
    }
});

// 람다 도입 후
Collections.sort(list, Comparator.comparingInt(o -> o));
```

## 지역 클래스
가장 드물게 사용된다.   
유효범위가 지역 변수와 같고, 비정적 문맥에서 사용될 때만 바깥 인스턴스를 참조할 수 있고, 정적 멤버는 가질 수 없고, 짧게 작성해야 한다.

