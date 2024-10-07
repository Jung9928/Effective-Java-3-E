# Item 80. 스레드보다는 실행자, 태스크, 스트림을 애용하라

### 내용 요약 <br>
1. 스레드 생성의 단점
    - 직접 스레드를 생성하여 관리하면 `코드의 가독성`과 `유지보수성`이 떨어진다. <br><br>
        
    - `자원 낭비`가 발생할 수 있다. 스레드를 많이 생성하면 메모리 소모가 커지고, 스레드 간의 `컨텍스트 스위칭 비용`이 발생할 수 있다. <br><br>

    - `성능 저하`와 `병목 현상`을 초래할 수 있으며, 특히 많은 스레드를 사용할 때 이를 적절하게 관리하기 어렵다. <br><br>
   
2. 고수준 API 사용
    - `java.util.concurrent` 패키지는 `스레드 관리의 복잡성을 줄이기 위한 다양한 고수준 API`를 제공한다. <br>
      대표적으로 `Executor 프레임워크`가 있으며, 이를 통해 스레드를 생성하고 관리하는 복잡한 작업을 숨길 수 있다. <br><br>

    - `ExecutorService`는 스레드 풀을 제공하여 다수의 태스크를 효율적으로 관리할 수 있다. <br>
      이를 사용하면 각 태스크를 별도로 스레드로 실행하지 않고, 필요한 만큼의 스레드만 생성하여 `효율적으로 자원을 관리`할 수 있다. <br><br>


3. 태스트(Task)와 Runnable, Callable
    - `태스크`는 실행할 작업을 나타내며, `Runnable` 또는 `Callable` 인터페이스로 구현할 수 있다. <br><br>
   
    - `Runnable`은 반환값이 없고, `Callable`은 반환값이 있는 작업을 정의한다. <br>
      이 둘을 Executor에 제출하여 스레드풀에서 실행하게 할 수 있다. <br><br>

<br>

4. Fork/Join 프레임워크
   - 병럴처리를 위한 프레임워크로, CPU 집약적인 작업을 작은 태스크로 쪼개어 병럴로 실행할 때 사용한다. <br>
     Java 7에 도입되었으며 `재귀적인 작업을 처리`할 때 유용하며, 스레드를 직접 다루지 않고도 고성능을 낼 수 있다. <br><br>

5. 스트림 API
   - Java 8부터 제공되는 `스트림 API`는 데이터를 병렬로 처리할 수 있는 기능을 제공한다. <br>
     `병렬 스트림`을 통해 대규모 데이터를 효율적으로 처리할 수 있으며, 스레드를 직접 관리할 필요 없이 `데이터 병렬화`를 지원한다. <br><br>


<br><br>



### 직접 스레드를 사용하는 비효율적인 코드
```java
public class ThreadExample {

    public static void main(String[] args) {
        for(int i=0; i<10; i++) {
            new Thread(() -> {
                System.out.println("Task executed by : " + Thread.currentThread().getName());
            }).start();
        }
    }
}
```
위 코드에선 `직접 스레드를 생성`하여 작업을 처리한다. <br>
작은 규모의 작업이라면 문제가 없지만, 많은 작업을 처리할 경우 `스레드가 과도하게 생성`되어 시스템 자원을 낭비하게 된다. <br><br>

<br><br>



### `ExecutorService`를 사용한 효율적인 코드
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorExample {

    public static void main(String[] args) {
        ExecutorExample executor = Executors.newFixedThreadPool(5);         // 5개의 스레드 풀
        
        for(int i=0; i<10; i++) {
            executor.submit(() -> {
                System.out.println("Task executed by : " + Thread.currentThread().getName());
            });
        }
        
        executor.shutdown();
    }
}
```
위 코드에선 `ExecutorService`를 사용하여 `스레드 풀`에서 태스크를 실행한다. <br>
`고정된 수의 스레드`만 생성되며, 새로운 작업은 기존 스레드가 처리할 준비가 될 때까지 대기한다. <br>
이를 통해 `자원을 효율적으로 관리`할 수 있다. 

<br><br>


### `Callable`과 `Future` 사용 예시
```java
import java.util.concurrent.*;

public class CallableExample {
    
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executor = Executor.newSingleThreadExecutor();
        
        Callable<Integer> task = () -> {
            return 123;
        };
        
        Future<Integer> future = executor.submit(task);
        System.out.println("Result : " + future.get());             // 123 출력
        executor.shutdown();
    }
}
```
위 코드에선 `Callable` 인터페이스를 사용하여 `반환값이 있는 태스크`를 정의할 수 있다. <br>
`Future` 객체를 통해 태스크의 `결과를 비동기적으로` 받아올 수 있다. <br><br>

<br><br>



### `Fork/Join 프레임워크` 사용 예시
```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

public class ForkJoinExample extends RecursiveTask<Long> {

    private long start, end;
    
    public ForkJoinExample(long start, long end) {
        this.start = start;
        this.end = end;
    }
    
    @Override
    protected Long compute() {
        if(end - start <= 10000) {
            long sum = 0;
            
            for(long i = start; i <= end; i++) {
                sum += i;
            }
            
            return sum;
        }
        else {
            long mid = (start + end) / 2;
            ForkJoinExample leftTask = new ForkJoinExample(start, mid);
            ForkJoinExample rightTask = new ForkJoinExample(mid + 1, end);
            leftTask.fork();
            
            return rightTask.compute() + leftTask.join();
        }
    }
    
    public static void main(String[] args) {
        ForkJoinPool pool = new ForkJoinPool();
        ForkJoinExample task = new ForkJoinExample(0, 1000000);
        long result = pool.invoke(task);
        System.out.println("Result : " + result);
    }
}
```
위 코드는 `Fork/Join 프레임워크`를 사용하여 `병렬 작업`을 처리하는 코드이다. <br>
큰 태스크를 작은 태스크로 분할하여 병렬로 처리한다. 이처럼 `복잡한 병렬 작업도 간결하게 구현`할 수 있다. <br><br>

<br><br>


### `스트림 API를 사용한 병렬처리`
```java
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

public class StreamExample {
    
    public static void main(String[] args) {
        List<Integer> numbers = IntStream.rangeClosed(1, 100)
                .boxed()
                .collect(Collectors.toList());
        
        List<Integer> squares = numbers.parallelStream()
                .map(x -> x * x)
                .collect(Collectors.toList());
        
        System.out.println(squares);
    }
}
```
위 코드는 `스트림 API`를 사용하여 `병렬로 작업을 처리`한다. `parallelStream()`을 사용하면 데이터가 병렬로 처리되어 `고성능`을 낼 수 있다. <br>
이 방식은 스레드를 직접 다루지 않고도 병렬 처리를 쉽게 구현할 수 있다. <br><br>

<br><br>

### 정리하자면
1. `직접적인 스레드 생성`은 비효율적이고 관리하기 어렵다.<br>
   대신 `Executor`, `Callable`, `Fork/Join`, `스트림 API`와 같은 고수준의 도구를 사용하여 스레드 관리를 단순화하고 성능을 최적화하는 것이 바람직하다. <br><br>

2. 이러한 도구들은 `멀티스레드 작업을 안전하고 효율적으로 처리`할 수 있도록 도와준다.


