# Item 36. 비트 필드 대신 EnumSet을 사용하라

### 내용 요약 <br>
1. `비트 필드`의 문제점
    - 가독성 및 유지보수성 부족 
      - 비트 필드는 숫자로 표현되기 때문에 코드의 의미를 파악하기 어렵고 추가적인 정보나 설명 없이는 이해하기 힘들다. <br><br>

    - 타입 안정성 부족
      - 비트 필드는 정수로 처리되므로, 컴파일러가 잘못된 값을 잡아내지 못하고 실수를 유발할 수 있다. <br><br>

    - 확장성 제한
      - 새로운 상수를 추가하거나 변경할 때 복잡한 비트 연산을 처리해야 하며, 실수를 하기 쉽다. <br><br>

2. `EnumSet`의 장점
    - 가독성
        - `EnumSet`은 열거형 상수를 사용하므로 코드의 의미가 명확하고, 값들이 잘 드러난다. <br><br>

    - 타입 안정성
        - `EnumSet`은 열거형 타입만 허용하므로 컴파일 타임에 타입 안전성을 보장한다. <br><br>

    - 효율성
      - `EnumSet`은 내부적으로 비트 벡터로 구현되어 있어 메모리 효율적이고 성능도 우수하다. <br><br>
      
    - 편리한 API
      - `EnumSet`은 비트 필드보다 사용하기 쉬운 API를 제공한다. <br><br>


### `비트 필드`를 사용하는 경우
```java
public class Text {

   public static final int STYLE_BOLD               = 1 << 0;       // 1
   public static final int STYLE_ITALIC             = 1 << 1;       // 2
   public static final int STYLE_UNDERLINE          = 1 << 2;       // 4
   public static final int STYLE_STRIKETHROUGH      = 1 << 3;       // 8
   
   // 매개변수 styles는 OR(|)로 결합된 비트필드들이다.
   public void applyStyles(int styles) {
       // 스타일 적용 로직
   }
}

// 사용 예
Text text = new Text();
text.applyStyles(Text.STYLE_B0LD | Text.STYLE_ITALIC);
```
`비트 필드`는 상수들을 `1`, `2`, `4`, `8` 처럼 2의 제곱수로 정의하고, 이를 OR(`|`) 연산자로 결합하여 여러 가지 옵션을 표현한다. <br><br>
그러나, 이 방법은 코드의 의미를 파악하기 어렵게 만든다. <br>
또한, 잘못된 상수를 사용해도 컴파일 타임에 잡아내지 못할 가능성이 크다. <br>

<br><br>



### `EnumSet`를 사용하는 경우
```java
import java.util.EnumSet;

public class Text {

   public enum Style {BOLD, ITALIC, UNDERLINE, STRIKETHROUGH}
   
   public void applyStyles(EnumSet<Style> styles) {
       // 스타일 적용 로직
   }
}

// 사용 예
Text text = new Text();
text.applyStyles(EnumSet.of(Text.Style.BOLD, Text.Style.ITALIC));
```
`EnumSet`은 특정 열거형 타입의 모든 상수를 집합으로 다룬다. `EnumSet.of()` 메소드를 사용하여 여러 스타일을 쉽게 결합할 수 있으며, 코드의 의미가 명확해진다. <br><br>
`EnumSet`은 비트 필드의 효율성을 유지하면서도, 가독성과 타입 안정성을 제공하는 훨씬 더 나은 방법이다. <br><br>
또한, 코드가 직관적이고 명확해지며, 잘못된 조합을 컴파일 타임에 잡아낼 수 있어 안전하다. <br>
이는 복잡한 비트 연산의 사용을 피하면서 성능과 메모리 효율성에서 이점을 유지할 수 있다.

<br><br>

