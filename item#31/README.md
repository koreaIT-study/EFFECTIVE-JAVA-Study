1. [타입 추론](#타입-추론)

# 타입 추론
타입 추론이란 컴파일러가 타입을 알아내는 것.<br/>
![image](https://user-images.githubusercontent.com/92290312/234442127-e72d3a34-ecce-486c-ad11-5b290b7983c4.png)<br/>

* 선언 시 한쪽에 타입을 명시해주면 다른 쪽에 명시를 안해줘도 알아먹음.<br/>
* 매개변수에 의해 타입을 추론할 수 있으면 리턴타입을 명시 안해줘도 알아먹음.<br/>

타겟타입 추론 능력이 자바 8에서 메서드의 인자타입까지로 확장됐음.<br/>
```java
List<String> stringList = Collection.<String>emptyList();
```
자바 7에서는 위의 코드처럼 써줘야했었음.(타입위트니스 라고 부름)<br/>
현재는 아래처럼도 가능함.
```java
var listOfIntegerBoxs = new ArrayList<Box<Integer>>();
```
