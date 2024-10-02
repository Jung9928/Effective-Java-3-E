# Item 75. 예외의 상세 메시지에 실패 관련 정보를 담으라

### 내용 요약 <br>
1. 구체적이고 유용한 정보 포함
    - 예외가 발생한 상황과 실패의 원인을 `명확하게 설명`해야 한다. <br>
      예외 메시지에는 잘못된 인자, 시스템 상태, 발생 시점 등을 담아야 한다. <br><br>

2. `모든 관련 정보를 포함`
    - 예외 메시지는 문제가 발생한 `컨텍스트`를 포함해야 한다. <br>
      예를 들어, 메소드 호출 시, 인자값이 잘못되었다면 그 인자값을 예외 메시지에 포함시켜야 디버깅 시 쉽게 원인을 파악할 수 있다. <br><br>

3. 에러 원인 추적에 도움
    - 예외 메시지가 명확하고 구체적이면, 개발자는 문제 해결에 필요한 정보를 더 빨리 얻을 수 있어 `에러 원인`을 신속히 파악할 수 있다. <br><br>

    - 또한, 상위 계층에서 던진 예외가 더 상세한 정보와 함께 전달되면 전체적인 시스템 상태와 오류의 연관성을 파악하는 데 도움이 된다. <br><br>

4. 다양한 예외 상황에 대한 처리
    - 각 예외 상황에 맞는 `다양한 실패 정보`를 담는 것이 중요하다. <br>
      예를 들어, 파일을 찾을 수 없거나 접근 권한이 없는 경우에는 그 경로나 권한에 대한 정보를 포함해야 한다. <br><br>


<br><br>

### 잘못된 예외 메시지
```java
public class FileParser {
    
    public static void readFile(String filePath) throws FileNotFoundException {
        File file = new File(filePath);
        if(!file.exists()) {
            throw new FileNotFoundException("파일을 찾을 수 없다.");
        }
        
        // 파일 처리 코드...
    }
    
    public static void main(String[] args) {
        try {
            readFile("data.txt");
        } catch (FileNotFoundException e) {
            System.err.println(e.getMessage());
        }
    }
}
```
위 코드에선 예외 메시지는 단순히 "파일을 찾을 수 없다"라고만 설명하고 있어, 어떤 파일에서 문제가 발생했는 지 알 수 없다. <br>
만약, 시스템에서 여러 파일을 처리하는 경우라면 어떤 파일에서 문제가 발생했는지 파악하기 어려워질 수 있다. <br><br>

<br><br>



### 개선된 예외 메시지
```java
public class FileParser {
    
    public static void readFile(String filePath) throws FileNotFoundException {
        File file = new File(filePath);
        if(!file.exists()) {
            throw new FileNotFoundException("파일을 찾을 수 없다 : " + filePath);
        }
        
        // 파일 처리 코드...
    }
    
    public static void main(String[] args) {
        try {
            readFile("data.txt");
        } catch (FileNotFoundException e) {
            System.err.println(e.getMessage());
        }
    }
}
```
위 코드에선 예외 메시지에 `파일 경로(filePath)`를 포함시킴으로써 `어떤 파일이 문제인지` 명확히 알 수 있다. <br>
이렇게 구체적인 정보가 포함되면, 개발자는 파일 경로 오류를 더 쉽게 파악하고 문제를 해결할 수 있다. <br><br>

<br><br>



### 더 복잡한 예외 상황에서의 메시지
```java
public class Calculator {
    
    public static int divide(int numerator, int denominator) {
        if(denominator == 0) {
            throw new ArithmeticException("0으로 나눌 수 없다. 분자 : " + numerator + ", 분모 : " + denominator);
        }
        return numerator / denominator;
    }
    
    public static void main(String[] args) {
        try {
            System.out.println(divide(0, 0));
        } catch (ArithmeticException e) {
            System.err.println(e.getMessage());
        }
    }
}
```
위 코드에선 예외 메시지에 `분자(numerator)`와 `분모(denominator)`값을 포함시킴으로써, 나눗셈에서 오류가 발생했을 때 구체적인 문제를 쉽게 파악할 수 있게 한다. <br> 
만약 이 정보가 포함되지 않았다면, 0으로 나누기 오류는 알 수 있지만 어떤 값에서 문제가 발생했는지 알기 어렵다. <br><br>

<br><br>

### 정리하자면
1. `예외 메시지에는 실패와 관련된 구체적인 정보를 담아야`한다. <br>
   단순히 "파일을 찾을 수 없다"나 "오류 발생"같은 메시지보다는, 어떤 파일이 없는지, 어떤 인자가 잘못되었는지를 포함하는 메시지가 유용하다. <br><br>

2. 예외가 발생한 원인, 그 상황에 대한 구체적인 정보(ex : 잘못된 값, 상태)를 함께 제공하면, 디버깅 및 문제 해결 시간이 크게 단축된다. <br><br>

3. 예외 메시지에 관련 정보를 포함하는 것은 코드의 `가독성`과 `유지보수성`을 높여주는 중요한 방법이다. <br><br>


<br><br>





