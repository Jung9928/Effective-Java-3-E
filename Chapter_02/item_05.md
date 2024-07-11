# Item 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

- 내용 요약 : 객체 생성 시, 필요한 리소스를 직접 생성하면 결합도가 높아져 테스트 하기 어려워지고 유연성이 떨어진다
- 해결 방법 : 의존성 주입(DI)을 사용하여 객체가 필요한 자우너을 외부에서 주입받게 한다
- 장    점 <br>
  1) 유연성 증가 : 객체가 다양한 리소스와 함께 사용 가능해진다. <br>
  2) 테스트 용이성 증가 : 가짜 객체(mock Object)나 스텁(stub)을 주입하여 단위 테스트가 쉬워진다
  3) 재사용성 증가 : 자원을 여러 객체에 공유 가능해진다.

<br>

### ex_1) 정적 유틸리티를 잘못 사용한 예 -> 유연하지 않고 테스트하기 어려움
```Java
public class SpellChecker {
    private static final Lexicon dictionary = ...;
    
    private SpellChecker() {}   // 객체 생성 방지
    
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}
```

### ex_2) 싱글턴을 잘못 사용한 예 -> 유연하지 않고 테스트하기 어려움
```Java
public class SpellChecker {
    private static final Lexicon dictionary = ...;
    
    private SpellChecker() {}   // 객체 생성 방지
    public static SpellChecker INSTANCE = new SpellChecker(...);
    
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}
```
Lexicon 클래스 타입 단 하나만을 사용한다는 가정하에, <br> 
위 방식들은 'SpellChecker' 클래스가 'Lexicon' 클래스에 강하게 결합(SpellChecker 객체가 Lexicon 객체 라이프사이클을 관리)되어 있어 <br>
유연성이 떨어지고 테스트하기 어렵다.
<br><br>
즉, 'SpellChecker'는 항상 'Lexicon'의 특정 구현을 사용해야 하며, 변수 dictionary의 타입을 변경하고 싶다면 <br>
'SpellChecker' 클래스를 수정해야 한다. (유연성 부족) <br><br>

'Lexicon' 클래스가 외부 자원(ex: db, 파일, 네트워크 등)에 접근할 경우, 'SpellChecker'를 테스트하기 어려워 진다. <br>
mock 객체나 stub을 사용하고 싶어도 직접 객체를 생성하므로 이를 대체하기 어렵다. (테스트 용이성 저하)<br><br>

'SpellChecker'가 다양한 환경에서 재사용되기 어렵다. 예를 들어, Lexicon이 아닌 다른 클래스의 인스턴스를 사용해야 할 경우, <br>
매번 새로운 'SpellChecker' 클래스를 생성해야 한다. (재사용성 저하) <br><br>

### 개선 예) 의존 객체 주입을 사용한 경우
```Java
public class SpellChecker {
    private static final Lexicon dictionary;
    
    // Lexicon 객체를 외부에서 주입받도록 생성자를 정의
    // 1) 유연성 증가 : `SpellChecker`는 어떤 종류의 `Lexicon` 객체던 주입 받을 수 있음 -> 다양한 dictionary 사용 가능
    // 2) 테스트 용이성 증가 : 실제 `Lexicon` 대신 mock 객체나 stub 주입하여 테스트 수행 가능
    //                      -> 테스트 환경에서 외부 자원에 접근하지 않게 됨
    // 3) 재사용성 증가 : `SpellChecker` 는 다른 타입의 dictionary을 사용할 경우에도 코드 변경 필요가 없으므로
    //                   쉽게 재사용 가능. 
    private SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }
    
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}

// 개선된 SpellChecker 사용
public class Main {
    public static void main(String[] args) {
        Lexicon dictionary = new Lexicon();
        SpellChecker spellChecker = new SpellChecker(dictionary);
        
        System.out.println(spellChecker.isValid("example"));
    }
}

// 개선된 SpellChecker를 단위테스트에 사용
public class SpellCheckerTest {
    @Test
    public void testIsValid() {
        Lexicon mockDictionary = mock(Lexicon.class);
        when(mockDictionary.contains("example")).thenReturn(true);
        
        SpellChecker spellChecker = new SpellChecker(mockDictionary);
        assertTrue(spellChecker.isValid("example"));
    }
}
```
위 방식을 사용하면 `SpellChecker` 클래스는 이제 'Lexicon' 객체를 외부에서 주입받기 때문에 결합도가 낮아지고 테스트가 용이해진다.
<br><br>

### ※ TIP
- 클래스가 내부적으로 하나 이상의 리소스에 의존하고, 그 리소스가 클래스 동작에 영향을 준다면 <br>
  싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 이 리소스들을 클래스가 직접 만들게 해서도 안된다. <br>
  대신, 필요한 리소스 or 그 리소스를 생성하는 팩토리를 생성자 or 정적 팩토리나 builder에 넘겨주는 DI 방식을 적용하는게 좋다.


### TIP 예시 코드

#### 잘못된 예 ) 싱글턴과 정적 유틸리티 클래스 사용 및 직접 리소스 생성
```Java
public class SpellChecker {
    private final Lexicon dictionary = new Lexicon();       // 직접 리소스 생성
    
    private SpellChecker() {}     // 객체 생성 방지
    
    public static final SpellChecker INSTANCE = new SpellChecker();     // 싱글턴
    
    public boolean isValid(String word) {
        return dictionary.contains(word);
    }
}
```

<br><br>

#### 올바른 예 ) 의존성 주입 (DI) 사용
```Java
// 리소스 클래스
public class Lexicon {
    public boolean contains(String word) {
        return true;
    }
}

public class SpellChecker {
    private final Lexicon dictionary;
    
    // 생성자를 통한 의존성 주입
    private SpellChecker(Lexicon dictionary) {
        this.dictionary = dictionary;
    }
    
    public boolean isValid(String word) {
        return dictionary.contains(word);
    }
    
    // 정적 팩토리 메소드를 통한 의존성 주입
    public static SpellChecker withDictionary(Lexicon dictionary) {
        return new SpellChecker(dictionary);
    }
    
    // 빌더를 통한 의존성 주입
    public static class Builder {
        private Lexicon dictionary;
        
        public Builder dictionary(Lexicon dictionary) {
            this.dictionary = dictionary;
            return this;
        }
        
        public SpellChecker build() {
            return new SpellChecker(dictionary);
        }
    }
}

public class Main {
    public static void main(String[] args) {
        // 생성자를 통한 의존성 주입
        Lexicon dictionary = new Lexicon();
        SpellChecker spellChecker1 = new SpellChecker(dictionary);
        
        // 정적 팩토리 메소드를 통한 의존성 주입
        SpellChecker spellChecker2 = SpellChecker.withDictionary(dictionary);
        
        // 빌더를 통한 의존성 주입
        SpellChecker spellChecker3 = new SpellChecker().Builder()
                .dictionary(dictionary)
                .build();

        System.out.println(spellChecker1.isValid("example"));
        System.out.println(spellChecker2.isValid("example"));
        System.out.println(spellChecker3.isValid("example"));
    }
}
```




