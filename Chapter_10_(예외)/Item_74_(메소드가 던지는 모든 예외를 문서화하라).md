# Item 74. 메소드가 던지는 모든 예외를 문서화하라

### 내용 요약 <br>
1. 모든 예외를 문서화
    - 메소드가 던질 수 있는 `체크 예외`뿐만 아니라 `런타임 예외`도 문서화해야 한다. <br>
      예외가 문서화되지 않으면 메소드 사용자는 어떤 예외가 발생할 지 알 수 없고, 그에 따라 코드를 적절히 작성하기 어렵다. <br><br>

2. `@throws`태그 사용
   - 자바독(JavaDoc)의 `@throws` 태그를 사용하여 메소드가 던질 수 있는 예외를 명시적으로 문서화한다. <br>
     이로 인해 API 사용자들은 메소드가 던질 수 있는 예외를 사전에 파악할 수 있어, 예외 상황에 대해 적절히 대비할 수 있다. <br><br>

3. 필수 예외 문서화와 선택적 예외 문서화
    - `필수 예외 문서화`
        - 체크 예외는 반드시 문서화해야 한다. 체크 예외는 호출자가 반드시 처리해야 하므로 문서화를 통해 예외 처리의 필요성을 알려야 한다. <br><br>

    - `선택적 예외 문서화`
        - 런타임 예외는 체크 예외보다 자유롭지만, 특별한 이유가 있는 경우 문서화하는 것이 좋다. <br>
          런타임 예외는 문서화하지 않아도 되지만, 만약 런타임 예외가 발생할 수 있는 특별한 상황이라면 이를 문서화하여 호출자가 이해할 수 있도록 해야 한다. <br><br>

4. 예외에 대한 명확한 설명
    - 예외를 던지는 이유와 발생 조건에 대한 `명확한 설명`이 중요하다. <br>
      예외 메세지나 문서화를 통해 예외가 발생하는 구체적인 상황을 설명해야 사용자가 적절한 방식으로 예외를 처리할 수 있다. <br><br>

5. 체크 예외와 런타임 예외의 구분
    - 체크 예외는 예측 가능한 예외 상황에 대해 호출자가 `반드시 처리해야 할 경우`에 사용되며 <br>
      런타임 예외는 주로 `프로그래밍 오류`나 예측할 수 없는 예외 상황에 사용된다. <br>
      따라서 두 예외를 구분하고 문서화할 때도 각 예외의 성격을 고려해야 한다. <br><br>


<br><br>


### 예외를 문서화한 메소드의 예시
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

/**
 * 파일을 읽어서 내용을 반환하는 유틸리티 클래스
 * */

public class FileUtil {
    
   /**
    * 주어진 파일 경로에서 첫 번째 줄을 읽어 반환한다.
    * 
    * @param filePath 읽을 파일의 경로
    * @return 파일의 첫 번째 줄
    * @throws FileNotFoundException 파일이 존재하지 않을 때 발생한다.
    * @throws IllegalArgumentException 파일 경로가 null일 때 발생한다.
    * */
    
    public static String readFirstLine(String filePath) throws FileNotFoundException {
        if(filePath == null) {
            throw new IllegalArgumentException("파일 경로는 null일 수 없다.");
        }
        
        File file = new File(filePath);
        Scanner scanner = new Scanner(file);
        return scanner.nextLine();
    }
    
    public static void main(String[] args) {
        try {
            String firstLine = FileUtil.readFirstLine("data.txt");
            System.out.println(firstLine);
        } catch (FileNotFoundException e) {
            System.err.println("파일을 찾을 수 없다 : " + e.getMessage());
        } catch (IllegalArgumentException e) {
            System.err.println("잘못된 인자 : " + e.getMessage());
        }
    }
}
```
위 코드에선 메소드가 던질 수 있는 `체크 예외(FileNotFoundException)`와 `런타임 예외(IllegalArgumentException)`을 명시적으로 문서화했다. <br>
`@throws` 태그를 통해 각 예외가 어떤 상황에서 발생하는 지 상세히 설명한다. <br>
사용자는 이 메소드를 호출할 때, `FileNotFoundException`이 발생할 수 있다는 것을 알 수 있고, 이를 적절히 처리해야 한다. <br><br>

<br><br>

### 주요 예외 상황에 대한 문서화 전략
1. 체크 예외에 대한 문서화
   - 체크 예외는 호출자가 반드시 처리해야 하기 때문에, `반ㄷ싀 문서화`해야 한다. <br>
   - `FileNotFoundException`은 파일이 존재하지 않을 때 발생하므로, 이 예외가 메소드에서 던져질 수 있다는 점을 명확하게 문서화해야 한다. <br><br>

2. 런타임 예외에 대한 문서화
   - 런타임 예외는 선택적이지만, 예외 발생 가능성이 높거나 예상되는 경우 `문서화하는 것이 바람직`하다. <br>
   - `IllegalArgumentException`은 메소드 인수가 부적절할 때 발생할 수 있는 예외로, 명시적으로 문서화하여 호출자가 예상할 수 있도록 한다. <br><br>



### 정리하자면
1. 메소드가 던질 수 있는 `모든 예외`를 철저히 문서화하는 것은 API의 사용성을 높이고 개발자들 간의 의사소통을 원활하게 한다. <br>
   또한, 예외 처리 과정에서 발생할 수 있는 혼란을 줄이고, 코드를 더 `신뢰성 있게 유지`할 수 있도록 돕는다. <br><br>

2. 자바독의 `@throws`태그를 사용하여 각 예외의 발생 원인과 상황을 상세히 설명하는 것이 중요하며 <br>
   이를 통해 API 사용자는 메소드의 동작을 더 잘 이해하고, 예외 처리 코드를 작성할 수 있다. <br><br>
