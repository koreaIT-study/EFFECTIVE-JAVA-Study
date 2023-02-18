https://readystory.tistory.com/116  
# private 생성자나 열거타입으로 싱글턴임을 보증하라
<b>싱글턴(singleton)</b> : 인스턴스를 하나만 생성할 수 있는 클래스<br>
(인터페이스를 구현해서 만든 싱글턴이 아니면 mock 구현으로 대체할 수 없음 => (1)싱글턴은 클라이언트가 테스트하기 어려울 수 있음)<br>
<br>
### 싱글턴을 만드는 방식
모두 공통적으로 생성자는 private으로 감춤, 유일한 인스턴스 접근수단으로 public static 멤버를 만듬
1. public static 멤버가 final 인 방식
    ```java
    public class Elvis{
      public static final Elvis INSTANCE = new Elvis();
      private Elvis(){...}

      public void leaveTheBuilding(){...}
    }
    ```
    private 생성자가 Elvis.INSTANCE를 초기화할 때 딱 한번만 호출되어 싱글턴임을 보장.<br>
    but 리플렉션을 사용해 private 생성자 호출 가능. =>  이럴때는 두번째 객체생성 때 오류 던져주면됨.
    
    **장점<br>**
    해당 클래스가 싱글턴인게 명확히 드러남.<br>
    간결함.
    
    
2. 정적 팩터리 메서드를 public static 멤버로 제공
    ```java
    public class Elvis{
      private static final Elvis INSTANCE = new Elvis();
      private Elvis(){...}
      public static Elvis getInstance(){ return INSTANCE; }

      public void leavTheBuilding(){...}
    }
    ```
    getInstance() 가 항상 같은 객체만 return 하지만 역시나 리플렉션에 뚫림.
    
    **장점<br>**
    API를 바꾸지 않아도 싱글턴이 아니게 변경 가능. <br>
    정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있음.<br>
    정적 팩터리의 메서드 참조를 supplier로 사용 가능
    
    1번화 2번 방법은 직렬화(Serializable 구현) 후 역직렬화 할 때 새로은 인스턴스가 만들어짐.<br>
    => readResolve 메서드를 제공해야 같은 인스턴스를 return 함.
    ```java
      private Object readResolve(){
        return INSTANCE;
      }
    ```
          
3. 원소가 하나인 열거타입을 선언 \* 가장 추천
    ```java
    public enum Elvis{
      INSTANCE;
      
      public void leaveTheBuilding(){...}
    }
    ```
    
    **장점<br>**
    간결.<br>
    직렬화가능.<br>
    리플렉션 공격도 막아줌.<br>
    
    단, Enum 외의 클래스를 상속해야 한다면 이 방법은 사용불가.<br>
    (열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있음.)
