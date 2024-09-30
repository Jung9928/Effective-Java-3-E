# Item 73. 추상화 수준에 맞는 예외를 던져라

### 내용 요약 <br>
1. 추상화의 일관성
    - API를 설계하거나 코드 작성 시, 예외는 `해당 계층의 추상화 수준`에 맞는 것이어야 한다. <br>
      저수준의 예외를 그대로 상위 계층에 전달하면, 상위 계층에서 저수준의 세부사항을 알아야 하므로 `추상화의 일관성이 깨질 수 있다` <br><br>

    - 상위 계층에서는 저수준 구현 세부 사항에 의존하지 않아야 하며, 예외도 마찬가지이다. <br>
      예를 들어, 파일 시스템 관련 예외를 추상화한 계층에서는 파일 시스템의 에외를 그대로 노출하기보다, 해당 계층에 적합한 예외로 변환해야 한다. <br><br>

2. 저수준 예외를 변환하는 기법
    - `예외 번역(Exception Translation)`
        - 저수준의 예외를 캡처하고, 해당 계층에 적합한 `새로운 예외로 변환`하는 방법이다. <br>
          예를 들어, 데이터베이스와 관련된 예외를 더 추상적인 서비스 계층에서는 `DatabaseException`이 아닌 `ServiceException`과 같은 새로운 예외로 변환한다. <br><br>

    - `예외 연쇄(Exception Chaining)`
        - 새로운 예외를 던지면서 원래 발생한 예외를 포함하여, 디버깅 시 예외의 원인을 추적할 수 있게 한다. <br>
          자바에서는 예외 생성자에 원래의 예외를 인수로 전달하여 이를 구현할 수 있다. <br><br>

3. 상위 계층의 요구를 충족하는 예외 처리
   - 상위 계층의 호출자가 처리할 수 없는 예외는 적절히 변환해야 한다. <br>
     예를 들어, DB 접근 로직에서 발생하는 예외는 상위 계층에서는 이해할 수 있는 더 높은 수준의 예외로 바꿔야 한다.

4. 구체적인 예외 사용을 지양
    - 일반적으로 `구체적인 저수준 예외`는 상위 계층에서 불필요한 구현 세부사항을 노출하므로 피해야 한다. <br>
      `하위 계층의 세부사항을 숨기는 것이 추상화의 핵심`이다. <br><br>

      
<br><br>



### 잘못된 예외 처리(저수준 예외 그대로 전달)
```java
public class FileParser {
    
    public String readFile(String filePath) throws FileNotFoundException {
        File file = new File(filePath);
        Scanner scanner = new Scanner(file);
        return scanner.nextLine();
    }
}

public class Main {
    
    public static void main(String[] args) {
        FileParser parser = new FileParser();
        try {
            parser.readFile("data.txt");
        }
        catch (FileNotFoundException e) {
            System.out.println("파일을 찾을 수 없다 : " + e.getMessage());
        }
    }
}
```
위 코드에선 `FileNotFoundException`은 저수준 파일 I/O 예외이다. <br>
이 예외를 상위 계층으로 그대로 던지면, 파일 시스템에 대한 세부 구현 사항이 노출된다. <br>
상위 계층은 파일의 존재 여부보다 `데이터를 읽는 문제`를 다루는 것이 더 적절하다. <br><br>

<br><br>




### 올바른 예외 처리(추상화 수준에 맞춘 예외 번역)
```java
public class FileParser {
    
    public String readFile(String filePath) throws FileNotFoundException {
        try {
            File file = new File(filePath);
            Scanner scanner = new Scanner(file);
            return scanner.nextLine();    
        }
        catch (FileNotFoundException e) {
            throw new IllegalStateException("파일을 읽을 수 없다 : " + filePath, e);
        }
    }
}

public class Main {
    
    public static void main(String[] args) {
        FileParser parser = new FileParser();
        try {
            parser.readFile("data.txt");
        }
        catch (IllegalStateException e) {
            System.out.println(e.getMessage());
        }
    }
}
```
위 코드에선 저수준의 `FileNotFoundException`을 상위 계층에 맞춘 `IllegalStateException`으로 변환했다. <br>
이렇게 하면 상위 계층에서는 더 추상적인 예외를 처리하게 되어 코드가 깔끔해지고 유지보수성이 높아진다. <br><br>

예외 연쇄를 통해 원래 예외 `FileNotFoundException`를 포함시킴으로써 디버깅 시에도 원인을 추적할 수 있다. <br>


<br><br>


### 정리하자면
1. `추상화 수준에 맞는 예외`를 던지면 상위 계층에서 저수준 세부사항에 의존하지 않고도 문제를 처리할 수 있다. <br>
   이는 코드의 `유연성`과 `유지보수성`을 높인다. <br><br>


2. `예외 번역`과 `예외 연쇄`는 저수준 예외를 상위 계층에서 적절하게 처리할 수 있도록 도와주는 중요한 기법이다. <br>
   이를 통해 API 설계자는 각 계층의 역할을 명확히 유지할 수 있다. <br><br>

3. 항상 상위 계층의 호출자가 이해할 수 있는 방식으로 예외를 처리하고, 예외가 적절하게 변환되도록 `신중하게 설계`하는 것이 중요하다. <br><br>
