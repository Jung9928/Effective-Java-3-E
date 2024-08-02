# Item 7. 다 쓴 객체 참조를 해제하라

### 내용 요약 <br>
  - 메모리 leak은 Java 프로그램에서 종종 발생하는 이유는 주로 더 이상 쓰지 않는 잉여 인스턴스가 남아있기 때문이다. <br>
    이러한 메모리 누수는 프로그램 메모리 사용량을 증가시키고 결국엔 성능 저하 및 OOM(Out Of Memory Error)을 발생시킨다. <br>

    
### 메모리 누수 방지 방법 <br>
    1) 다 쓴 객체 참조 해제 : 더 이상 필요없는 객체를 참조하는 필드를 null로 설정하여 GC가 이를 회수하도록 한다. <br><br>
    2) 자기 메모리 관리 클래스 : 자신이 관리하는 객체를 명시적으로 해제해야 한다. ex) 더 이상 사용하지 않는 인스턴스에 대해서 null 처리를 한다. <br><br>
    3) 캐시 사용 시, 주의 : 캐시는 메모리 leak을 발생시킬 수 있으므로 캐시에 인스턴스를 추가할 때, 강한 참조를 사용하지 않도록 주의 <br><br>
    4) 리스너와 콜백 해제 : 리스너와 콜백을 등록하고 해제하지 않으면 메모리 누수가 발생할 수 있음. 따라서, 더 이상 필요하지 않을 땐, 명시적으로 해제하자.


```Java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    
    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    
    public Object pop() {
        if(size == 0)
            throw new EmptyStackException();
        
        Object result = elements[--size];
        elements[size] = null;      // 다 쓴 객체 참조 해제
        return result;
    }
    
    private void ensureCapacity() {
        if(elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```
* 위 코드에서 `elements = new Object[DEFAULT_INITIAL_CAPACITY];` 이 부분에서 메모리 누수가 발생할 가능성이 있다. <br>
만약, `DEFAULT_INITIAL_CAPACITY`값 만큼 메모리를 할당받았으나 사용하는 메모리는 그보다 적은 공간만 활용한다면 <br>
GC가 사용되지 않는 비활성영역에 대해선 가비지 컬렉션을 해야할 대상으로 판단할 수 없다. <br>
그 이유는, GC 입장에선 `DEFAULT_INITIAL_CAPACITY`에서 비활성화된 메모리영역을 참조하는 것도 똑같이 유효한 객체로 판단하기 때문이다. <br><br>
`pop()` 메소드는 스택에서 요소 제거 후, 해당 요소에 대한 참조를 `null`로 설정하여 메모리 누수를 방지한다. <br>
이로써, 가비지컬렉터(GC)가 더 이상 필요치 않은 객체를 회수할 수 있게 된다. <br><br>
또한, 무조건 null처리를 하는 것이 모든 상황에서 정답은 아니다. 다 쓴 메모리공간을 해제하는 가장 좋은 방법은 <br>
메모리가 할당된 변수의 유효범위(scope)를 최소한으로 설정해놓는 것이다.

### 정리하자면, 아래와 같다.
1) 필요 없어진 객체 참조를 해제하지 않으면 메모리 누수가 발생할 수 있다 (GC가 인스턴스를 heap 해제 하지 않기 때문)
2) 메모리를 할당하는 역할을 수행하는 클래스는 명시적으로 객체 참조를 해제하는게 좋다 (위 코드에서 Stack 클래스처럼 new로 메모리 할당을 수행하는 클래스)
3) 캐시와 리스너, 콜백 등을 사용할 때는 참조 해제에 주의할 것.

<br><br>

### ex_01) 캐시에서 강한 참조 유지 시, 메모리가 누수 발생을 방지할 수 있는 코드
```Java
import java.util.WeakHashMap;

public class Cache<K, V> {
    
    private final WeakHashMap<K, V> cacheMap = new WeakHashMap<>();
    
    public void put(K key, V value) {
        cacheMap.put(key, value);
    }
    
    public V get(K key) {
        return cacheMap.get(key);
    }
    
    public static void main(String[] args) {
        Cache<String, String> cache = new Cache<>();
        String key = new String("key");
        String value = "value";
        
        cache.put(key, value);
        System.out.println("Before GC : " + cache.get("key"));
        
        key = null;
        System.gc();
        
        // GC 이후에 캐시에서 값을 가져오면 NULL이 반환된다.
        System.out.println("After GC : " + cache.get("key"));
    }
};
```
위 코드에서 `WeakHashMap`은 키에 대한 약한 참조를 유지하므로 GC가 키를 회수할 수 있다. <br>
키가 더 이상 사용되지 않으면 메모리에서 제거된다. <br><br>

### ex_02) 리스너 로직 사용 시, 메모리가 누수 발생을 방지할 수 있는 코드

```java
import java.util.ArrayList;
import java.util.list;

interface EventListener {
  void onEvent();
}

class EventSource {
  private final List<EventListener> listeners = new ArrayList<>();

  public void addListener(EventListener listener) {
    listener.add(listener);
  }

  public void removeListener(EventListener listener) {
    listener.remove(listener);
  }

  public void triggerEvent() {
    for (EventListener listener : listeners) {
      listener.onEvent();
    }
  }
}

public class ListenerExample {
  public static void main(String[] args) {
    EventSource source = new EventSource();

    EventListener listener = new EventListener() {
      @Override
      public void onEvent() {
          System.out.println("Event received");
      }
    };
    
    source.addListener(listener);
    source.triggerEvent();
    
    source.removeListener(listener);        // 리스너 해제
    source.triggerEvent();
  }
}
```
* 위 코드에선, 리스너를 더 이상 필요로 하지 않을 때, `removeListener` 메소드를 호출하여 리스너를 해제한다.

<br><br>

### ex_03) 콜백 사용 시, 메모리가 누수 발생을 방지할 수 있는 코드

```java
import java.lang.ref.WeakReference;

interface Callback {
  void execute();
}

class CallbackHandler {
  private WeakReference<Callback> callbackRef;

  public void registerCallback(Callback callback) {
    this.callbackRef = new WeakReference<>(callback);
  }

  public void doSomething() {
    if (callbackRef != null) {
      Callback callback = callbackRef.get();

      if (callback != null) {
        callback.execute();
      } else {
        System.out.println("Callback has been garbage collected");
      }
    }
  }
}

public class CallbackExample {
  public static void main(String[] args) {
    CallbackExample handler = new CallbackExample();

    Callback callback = new Callback() {
      @Override
      public void execute() {
          System.out.println("Callback executed");
      }
    };
    
    handler.registerCallback(callback);
    handler.doSomething();
    
    callback = null;
    System.gc();
    
    handler.doSomething();
  }
}
```
* 위 코드에선, 콜백에 대한 약한 참조를 유지하므로, 콜백이 더 이상 필요 없으면 GC에 의해 회수될 수 있다.

<br><br>


### GC의 참조 방식
1) 강한 참조 : 기본적으로 Java에서 객체를 참조하는 모든 참조를 의미. GC가 해당 객체를 회수하지 못하도록 한다. 
```Java
Object strongRef = new Object();
```
* `strongRef`가 객체를 참조하는 동안, GC는 해당 객체를 회수할 수 없다.

<br>

2) 약한 참조 : GC가 언제든지 객체를 회수할 수 있도록 허용한다. 주로 캐시와 같은 메모리 구조에 사용된다. `java.lang.ref.WeakReference`클래스를 사용하여 약한 참조 생성이 가능하다
<br>
```java
import java.lang.ref.WeakReference;

public class WeakReferenceExample {
    public static void main(String[] args) {
        Object strongRef = new Object();
        WeakReferenceExample<Object> weakRef = new WeakReferenceExample<>(strongRef);
        
        System.out.println("Before GC : " + weakRef.get());
        
        strongRef = null;               // 강한 참조 해제
        System.gc();                    // GC 호출
      
        System.out.println("After gc : " + weakRef.get());      // null일 가능성이 농후
    }
}
```
* 위 코드에서 `strongRef`가 null로 설정되고 GC가 실행된 후, 약한 참조인 `weakRef`는 `null`을 반환할 가능성이 크다. <br>
  why? -> GC가 약한 참조만 남아있는 객체를 회수했기 때문. <br><br>


3) 소프트 참조 : 약한 참조보다 덜 강력한 방식으로 객체를 참조한다. GC는 메모리가 부족할 때만 소프트 참조를 회수한다. 주로 캐시에서 사용된다. <br>
```java
import java.lang.ref.SoftReference;

public class SoftReferenceExample {
    public static void main(String[] args) {
        Object strongRef = new Object();
        SoftReference<Object> softRef = new SoftReference<>(strongRef);
        
        System.out.println("Before GC : " + softRef.get());
        
        strongRef = null;               // 강한 참조 해제
        System.gc();                    // GC 호출
      
        System.out.println("After GC : " + softRef.get());          // 여전히 객체를 참조하고 있음
    }
}
```
* 소프트 참조는 GC가 즉시 회수하지 않으므로, 메모리가 충분하다면 `softRef`는 여전히 객체를 참조할 수 있다. <br><br>

4) 팬텀 참조 <br> 
-> 객체가 GC에 의해 회수된 후에도 객체의 소멸을 추적하기 위해 사용된다. <br> 
   `java.lang.ref.PhantomReference` 클래스를 사용하며, 객체의 수거 후 특정 작업을 수행할 필요가 있을 때 유용하다. <br>

```java
import java.lang.ref.PhantomReference;
import java.lang.ref.ReferenceQueue;

public class PhantomReferenceExample {
    public static void main(String[] args) {
        Object strongRef = new Object();
        ReferenceQueue<Object> refQueue = new ReferenceQueue<>();
        
        strongRef = null;           // 강한 참조 해제
        System.gc();                // GC 호출
        
        // 팬텀 참조는 직접 객체에 접근할 수 없음
        System.out.println("After GC : " + phantomRef.get());
        
        // ReferenceQueue에서 팬텀 참조가 큐에 추가되었는지 확인
        if(refQueue.poll() != null) {
            System.out.println("Phantom reference enqueued");
        }
    }
}
```
* 팬텀 참조는 객체가 가비지 컬렉션된 후, 참조 큐에 들어가므로 객체의 수거를 감지하고 후속 작업 진행이 가능하다
<br><br>

### 정리하자면
  - 강한 참조 : 기본 참조 방식, 객체가 GC에 의해 회수되지 않음.
  - 약한 참조 : 객체가 약한 참조만 가지고 있을 때, GC가 언제든지 회수 가능
  - 소프트 참조 : 메모리가 부족할 때만 객체를 회수
  - 팬텀 참조 : 객체가 가비지 컬렉션된 후, 객체의 소멸을 추적

-> 약한 참조는 주로 캐시에서 사용되며, 메모리 누수를 방지하면서도 필요하지 않은 객체를 GC가 회수할 수 있도록 한다.






