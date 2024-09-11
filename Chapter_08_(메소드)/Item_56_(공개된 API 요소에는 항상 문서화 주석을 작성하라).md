# Item 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라

### 내용 요약 <br>
1. `JavaDoc` 주석의 중요성
    - Javadoc 주석을 통해 API의 동작, 매개변수, 반환값, 예외 상황 등을 명확히 설명할 수 있다. <br>
      이 주석은 개발자에게 코드의 사용 방법을 정확하게 전달하는 공식 문서로, API의 가독성, 유지보수성, 재사용성을 크게 향상시킨다. <br><br>


2. 모든 공개 API 요소에 주석을 작성하라
    - 클래스, 인터페이스, 메소드, 필드 등 모든 공개 API에는 문서화 주석을 작성해야 한다. <br>
      특히, 공개 API느 다른 개발자가 사용하게 될 코드이므로, 이를 이해하고 사용할 수 있도록 충분한 설명이 필요하다. <br><br>
        

3. `문서화 주석 작성` 시 주의 사항
    - 간결하고 명확하게 작성
      - 문서화 주석은 불필요한 정보를 제거하고, 필요한 정보를 명확히 전달해야 한다. <br><br>
      
    - 모든 매개변수와 반환 값 설명
      - 메소드의 매개변수(@param), 반환 값(@return), 예외(@throws)등을 모두 설명해야 한다. <br>
        이를 통해, 메소드의 의도를 분명히 하고, 호출자가 예상할 수 있는 결과를 명확하게 기술할 수 있다. <br><br>
      

4. API 사용 방법을 명확히 전달
    - 문서화 주석은 API의 의도된 사용 방법을 잘 설명해야 하며, 사용자가 올바르게 API를 사용할 수 있도록 가이드를 제공해야 한다.


<br><br>


### 잘못된 예시 : `Javadoc` 주석이 없는 경우
```java
public class Item56Example {
    
    // 두 수를 더하는 메소드
    public int add(int a, int b) {
        return a + b;
    }
    
    // 배열에서 특정 값을 찾는 메소드
    public int findIndex(int[] arr, int value) {
        for(int i=0; i<arr.length; i++) {
            if(arr[i] == value) {
                return i;
            }
        }
        return -1;              // 찾지 못했을 때
    }
}
```
위 코드에서 public 메소드가 있지만 `Javadoc` 주석이 없어 메소드의 동작, 매개변수, 반환 값 등에 대한 정보가 부족하다. <br>
다른 개발자가 이 메소드를 사용할 때, 명확한 정보를 얻을 수 없으므로, 주석을 추가해야 한다. <br><br>



### 올바른 예시 : `Javadoc` 주석이 포함된 경우
```java
/**
 * 수학 관련 유틸리티 메소드를 제공하는 클래스.
 * */
public class Item56Example {
    
    /**
     * 두 정수를 더하여 합을 반환한다.
     * 
     * @param a 더할 첫 번째 정수
     * @param b 더할 두 번째 정수
     * @return 두 정수의 합
     * 
     * */
    public int add(int a, int b) {
        return a + b;
    }
    
    /**
     * 주어진 배열에서 특정 값을 찾아 그 인덱스를 반환한다.
     * 배열에 값이 없으면 -1을 반환한다.
     * 
     * @param arr 검색할 배열
     * @param value 찾고자 하는 값
     * @return 배열에서 값의 인덱스, 값이 없으면 -1
     * @throws IllegalArgumentException 배열이 null인 경우
     * */
    public int findIndex(int[] arr, int value) {
        
        if(arr == null) {
            throw new IllegalArgumentException("배열이 null일 수 없습니다.");
        }
        
        for(int i=0; i<arr.length; i++) {
            if(arr[i] == value) {
                return i;
            }
        }
        return -1;              // 찾지 못했을 때
    }
}
```

<br><br>

### Javadoc 주요 태그
1. `@param` : 메소드나 생성자의 매개변수를 설명할 때 사용.
2. `@return` : 메소드가 반환하는 값을 설명할 때 사용.
3. `@throws` 또는 `@exception` : 메소드가 던질 수 있는 예외를 설명할 때 사용.
4. `@see` : 관련된 클래스나 메소드로의 참조를 제공.
5. `@deprecated` : 더 이상 사용되지 않는 API 요소를 표시.


<br><br>


### 정리하자면
1. Open API의 모든 요소에는 Javadoc 주석을 작성해야 하며, 이는 다른 개발자가 API를 이해하고 사용할 수 있도록 도와준다. 
2. Javadoc 주석은 간결하고 명확하게 작성되어야 하며, 매개변수, 반환값, 예외 등을 모두 설명해야 한다.
3. API 문서화는 구현 세부 사항 대신 `동작`과 `계약`을 중심으로 작성되어야 하며, 이를 통해 API의 사용성을 높일 수 있다.

