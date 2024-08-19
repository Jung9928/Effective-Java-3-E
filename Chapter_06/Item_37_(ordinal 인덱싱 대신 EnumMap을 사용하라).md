# Item 37. ordinal 인덱싱 대신 EnumMap을 사용하라

### 내용 요약 <br>
1. `ordinal 인덱싱`의 문제점
    - 안정성 부족
        - `ordinal` 메소드는 열거형 상수의 선언 순서를 반환하지만, 이 값에 의존하여 배열 인덱싱을 하면 실수가 발생할 가능성이 크다.
          열거형의 순서가 변경되거나 상수가 추가될 때, 이러한 변경이 코드의 동작에 영향을 미칠 수 있다. <br><br>

    - 가독성 저하
        - `ordinal`을 사용한 배열 접근은 코드의 의도를 명확히 드러내지 못하며, 유지보수가 어려워진다. <br><br>

    - 타입 안전성 부족
        - `ordinal` 값은 단순한 정수이기 때문에 컴파일러가 잘못된 인덱싱을 잡아내지 못한다. <br><br>

2. `EnumMap`의 장점
    - 타입 안정성
        - `EnumMap`은 열거형 타입을 키로 사용하므로, 잘못된 키를 사용할 경우, 컴파일 타임에 오류를 잡아낼 수 있다. <br><br>

    - 가독성
        - `EnumMap`은 코드의 의미가 명확해지고 각 열거형 상수가 무엇을 나타내는 지 명확히 드러난다. <br><br>

    - 효율성
        - `EnumMap`은 내부적으로 배열을 사용해 매우 효율적이며, 메모리와 성능 측면에서 비슷한 효과를 얻을 수 있다. <br><br>

### `ordinal`을 사용한 배열 인덱싱
```java
public enum Phase {

   SOLID, LIQUID, GAS;
   
   public static final int TRANSITIONS[][] = {
           {0, 1, -1},          // SOLID
           {-1, 0, 2},          // LIQUID
           {-1, -1, 0}          // GAS
   };
   
   public static Phase transition(Phase from, Phase to) {
       return Phase.values()[TRANSITIONS[from.ordinal()][to.ordinal()]];
   }
}

// 사용 예
Phase nextPhase = Phase.transition(Phase.SOLID, Phase.LIQUID);
```
`TRANSITION` 배열을 사용하여 상 변화를 표현했는데 여기서 `ordinal()` 메소드를 사용하여 배열 인덱스를 얻고, 이를 통해 변화를 찾는다. <br>
이 방법은 열거형 상수의 순서가 바뀌거나 상수가 추가될 때 문제가 발생할 수 있다. <br><br>

<br><br>

### `EnumMap`을 사용한 경우
```java
import java.util.EnumMap;
import java.util.Map;

public enum Phase {

   SOLID, LIQUID, GAS;
   
   public enum Transition {
       MELT(SOLID, LIQUID),
       FREEZE(LIQUID, SOLID),
      BOIL(LIQUID, GAS),
      CONDENSE(GAS, LIQUID),
      SUBLIME(SOLD, GAS),
      DEPOSIT(GAS, SOLID),
      
      private final Phase from;
       private final Phase to;
       
       Transition(Phase from, Phase to) {
           this.from = from;
           this.to = to;
       }
       
      // Transition 맵을 생성한다.
      private static final Map<Phase, Map<Phase, Transition>> m = 
              new EnumMap<>(Phase.class);
       
       static {
           for(Phase p : Phase.values()) {
               m.put(p, new EnumMap<>(Phase.class));
           }
           
           for(Transition trans : Transition.values()) {
               m.get(trans.from).put(trans.to, trans);
           }
       }
       
       public static Transition from(Phase from, Phase to) {
           return m.get(from).get(to);
       }
   }
}

// 사용 예
Phase.Transition transition = Phase.Transition.from(Phase.SOLID, Phase.LIQUID);
```
`EnumMap`을 사용해 상변화(transition)을 관리한다. <br>
`EnumMap`은 `Phase` 열거형을 키로 사용하여 열거형 상수 간의 변화를 표현한다. <br> 
이 방법은 코드가 더 명확하고 확장하기 쉬우며, `ordinal` 인덱싱을 사용하는 것 보다 안전하다. <br><br>

<br><br>


### 정리하자면
`EnumMap`은 `ordinal`에 의존하는 방식보다 훨씬 더 명확하고 안전한 방법이다. 이를 사용함으로써, 코드의 가독성과 유지보수성이 향상되며 실수를 줄일 수 있다. <br>
또한, 열거형 타입을 키로 사용해 타입 안전성을 보장할 수 있다.<br>


