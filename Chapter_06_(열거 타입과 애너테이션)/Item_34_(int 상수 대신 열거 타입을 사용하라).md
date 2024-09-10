# Item 34. int 상수 대신 열거 타입을 사용하라

### 내용 요약 <br>
1. `정수 상수`사용의 문제점
    - `정수 상수`를 사용하여 상태나 유형을 표현하는 경우, 코드의 가독성이 떨어지고 오류가 발생할 가능성이 높다. <br>
      예를들어, 상수를 잘못된 값으로 초기화하거나 혼동할 수 있다. <br><br>
   
    - `정수 상수`는 의미를 명확하게 전달하지 않으며, 코드에서 사용되는 곳마다 상수의 의미를 기억해야 한다. <br><br>

    - 타입 안전을 보장할 방법이 없다. <br><br><br>

2. `열거`타입의 장점
    - 타입 안정성
        - 특정 집합의 값만을 허용하므로, 잘못된 값이 들어가는 것을 방지한다. <br><br>

    - 가독성
        - 코드의 의도를 명확하게 표현하므로, 읽는 사람이 코드의 의미를 이해하기 쉬워진다. <br><br>
      
    - 유지보수성
      - 열거 타입은 새로운 값이 추가되거나 기존 값이 변경될 때, 컴파일 타임에 오류를 발생시켜서 문제를 조기에 발견할 수 있다.

### `정수 상수` 사용 예 1)
```java

public class Operation {
    
    public static final int PLUS = 1;
    public static final int MINUS = 2;
    public static final int TIMES = 3;
    public static final int DIVIDE = 4;
    
    public int apply(int x, int y, int op) {
        switch(op) {
            case PLUS : return x + y;
            case PLUS : return x - y;
            case PLUS : return x * y;
            case PLUS : return x / y;
            default: throw new IllegalArgumentException("Invalid operation code");
        }
    }
}
```
상수를 정수로 정의하고 switch문을 사용하여 처리한다. 이 경우, 코드의 의미가 명확하지 않고 잘못된 정수 값을 사용할 위험이 있다. <br><br>

<br><br>

### `열거 타입` 사용 예 1)
```java

public enum Operation {
    
    PLUS {
        @Override
        public int apply(int x, int y) {
            return x + y;
        }
    },

    MINUS {
        @Override
        public int apply(int x, int y) {
            return x - y;
        }
    },

    TIMES {
        @Override
        public int apply(int x, int y) {
            return x * y;
        }
    },

    DIVIDE {
        @Override
        public int apply(int x, int y) {
            return x / y;
        }
    };
    
    public abstract int apply(int x, int y);
}
``` 
열거 타입을 사용하여 각 연산을 구체적으로 정의한다. `apply` 메소드는 각 열거 상수에서 구현되며, 코드가 더 직관적이고 타입 안정성을 제공한다. <br><br>
열거 타입은 각 상수의 동작을 캡슐화하므로, 새로운 연산을 추가하거나 기존 연산을 변경할 때도 코드가 더 안정적이고 유지보수하기 쉬워진다.


<br><br>

### `정수 상수` 사용 예 2)
```java

public class PhysicalConstants {
    
    public static final int METAL = 0;
    public static final int PLASTIC = 1;
    public static final int WOOD = 2;
    
    public static String getMaterialName(int type) {
        switch (type) {
            case METAL:
                return "Metal";
                
            case PLASTIC:
                return "Plastic";
                
            case WOOD:
                return "Wood";
                
            default:
                throw new IllegalArgumentException("Invalid type : " + type);
        }
    }
}
```


<br><br>

### `열거 타입` 사용 예 2)
```java

public enum Material {
    
    METAL, PLASTIC, WOOD;
    
    public String getMaterialName() {
        switch (this) {
            case METAL:
                return "Metal";

            case PLASTIC:
                return "Plastic";

            case WOOD:
                return "Wood";

            default:
                throw new AssertionError("Unknown material : " + this);
        }
    }
}

// 사용 예
public class Main {
    public static void main(String[] args) {
        Material material = Material.METAL;
        System.out.println(material.getMaterialName());     // Metal
    }
}
```
1) 열거형 정의 : `Material` 열거형은 3가지 상수 `METAL`, `PLASTIC`, `WOOD`를 정의한다.
2) 메소드 : `getMaterialName()` 메소드는 열거형의 값에 따라 적절한 문자열을 반환한다.
3) 타입 안정성 : `Material` 열거형은 타입 안정성을 보장하므로, 잘못된 값이 들어올 수 없다.

<br><br>

열거형을 사용하면 코드는 더 명확해지고 안전해지며, 새로운 값이 추가될 때 마다 컴파일 타임에 오류를 발견할 수 있다. <br>
이는, `int`상수를 사용하는 경우보다 훨씬 더 유용하고 오류를 줄일 수 있는 방법이다.