# Item 70. 복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라

### 내용 요약 <br>
1. 검사 예외(Checked Exception)
    - `복구 가능한 상황`에서는 검사 예외를 사용한다. <br><br>
   
    - 메소드 호출자가 예외를 처리하여 문제를 해결할 수 있을 때, 검사 예외를 사용하는 것이 적합하다. <br>
      검사 예외는 호출자에게 `예외 처리`를 강제하며, 복구할 기회를 제공한다. <br><br>

    - 예를 들어, 파일을 읽거나 네트워크 연결을 시도할 때 발생하는 오류는 검사 예외로 처리할 수 있다. <br>
      호출자가 이러한 오류에 대해 적절히 대응하고 재시도하거나 대체 방안을 찾을 수 있기 때문이다. <br><br>

2. 런타임 예외(Runtime Exception)
    - `프로그래밍 오류`를 나타낼 때는 런타임 예외를 사용한다. <br><br>
    - 런타임 예외는 `논리적 오류`나 `코드 버그`에서 발생하며, 일반적으로 복구 불가능한 상황을 의미한다. <br>
      예를 들어, 배열의 잘못된 인덱스 접근이나 `null`참조 사용과 같은 문제이다. <br><br>
    - 이러한 예외는 호출자가 처리하는 것을 기대하지 않으며, 프로그램의 `개발 과정에서 수정`이 필요한 문제이다. <br><br>


3. 오류(Error)
    - 오류는 가상머신(VM)수준에서 발생하는 심각한 문제를 나타내며, 개발자가 처리할 수 없는 상황을 의미한다. <br>
      일반적으로 `OutOfMemoryError`나 `StackOverflowError`와 같은 경우이다. <br>
      오류는 이 항목에서 다루는 범위를 넘어서며, 주로 `시스템적인 문제`이다. <br><br>

4. 검사 예외와 런타임 예외를 구분하는 기준
    - 검사 예외는 `복구 가능`한 상황에 사용하고, 런타임 예외는 `프로그래밍 오류`로 복구할 수 없는 상황에 사용해야 한다. <br><br>


<br><br>


### 검사 예외(Checked Exception)를 사용하는 예시
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;

public class CheckedExceptionExample {
    
    public static void readFile(String filePath) throws FileNotFoundException {
        File file = new File(filePath);
        FileReader fileReader = new FileReader(file);           // 파일이 없을 경우 FileNotFoundException 발생
       
        // 파일 읽기 작업 수행
    }
    
    public static void main(String[] args) {
        try {
            readFile("non_existent_file.txt");
        } catch (FileNotFoundException e) {
            System.out.println("파일을 찾을 수 없다 : " + e.getMessage());
            
            // 복구 작업 수행 : 파일 경로 재입력 또는 기본 값 사용
        }
    }
}
```
위 코드에선 `FileNotFoundException`은 `검사 예외`이다. <br>
파일을 읽는 작업에서 파일이 존재하지 않을 경우 복구할 수 있기 때문에 검사 예외를 사용했다. <br><br>

호출자는 예외를 처리하여 파일 경로를 다시 입력받거나, 기본 파일을 사용하는 등의 방법으로 복구할 수 있다. <br><br>

<br><br>



### 런타임 예외(Runtime Exception)를 사용하는 예시
```java
public class RuntimeExceptionExample {
    
    public static int divide(int numerator, int denominator) {
        if(denominator == 0) {
            throw new ArithmeticException("0으로 나눌 수 없다.");
        }
        
        return numerator / denominator;
    }
    
    public static void main(String[] args) {
        try {
            System.out.println(divide(10, 0));
        } catch (ArithmeticException e) {
            System.out.println("에러 : " + e.getMessage());
            
            // 논리적 오류로 인해 프로그램이 실행되지 않음
        }
    }
}
```
위 코드에선 `ArithmeticException`은 `런타임 예외`이다. <br>
0으로 나누는 것은 논리적 오류이며, 이 오류는 개발 중에 코드를 수정해야 할 문제이다. <br><br>

이 예외는 호출자에게 복구할 기회를 주는 것이 아니라, `프로그래밍 오류`를 나타낸다. <br>
따라서, 런타임 예외로 처리하는 것이 적합하다.

<br><br>


### 정리하자면
1. `검사 예외(Checked Exception)`는 호출자가 `복구할 수 있는 상황`에서 사용하며, 이를 통해 프로그램이 `예외에 대응`할 수 있게 해야 한다.
2. `런타임 예외(Runtime Exception)`는 `복구할 수 없는 프로그래밍 오류`에서 사용하며, 이런 오류는 코드의 `논리적 결함`을 나타낸다. 
3. 올바르게 예외를 구분하여 사용하면 프로그램이 `가독성`, `유지보수성`, `안정성`을 높일 수 있다.
