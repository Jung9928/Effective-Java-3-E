# Item 38. 확장할 수 있는 열거타입이 필요하면 인터페이스를 사용하라

### 내용 요약 <br>
1. `열거형`의 제한
    - 열거형은 상속을 지원하지 않으며, 열거형 타입은 일반적으로 상수 집합으로 고정되어 있다. <br>
      따라서, 새로운 상수를 추가하거나 열거형을 확장하는 것이 불가능하다. <br><br>

    - `인터페이스` 사용
        - 열거형이 특정 인터페이스를 구현하도록 설계하면, 그 인터페이스를 통해 열거형을 확장할 수 있다. <br>
          이렇게 하면, 다양한 열거형이 동일한 인터페이스를 구현할 수 있으며, 열거형끼리의 호환성을 유지하면서 확장이 가능하다. <br><br>
        - 인터페이스를 통해 열거형을 확장하면, 열거형의 인스턴스가 다른 열거형의 인스턴스와 동일한 타입으로 처리될 수 있어 유연성이 크게 향상된다. <br><br>
      
    - `전략 패턴`과 결합
        - 이 기법은 전략 패턴과 결합하여 열거형의 각 상수가 특정 전략(동작)을 구현하도록 할 수 있다. <br>
          이를 통해 공통된 동작을 정의하고, 열거형의 각 인스턴스에서 해당 동작을 다르게 구현할 수 있다.  <br><br>


### `인터페이스`를 사용하여 열거형 확장하기
```java
public interface Operation {
    double apply(double x, double y);
}

// 기본 연산 열거형
public enum BasicOperation implements Operation {

    PLUS("+") {
        public double apply(double x, double y) {return x + y;}
    },
    MINUS("-") {
        public double apply(double x, double y) {return x - y;}
    },
    TIMES("*") {
        public double apply(double x, double y) {return x * y;}
    },
    DIVIDE("/") {
        public double apply(double x, double y) {return x / y;}
    };
    
    private final String symbol;
    
    BasicOperation(String symbol) {
        this.symbol = symbol;
    }
    
    @Override
    public String toString() {
        return symbol;
    }
}

// 확장된 연산 열거형
public enum ExtendedOperation implements Operation {
    EXP("^") {
        public double apply(double x, double y) {return Math.pow(x, y);}
    },

    REMAINDER("%") {
        public double apply(double x, double y) {return x % y;}
    };
    
    private final String symbol;
    
    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }
    
    @Override
    public String toString() {
        return symbol;
    }
}


// 사용 예
public class Main {
    public static void main(String[] args) {
        double x = 2;
        double y = 4;
        
        for(Operation op : BasicOperation.values()) {
            System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
        }

        for(Operation op : ExtendedOperation.values()) {
            System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
        }
    }
}
```
인터페이스 `Operation` : `Operation` 인터페이스는 2개의 double 값을 받아 연산을 수행하는 `apply` 메소드를 정의했고 <br>
이를 통해 여러 열거형이 이 인터페이스를 구현하도록 할 수 있다. <br><br>

기본 연산을 정의하는 `BasicOperation`과 열거형인 `BasicOperation`은 `PLUS`, `MINUS`, `TIMES`, `DIVIDE`와 같은 기본 산술 연산을 구현한다. <br>
각 열거형 상수는 `apply` 메소드르 통해 자신만의 연산을 정의한다. <br><br>

확장된 연산을 정의하는 `ExtendedOperation`과 열거형인 `ExtendedOperation`은 `EXP`, `REMAINDER`와 같은 확장된 연산을 정의한다. <br>
이 열거형도 `Operation` 인터페이스를 구현하여 동일한 방식으로 처리될 수 있다. <br><br>


<br>

### 정리하자면
열거형을 상속하지 않고도 확장할 수 있으며 코드의 유연성과 재사용성을 높일 수 있다.<br>
위 방식은, 다양한 열거형이 공통된 인터페이스를 구현하도록 하여, 서로 다른 열거형을 유사하게 다룰 수 있도록 하는데 유용하다. <br>

