# Item 8. finalizer와 cleaner 사용을 피하라

### 내용 요약 <br>
1. finalizer 사용의 문제점
   - 예측 불가 : finalizer는 객체가 더 이상 사용되지 않을 때 호출되지만 정확히 언제 호출되는지 알 수 없다.
   - 성능 저하 : finalizer는 성능을 크게 저하시킬 수 있다. 가비지 컬렉션이 느려지고, 전체 프로그램 응답성이 떨어질 수 있다.
   - 신뢰성 문제 : finalizer가 실행되지 않을 수 있다. JVM이 비정상 종료되거나 finalizer 자체에 버그가 있을 경우, 실행되지 않습니다.
   - 보안 문제 : finalizer가 예기치 않게 실행되면 보안 상 문제가 발생할 수 있습니다.

<br>

2. cleaner 사용의 문제점
   - cleaner는 finalizer보다 덜 위험하지만, 여전히 예측 불가능하고 성능 저하를 일으킬 수 있다.

<br>

3. 대응 방안
   - try-with-resources : `AutoCloseable` 인터페이스를 구현하고 try-with-resources 구문을 사용하여 자원을 명시적으로 해제한다.
   - try-finally : `try-finally` 블록을 사용하여 자원을 명시적으로 해제한다.

<br>

### finalizer 사용의 문제점 예시 코드
```java
public class Resource {
    // finalizer 정의
    @Override
    protected void finalize() throws Throwable {
        try {
            // 자원 해제 코드
            System.out.println("Resource is being finalized");
        } finally {
            super.finalize();
        }
    }
}
```
* 위 코드는 예측 불가하게 실행되고, 성능 문제와 신뢰성 문제를 일으킬 수 있다.

<br><br>

### cleaner 사용의 문제점 예시 코드
```java
import java.lang.ref.Cleaner;

public class Resource {
    private static final Cleaner cleaner = Cleaner.create();
    
    // 제거할 객체
    private static class State implements Runnable {
        @Override
        public void run() {
            // 자원 해제 코드
            System.out.println("Resource is being cleaned.");
        }
    }
    
    private final State state;
    private final Cleaner.Cleanable cleanable;
    
    public Resource() {
        this.state = new State();
        this.cleanable = cleaner.register(this, state);
    }
}
```
* Cleaner도 예측 불가하게 실행되며, 성능 저하를 일으킬 수 있다.

<br><br>

### try-with-resources를 사용한 안전한 자원 해제 예시 코드
```java
public class Resource implements AutoCloseable {
    public Resource() {
        // 자원 할당 코드
        System.out.println("Resource acquired");
    }
    
    @Override
    public void close() {
        // 자원 해제 코드
        System.out.println("Resource released.");
    }
    
    public void doSomething() {
        System.out.println("Doing something with the resource.");
    }
    
    public static void main(String[] args) {
        try(Resource resource = new Resource()) {
            resource.doSomething();
        }   // try-with-resource 구문은 자동으로 close()를 호출하여 자원을 해제한다.
    }
}
```
* 위 코드는 자원을 명시적으로 해제하며, 자원 해제가 예측가능하고 안전하다.

<br><br>

### try-finally를 사용한 자원 해제 예시 코드
```java
public class Resource {
    public Resource() {
        // 자원 할당 코드
        System.out.println("Resource acquired.");
    }
    
    public void close() {
        // 자원 해제 코드
        System.out.println("Resource released.");
    }
    
    public void doSomething() {
        System.out.println("Doing something with the resource.");
    }
    
    public static void main(String[] args) {
        Resource resource = null;
        
        try {
            resource = new Resource();
            resource.doSomething();
        } finally {
            if(resource != null) {
                resource.close();
            }
        }
    }
}
```
* 위 코드도 자원을 명시적으로 해제하며, 자원 해제가 예측 가능하고 안전하다. <br><br>

### 정리하자면
- finalizer와 cleaner는 예측 불가성과 성능 저하 등의 이유로 사용을 피하자.
- 자원 해제를 위해 `try-with-resource` 또는 `try-finally`를 사용하여 자원을 명시적으로 해제하는 것이 바람직하다.
- `try-with-resource`는 `AutoCloseable` 인터페이스를 구현한 자원을 자동으로 해제할 수 있어 가장 권장되는 방법이다.




