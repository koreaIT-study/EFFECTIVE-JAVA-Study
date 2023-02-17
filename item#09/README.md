1. [한번에 close 하면 안되나?](#한번에-close-하면-안되나?)
2. [예외가 먹히는 상황](#예외가-먹히는-상황)
3. [퍼즐러 예외 처리 코드 실수](#퍼즐러-예외-처리-코드-실수)
4. [try-with-resource](#try-with-resource)

## 한번에 close 하면 안되나?
```java
    static void copy(String src, String dst) throws IOException {
        InputStream in = new FileInputStream(src);
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        } finally {
            in.close();
            out.close();
        }
    }
```
이렇게 사용할 경우 자원의 구멍이 생길 수 있음(leak).<br/>
in을 반납할 때 오류가 생겨버리면 out은 반납을 못함.<br/><br/>


## 예외가 먹히는 상황
```java
public class BadBufferedReader extends BufferedReader {
    public BadBufferedReader(Reader in, int sz) {
        super(in, sz);
    }

    public BadBufferedReader(Reader in) {
        super(in);
    }

    @Override
    public String readLine() throws IOException {
        throw new CharConversionException();
    }

    @Override
    public void close() throws IOException {
        throw new StreamCorruptedException();
    }
}
```
```java
    static String firstLineOfFile(String path) throws IOException {
        BufferedReader br = new BadBufferedReader(new FileReader(path));
        try{
            return br.readLine();
        }finally {
            br.close();
        }
    }
```
readLine 과 close 둘다 오류가 떨어지는 상황에서 마지막에 발생한 오류만 보이게됨.
![image](https://user-images.githubusercontent.com/92290312/219684605-faeb53df-08e3-4520-ba29-307124ecd956.png)<br/>
하지만 try-with-resource로 변경하면 예외가 다 보임.<br/>
![image](https://user-images.githubusercontent.com/92290312/219685023-8bddd8fd-bd34-412f-8d38-2760018e787a.png)<br/><br/>


## 퍼즐러 예외 처리 코드 실수
```java
    static void copy(String src, String dst) throws IOException {
        InputStream in = new FileInputStream(src);
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        } finally {
            try {
                out.close();
            } catch (IOException e) {
                // TODO 이렇게 하면 되는거 아닌가?
            }
            try {
                in.close();
            } catch (IOException e) {
                // TODO 안전한가?
            }
        }
    }
```
마지막 close 도 try-catch로 감싸져있으면 괜찮지 않나? => catch로 잡는 예외 외의 다른 예외인 경우에 문제가 생김<br/>
오히려 자원 하나당 try로 잡는게 더 안전함.<br/><br/>


## try-with-resource
try-with-resource 코드는 어떻게 작동할까?
```java
    static String firstLineOfFile(String path) throws IOException {
        try(BufferedReader br = new BadBufferedReader(new FileReader(path))) {
            return br.readLine();
        }
    }
```
위의 코드를 컴파일 하면 아래와 같은 코드로 컴파일해줌(실제 바이트 코드는 다르게 생김)
```java
    static String firstLineOfFile(String path) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader(path));

        String var2;
        try {
            var2 = br.readLine();
        } catch (Throwable var5) {
            try {
                br.close();
            } catch (Throwable var4) {
                var5.addSuppressed(var4);
            }
            throw var5;
        }

        br.close();
        return var2;
    }
```
