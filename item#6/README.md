**목차**
1. [new String](#new-String)
2. [정규표현식](#정규표현식)
3. [Deprecation](#Deprecation)




# new String
JVM은 String을 풀(pool)에다가 캐싱을 하고 있다고 보면 됨.<br/>
=> 한번 만들어진 문자열을 재사용 할 때는 풀에서 꺼내서 씀. => new 를 사용하면 강제로 새로운 문자열을 만들기 때문에 불필요한 객체를 생성함.
![image](https://user-images.githubusercontent.com/92290312/218537958-f04b7f4e-a83f-4987-80ea-42287aab118a.png)
<br/>

# 정규표현식
정규표현식은 한번 만들때 CPU 메모리를 사용 하는 등 비용이 비쌈.<br/>
패턴을 문자열로 받고 컴파일하여 인스턴스를 만드는 과정이 오래걸림.<br/>
![image](https://user-images.githubusercontent.com/92290312/218538905-a9f9947a-6384-4668-b713-64fd9e389815.png)
<br/>
String의 matchs, split, replaceAll(replaceFirst) 에서 사용.<br/>
split의 경우에는 하나의 문자로 할때는 정규식을 사용하지 않는 것이 더 빠를 수도 있음.<br/>

# Deprecation
![image](https://user-images.githubusercontent.com/92290312/218543643-adab71b2-1256-48bb-8776-3bab2f215179.png)
<br/>
forRemoval : 더 강력하게 표시 <br/>
since : deprecate 한 버전 <br/>
javaDoc 으로 별도의 표시 가능
