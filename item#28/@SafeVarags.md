# @SafeVarags
* 생성자와 메소드의 제네렉 가변인자에 사용할 수 있는 어노테이션

제네릭 타입과 가변인수 메소드를 함께 쓰면 경고 메시지를 받게 된다.

가변인수 메서드를 호출할 때마다 가변인수 매개변수를 담을 배열이 하나 만들어지는데, 이때 그 배열의 원소가 실체화 불가 타입이라면 경고가 발생하는 것이다.

이 문제는 @SafeVarags로 대처할 수 있다.

```java
package org.example;

import java.util.ArrayList;
import java.util.List;

public class Dangerous {

    @SafeVarargs // Not safe
    static void dangerous(List<String>... stringLists) {
        List<Integer> integerList = List.of(42); // List<String>... => List[] 리스트 배열이니까 오브젝트 배열에 할당할 수 있다. 공변이니까.
        Object[] objects = stringLists;
        objects[0] = integerList;   // 힙 오염 발생
        String s = stringLists[0].get(0);   // ClassCastException
    }

    @SafeVarargs
    static <T> void safe(T... values) {
        for (T value : values) {
            System.out.println(value); // 새로운 변수에 할당하지 않고, 리턴하지 않고 단순 출력할 때만 @SafeVarargs 쓰는 것을 권장.
        }
    }

    public static void main(String[] args) {
        List<String> stringList = new ArrayList<>();
        stringList.add("a");
        stringList.add("b");
        stringList.add("c");

        List<String> stringListB = new ArrayList<>();
        stringListB.add("a");
        stringListB.add("b");
        stringListB.add("c");

        List<Integer> integerList = new ArrayList<>();
        integerList.add(1);
        integerList.add(2);
        integerList.add(3);

        safe(stringListB, stringList, integerList);
        dangerous(stringList, stringListB);
    }
}

```

<img width="1178" alt="image" src="https://user-images.githubusercontent.com/82895809/231150235-52f300f0-620f-4887-91f4-21e07885f193.png">


