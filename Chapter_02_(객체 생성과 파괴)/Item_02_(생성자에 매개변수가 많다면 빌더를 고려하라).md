# Item 02. 생성자에 매개변수가 많다면 빌더를 고려하라

### 내용 요약 <br>
1. 점층적 생성자 패턴의 단점
    - 매개변수가 적을 때는 생성자 사용이 간편하지만, `매개변수가 많아지면` 매우 복잡해질 수 있다. <br><br>

    - 점층적 생성자 패턴은 모든 조합의 생성자를 제공해야 하므로, 매개변수가 많을수록 `코드 가독성이 떨어지고 실수할 가능성`이 높아진다. <br><br>


2. 자바빈즈 패턴의 단점
    - 자바빈즈 패턴은 `매개변수를 설정할 수 있는 setter 메소드를 제공`하지만, 이 패턴은 `객체의 일관성을 보장할 수 없으며` <br>
      객체가 완전히 생성되기 전에 불완전한 상태일 수 있다. <br>
      또한, `불변성`을 유지할 수 없다. <br><br>


3. 빌더 패턴의 장점
    - 빌더 패턴은 `가독성이 좋고 안정적`이다. <br>
      특히, `필수 매개변수와 선택 매개변수`를 명확히 구분할 수 있으며, 가변적인 매개변수를 가진 객체를 쉽게 생성할 수 있다. <br><br>

    - 빌더 패턴은 객체를 생성하기 전에 `모든 매개변수를 명시적으로 설정`할 수 있어 객체의 `불변성`을 유지하면서도 `유연한 설정`이 가능하다. <br><br>

    - 메소드 체이닝을 사용하여 `코드의 가독성`을 높일 수 있다. <br><br>

4. 안전성 
    - 매개변수의 타입이나 순서에 대한 `실수 가능성이 줄어들고` 각 매개변수에 대해 명시적으로 메소드를 호출함으로써 `코드의 안전성이 증가`한다. <br><br>


<br><br>


### 점층적 생성자 패턴의 예시
```java
import java.io.*;

public class NutritionFacts {

    private final int servingSize;  // (mL)  필수
    private final int servings;     // (회)  필수
    private final int calories;     // (kcal) 선택
    private final int fat;          // (g)    선택
    private final int sodium;       // (mg)   선택
    private final int carbohydrate; // (g)    선택
   
    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }
    
    
    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }
    
    
    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
      this.servingSize = servingSize;
      this.servings = servings;
      this.calories = calories;
      this.fat = fat;
      this.sodium = sodium;
      this.carbohydrate = carbohydrate;
   }
}
```
위 코드에서 이 코드는 `점층적 생성자 패턴`을 사용한 예시로, 매개변수의 수가 늘어날수록 생성자 수가 증가하고 <br>
가독성이 떨어지며 `유지보수`가 어렵다. 

<br><br>


### 빌더 패턴의 예시
```java
import java.io.*;

public class NutritionFacts {

   private final int servingSize;  // (mL)  필수
   private final int servings;     // (회)  필수
   private final int calories;     // (kcal) 선택
   private final int fat;          // (g)    선택
   private final int sodium;       // (mg)   선택
   private final int carbohydrate; // (g)    선택

   // 빌더 클래스
   public static class Builder {
      // 필수 매개변수
      private final int servingSize;
      private final int servings;

      // 선택 매개변수 - 기본값으로 초기화
      private int calories = 0;
      private int fat = 0;
      private int sodium = 0;
      private int carbohydrate = 0;

      // 생성자: 필수 매개변수를 초기화
      public Builder(int servingSize, int servings) {
         this.servingSize = servingSize;
         this.servings = servings;
      }

      // 선택 매개변수들을 설정하는 메소드
      public Builder calories(int val) {
         calories = val;
         return this;
      }

      public Builder fat(int val) {
         fat = val;
         return this;
      }

      public Builder sodium(int val) {
         sodium = val;
         return this;
      }

      public Builder carbohydrate(int val) {
         carbohydrate = val;
         return this;
      }

      // 빌더를 사용해 NutritionFacts 객체 생성
      public NutritionFacts build() {
         return new NutritionFacts(this);
      }
   }

   // 빌더에서 값을 받아 필드 초기화
   private NutritionFacts(Builder builder) {
      servingSize = builder.servingSize;
      servings = builder.servings;
      calories = builder.calories;
      fat = builder.fat;
      sodium = builder.sodium;
      carbohydrate = builder.carbohydrate;
   }

   @Override
   public String toString() {
      return "NutritionFacts{" +
              "servingSize=" + servingSize +
              ", servings=" + servings +
              ", calories=" + calories +
              ", fat=" + fat +
              ", sodium=" + sodium +
              ", carbohydrate=" + carbohydrate +
              '}';
   }
}

public class Main {
   public static void main(String[] args) {
      NutritionFacts coke = new NutritionFacts.Builder(240, 8)
              .calories(100)
              .sodium(35)
              .carbohydrate(27)
              .build();
      System.out.println(coke);
   }
}
```
빌더 패턴을 사용하면 `필수 매개변수`인 `servingSize`와 `servings`는 빌더의 생성자에서 설정되고, `선택 매개변수`는 필요에 따라 설정할 수 있다. <br><br>

객체를 생성할 때, 어떤 값이 설정되고 어떤 값이 기본값으로 유지되는지 `가독성이 명확`해진다. <br><br>

`메소드 체이닝`을 사용하여 여러 매개변수를 설정하면서도 `코드가 간결하고 직관적`이다. <br><br>

<br><br>

### 빌더 패턴의 장점 요약
1. 가독성 : 메소드 체이닝을 통해 `가독성이 높아지고` 매개변수가 명확하게 구분된다. 
2. 유연성 : 필수 매개변수와 선택 매개변수를 구분하여 객체 생성의 `유연성을 높인다`.
3. 불변성 : 객체를 생성한 후에는 `수정할 수 없는 불변 객체`를 만들 수 있다
4. 안전성 : 각 매개변수에 대해 명시적인 설정이 필요하므로, 잘못된 매개변수 순서나 누락에 대한 `실수 가능성을 줄인다`

<br><br>

### 정리하자면
1. `점층적 생성자 패턴`과 `자바빈즈 패턴`의 한계를 극복하면서 `매개변수가 많고 복잡한 객체를 생성할 때` 빌더 패턴이 적합하다
2. 빌더 패턴은 특히 `대규모 객체 생성 시의 유연성과 안전성을 유지`하면서도 `가독성 있는 코드작성`을 돕는다

