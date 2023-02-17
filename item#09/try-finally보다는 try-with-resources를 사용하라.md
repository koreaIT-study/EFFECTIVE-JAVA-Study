# try-finally 보다는 try-with-resources 를 사용하라
자바는 close메서드를 직접 달아줘야하는 자원이 많음 => 이걸 놓쳐서 성능문제로 이어지기도 함.<br/>
이를 위한 안전망으로 finalizer를 활용하는데 그리 믿을만하지 못함.<br/>
```java
// 예제 1
static String firstLineOfFile(String path) throws IOException{
  BufferedReader br = new BufferedReader(new FileReader(path));
  try{
    return br.readLine();
  }finally{
    br.close();
  }
}
```
```java
// 예제 2 : 자원이 둘 이상인 경우
static void copy(String src, String dst) throws IOEception{
  InputStream in = new FileInputStream(src);
  try{
    OutputStream out = new FileOutputStream(dst);
    try{
      byte[] buf = new byte[BUFFER_SIZE];
      int n;
      while((n = in.read(buf)) >= 0) out.write(buf, 0, n);
    } finally{
      out.close();
    }
   } finally{
    in.close();
   }
}
// 코드가 너무 더러움
```
위의 예제는 try-finally 문을 제대로 사용한 것이지만 미묘한 결점이 있다.<br/>

예외는 try 블록과 finally 블로 모두 발생할 수 있다.<br/>
예를 들어, 기기에 물리적 문제가 생기면 firstLineOfFile 메서드 안에서 readLine 메서드가 예외를 던지고,<br/>
같은 이유로 close 메서드도 실패함. => 두번째 예외가 첫번째 첫번째 예외를 집어 삼켜 디버깅을 어렵게함(첫번째예외의 로그가 안남음)<br/>

### 해결책 try-with-resource
자바 7에서 생긴 try-with-resource가 이런 문제를 해결해줌.<br/>

try-with-resource를 사용하기 위해서는 해당 자원이 AutoCloseable 인터페이스를 구현해야함.<br/>
단순히 void를 반환하는 close 메서드 하나만 저으이한 인터페이스임.<br/>

```java
// try-with-resource 적용 예제1
static String firstLineOfFile(String path) throws IOException{
  try(BufferedReader br = new BufferedReader(new FileReader(path))){
    return br.readLine();
  }
}
```
```java
// try-with-resource 적용 예제2
static void copy(String src, String dst) throws  IOException{
  try(InputStream in - new FiileInputStream(src);
      OutputStream out = new FileOutputStream(dst)){
    byte[] buf = new byte[BUFFER_SIZE];
    int n;
    while((n = in.read(buf)) >= 0) out.write(buf, 0, n);
  }
}
```
try-with-resource 를 사용하므로써 코드도 읽기 수월하고 문제 진단도 훨씬 좋음.<br/>
적용예제1을 보면 readLine 과 close를 호출할때 둘다 예외가 발생하면 둘다 예외가 기록됨(숨겨진 예외는 suppressed 를 달고 출력)<br/>
또 자바 7에서 추가된 Throwable의 getSuppressed 메서드를 이용하면 프로그램 코드에서 가져올 수도 있음.<br/>
