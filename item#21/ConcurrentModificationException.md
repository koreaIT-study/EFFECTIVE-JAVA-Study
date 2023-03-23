# 현재 바뀌면 안되는 것을 수정할 때 발생하는 예외
* 멀티 스레드가 아니라 싱글 스레드 상황에서도 발생할 수 있다.  
* 가령, fail-fast 이터레이터를 사용해 콜렉션을 순회하는 중에 콜렉션을 변경하는 경우

![image](https://user-images.githubusercontent.com/67637716/227120799-8537a969-766b-47cd-b860-a1d4a2f0d67d.png)  
immutable Collection을 수정할 땐 UnsupportedOperationException 발생.  

####  ConcurrentModificationException
![image](https://user-images.githubusercontent.com/67637716/227121072-ca8ebe28-eae4-4bc9-a224-6112fbd58fc5.png)  

## 우회 하는 방법
``` java
      // 우회 하는 방법
        // iterator를 사용
        for (Iterator<Integer> iterator = numbers.iterator(); iterator.hasNext(); ) {
            Integer integer = iterator.next();
            if (integer == 3) {
                iterator.remove(); // iterator를 사용하여 빼내면 ConcurrentModificationException 안남
            }
        }

        // index를 사용하는 방법
        for (int i = 0; i < numbers.size(); i++) {
            if (numbers.get(i) == 3) {
                numbers.remove(numbers.get(i));
            }
        }

        // removeIf 사용하기
        numbers.removeIf(number -> number == 3);
```  
