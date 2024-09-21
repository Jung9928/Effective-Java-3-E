# Item 63. 문자열 연결은 느리니 주의하라

### 내용 요약 <br>
1. `문자열은 불변 객체`
    - `String`객체는 `불변(immutable)`이므로, 문자열을 연결할 때마다 새로운 `String`객체가 생성된다. <br>
      이 과정에서 이전 문자열의 값을 복사하고, 새로운 객체를 생성하는데 많은 비용이 든다. <br><br>

2. `+` 연산자를 반복해서 사용하면 성능 저하
    - 특히, 루프 안에서 문자열 연결을 `+`연산자로 하면 성능이 크게 저하될 수 있다. <br>
      각 반복에서 새로운 `String`객체가 생성되기 때문에 반복횟수가 늘어나면 성능이 기하급수적으로 떨어진다. <br><br>

3. 해결책 : `StringBuilder` 사용
    - `StringBuilder`는 `가변(mutable)`객체로, 문자열을 더할 때마다 새로운 객체를 생성하지 않고, 내부 버퍼에 문자열을 추가한다. <br>
      따라서, 성능이 매우 효율적이다.

4. `StringBuffer`는 `StringBuilder`와 비슷하지만
    - `StringBuffer`는 `Thread-safe`한 버전이다. 다중 스레드 환경에서는 `StringBuffer`를 사용하고 <br>
      단일 스레드 환경에서는 성능이 더 빠른 `StringBuilder`를 사용해야 한다. <br><br>


<br><br>


### 잘못된 예시 : `+`연산자를 사용한 문자열 연결
```java
public class Item63Example {
    
    public static String concatenateStrings() {
        String result = "";
        
        for(int i=0; i<1000; i++) {
            result += "a";              // 매번 새로운 String 객체 생성
        }
        
        return result;
    }
    
    public static void main(String[] args) {
        
        long start = System.currentTimeMills();
        System.out.println(concatenateStrings());
        
        long end = System.currentTimeMills();
        System.out.println("Time taken :" + (end - start) + "ms");
    }
}
```
위 코드에선 `+`연산자를 사용해 문자열을 반복해서 연결한다. 각 반복에서 새로운 `String`객체가 생성되고 <br>
기존 문자열을 모두 복사하여 새로운 객체를 만들어낸다. <br>
이로 인해, 매우 비효율적이다. 1000번 반복할 때마다 성능이 급격히 저하될 수 있다.

<br><br>


### 올바른 예시 : `StringBuilder` 사용
```java
public class Item63Example {
    
    public static String concatenateStrings() {
        StringBuilder result = new StringBuilder();                     // StringBuilder 사용
        
        for(int i=0; i<1000; i++) {
            result.append("a");                                         // 기존 객체에 문자열 추가
        }
        
        return result.toString();
    }
    
    public static void main(String[] args) {
        
        long start = System.currentTimeMills();
        System.out.println(concatenateStrings());
        
        long end = System.currentTimeMills();
        System.out.println("Time taken :" + (end - start) + "ms");
    }
}
```
위 코드에선 `StringBuilder`를 사용하여 문자열을 효율적으로 연결한다. 새로운 `String`객체를 생성하는 대신 <br>
`내부 버퍼에 문자열을 추가`하기 때문에 성능이 크게 향상된다. <br>
반복 횟수가 많아질수록 차이가 더 두드러진다. 

<br><br>



### 성능 비교 : `+` 연산자 vs `StringBuilder`
```java
public class Item63Example {
    
    public static void main(String[] args) {
        
        // + 연산자 사용
       long start1 = System.currentTimeMillis();
       String result1 = "";
       
       for(int i=0; i<10000; i++) {
           result1 += "a";
       }
       
       long end1 = System.currentTimeMillis();
       System.out.println("Using + operator : " + (end1 - start1) + "ms");
       
       // StringBuilder 사용
       long start2 = System.currentTimeMillis();
       StringBuilder result2 = new StringBuilder();
       for(int i=0; i<10000; i++) {
           result2.append("a");
       }
       
       long end2 = System.currentTimeMillis();
       System.out.println("Using StringBuilder : " + (end2 - start2) + "ms");
    }
}
```
위 코드에선 `+` 연산자와 `StringBuilder`의 성능을 비교한다. `+` 연산자를 사용하면 시간이 훨씬 더 오래 걸리지만 <br>
`StringBuilder`를 사용하면 성능이 훨씬 빠르다. 반복이 많아질수록 성능 차이가 더 커진다. <br><br>

<br><br>

### 정리하자면
1. 문자열 연결 시, `+`를 반복해서 사용하면 성능이 크게 저하된다.
2. 반복적인 문자열 연결 시, `StringBuilder`를 사용하는 것이 더 효율적이다. 멀티스레드 환경에서는 `StringBuffer`를 사용할 수 있지만, 단일 스레드 환경에서는 `StringBuilder`가 더 적합하다.
3. 성능 최적화를 위해, 루프에서 문자열을 결합하는 경우에는 항상 `StringBuilder`를 사용하는 것이 더 좋다.




