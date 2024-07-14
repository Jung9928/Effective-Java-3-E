# Item 9. try-finally 보다는 try-with-resources를 사용하라

### 내용 요약 <br>
- 자바 라이브러리에는 `close` 메소드를 호출하여 직접 자원해제를 해줘야하는 경우가 있는데 <br>
  클라이언트가 자원해제를 놓치기 쉬워서 예측할 수 없는 어플리케이션 성능 문제가 발생할 수 있다. <br>
  이런 경우를 대비하기 위해 `try-finally`와 `try-with-resources` 구문을 사용하는데 <br>
  각각의 장/단점을 알아보자.

<br>

1. `try-finally`의 문제점
    - 코드가 길어지고 가독성이 떨어진다.
    - 여러 자원을 처리할 때, 코드가 복잡해진다. <br><br>

2. `try-with-resources`의 장점
   - 코드가 간결하고 가독성이 높다
   - 여러 자원을 처리할 때도 간편한다
   - 자원이 자동으로 해제된다
   - `AutoCloseable`인터페이스를 구현하는 자원에 한해서만 사용하면 된다
    
<br>

### 다중 자원을 `try-finally` 로 처리하는 예시 코드
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class TryFinallyExample {
    public static void main(String[] args) {
        BufferedReader br = null;
        
        try {
            br = new BufferedReader(new FileReader("test.txt"));
            String line;
            
            while((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            
            if(br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```
위 코드는 자원 해제를 위해 `finally` 블록을 사용하며, 코드가 복잡하고 가독성이 떨어진다.

<br><br>

### 단일 자원을 `try-with-resources`로 처리하는 예시 코드
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class TryWithResourcesExample {
    public static void main(String[] args) {
        
        try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
            String line;
            
            while((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
위 코드는 `try-with-resources` 구문을 사용하여 자원을 자동으로 해제한다. <br>
코드가 간결하고 가독성이 높다는 장점이 있다. <br>

<br><br>

### 다중 자원을 `try-with-resource`로 처리하는 예시 코드
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class MultipleResourcesExample {
    public static void main(String[] args) {
        
        try(BufferedReader br = new BufferedReader(new FileReader("input.txt"));
        FileWriter fw = new FileWriter("output.txt")) {
            
            String line;
            
            while((line = br.readLine()) != null) {
                fw.write(line + System.lineSeparator());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
위 코드에서 `BufferedReader`와 `FileWriter` 두 자원을 `try-with-resources` 구문을 사용하여 간편하게 처리했다.
<br><br>

### 정리하자면
- `try-finally`의 문제점 : 코드가 복잡해지고 가독성이 떨어지며 다중 자원 처리가 어렵다.
- `try-with-resources`의 장점 : 코드가 간결하고 가독성이 높으며 다중 자원을 간편하게 처리할 수 있다. 또한, 자원을 자동으로 해제한다.
- 사용 방법 : `AutoCloseable` 인터페이스를 구현하는 자원에 대해 `try-with-resources` 구문을 사용한다.

<br>
즉, 자원을 안전하고 명확하게 회수해야한다면 `try-with-resources` 를 사용하는 것을 권장한다.
