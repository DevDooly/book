# Future

\
java.util.concurrent.Future비동기 연산의 결과를 나타내며, 연산이 완료되면 결과를 제공한다.​

### **Future 생성하기**

예를 들어, [`ExecutorService`](https://app.gitbook.com/o/XbTRMHXUOfD9OYyo64Tr/s/TxTGdaKq3U8C6l5S7Tr5/~/diff/~/changes/31/~/revisions/tbBOr6FtDAEO5SMIwHeW/java/executorservice)를 사용하여 비동기 작업을 제출하고 `Future` 객체를 받을 수 있다.

```java
ExecutorService executor = Executors.newSingleThreadExecutor();
Future<Integer> future = executor.submit(() -> {
    Thread.sleep(1000);return 1;
});
```

### **Future 사용하기**

* **결과 확인 및 가져오기**: `isDone()` 메서드로 작업 완료 여부를 확인하고, `get()` 메서드로 결과를 가져올 수 있다. `get()`은 작업이 완료될 때까지 블로킹된다.

```java
while(!future.isDone()) {
    System.out.println("Calculating...");
    Thread.sleep(300);
}
Integer result = future.get();
```



* **작업 취소하기**: `cancel(true)` 메서드로 작업을 취소할 수 있으며, 성공 시 `true`를 반환한다. 취소된 작업에서 `get()`을 호출하면 `CancellationException`이 발생한다.

```java
boolean canceled = future.cancel(true);
```

### **스레드 풀과 병렬 처리**

​[`ExecutorService`](https://app.gitbook.com/o/XbTRMHXUOfD9OYyo64Tr/s/TxTGdaKq3U8C6l5S7Tr5/~/diff/~/changes/31/~/revisions/tbBOr6FtDAEO5SMIwHeW/java/executorservice)를 사용하여 스레드 풀을 구성하면 여러 작업을 병렬로 처리할 수 있다.

예를 들어, `newFixedThreadPool(2)`를 사용하여 두 개의 스레드를 가진 풀을 생성하면 두 작업을 동시에 실행할 수 있다.

```java
ExecutorService executor = Executors.newFixedThreadPool(2);
Future<Integer> future1 = executor.submit(() -> {
    // 작업 1
});

Future<Integer> future2 = executor.submit(() -> {
    // 작업 2
});
```

### **ForkJoinTask**

`ForkJoinTask`는 `Future`를 구현하며, 작은 작업으로 분할하여 병렬 처리를 수행하는 데 사용된다.

`RecursiveTask`를 확장하여 재귀적인 작업을 구현할 수 있다.

```java
public class FactorialSquareCalculator extends RecursiveTask<Integer> {
    private Integer n;​
    
    @Override
    protected Integer compute() {
        if (n <= 1) {
            return n;
        }
        FactorialSquareCalculator calculator = new FactorialSquareCalculator(n - 1);
        calculator.fork();
        return n * n + calculator.join();
    }
}

```

### **사용 예시**

```java
ForkJoinPool forkJoinPool = new ForkJoinPool();
​FactorialSquareCalculator calculator = new FactorialSquareCalculator(10);​
forkJoinPool.execute(calculator)
```
