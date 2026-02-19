Trong lập trình đa luồng, tạo và quản lý threads là quan trọng. Bạn cần hiểu cách **Thread Pool**, **ExecutorService** và **CompletableFuture** hoạt động để tối ưu hiệu suất ứng dụng.
Java cung cấp **ExecutorService** để quản lý Thread Pool, giúp tối ưu hiệu suất:
- **FixedThreadPool**: Giới hạn số lượng thread chạy đồng thời.
- **CachedThreadPool**: Tạo thread mới khi cần, reuse thread cũ nếu có sẵn.
- **ScheduledThreadPool**: Lên lịch chạy task theo thời gian.
- **ForkJoinPool**: Chia nhỏ công việc theo mô hình Work Stealing.
# 1. ThreadPool là gì?
Là nhóm các worker threads được quản lý cách hiệu quả thay vì phải tạo một thread mới mỗi lần
- **Giảm overhead** của tạo thread mới liên tục
- **Tái sử dụng** threads giúp tăng hiệu suất
- **Kiểm soát số lượng threads** hoạt động
# 2. ExecutorService - Tạo Thread Pool

| Loại ThreadPool           | Đặc điểm                   |
| ------------------------- | -------------------------- |
| newFixedThreadPool(n)     | n cố định                  |
| newCachedThreadPool()     | số thread linh hoạt        |
| newSingleThreadExecutor() | chỉ 1 thread, chạy tuần tự |
| newScheduledThreadPool(n) |                            |
## VD với ThreadPool
📌 **Tạo một Fixed Thread Pool với 3 threads:**

```java
import java.util.concurrent.*;

public class ThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3);

        for (int i = 1; i <= 5; i++) {
            int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " is running on " + Thread.currentThread().getName());
                try {
                    Thread.sleep(2000); // Giả lập công việc
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }

        executor.shutdown(); // Không nhận thêm task mới, nhưng đợi các task hiện tại hoàn thành
    }
}
```
- **Giải thích**
- Pool có 3 threads nhưng có 5 tasks, nên 3 task đầu tiên sẽ chạy, 2 task còn lại phải đợi đến khi có thread trống.
- Không cần tự tạo và quản lý threads, chỉ cần submit task vào ExecutorService.
## So sánh Fixed Thread Pool & Cached Thread Pool
- **CachedThreadPool** có thể tạo nhiều threads hơn nếu cần
- Không thể tự kiểm soát được số lượng thread tạo ra
```java
ExecutorService executor = Executors.newCachedThreadPool();

for (int i = 1; i <= 10; i++) {
    int taskId = i;
    executor.submit(() -> {
        System.out.println("Task " + taskId + " running on " + Thread.currentThread().getName());
    });
}

executor.shutdown();
```
Nếu có sẵn **thread**, nó sẽ tái sử dụng; nếu không, nó sẽ tạo thread mới, có thể gây tốn tài nguyên nếu không kiểm soát tốt.
# Phần 2: CompletableFuture – Lập trình Bất Đồng Bộ
## CompletableFuture là gì?
Hỗ trợ lập trình bất đồng bộ
- Cho phép **chaining** các bước xử lý mà không cần **callback** phức tạp
- Kết hợp nhiều task với **combine, compose**
## Tạo CompletableFuture cơ bản
```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) {
        CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
            System.out.println("Running in: " + Thread.currentThread().getName());
        });

        future.join(); // Đợi task hoàn thành
    }
}
```
**runAsync**: chạy 1 thread từ **ForkJoinPool**.
## ChainingTasks (thenApply, thenAccept)
**VD: chaining các bước xử lý**
```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureChain {
    public static void main(String[] args) {
        CompletableFuture<Void> future = CompletableFuture.supplyAsync(() -> {
            System.out.println("Fetching data...");
            return "Data from API";
        }).thenApply(data -> {
            return data.toUpperCase();
        }).thenAccept(result -> {
            System.out.println("Processed Result: " + result);
        });

        future.join();
    }
}
```
- supplyAsync(): chạy tác vụ trả về String
- thenApply: chuyển đổi dữ liệu (**chaining**)
- thenAccept: nhận kết quả và in ra
## Kết hợp nhiều CompletableFuture
 Ví dụ: Kết hợp hai tasks chạy song song
 ```java
 import java.util.concurrent.CompletableFuture;

public class CombineCompletableFuture {
    public static void main(String[] args) {
        CompletableFuture<String> task1 = CompletableFuture.supplyAsync(() -> {
            return "Hello";
        });

        CompletableFuture<String> task2 = CompletableFuture.supplyAsync(() -> {
            return "World";
        });

        CompletableFuture<String> combined = task1.thenCombine(task2, (res1, res2) -> res1 + " " + res2);

        System.out.println(combined.join()); // Kết quả: "Hello World"
    }
}
 ```
 **thenCombine**: kết hợp 2 kết quả của 2 CompletableFuture

## Handling Exceptions
bắt lỗi với **exceptionally**: trả giá trị mặc định khi có lỗi
```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureErrorHandling {
    public static void main(String[] args) {
        CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
            if (true) throw new RuntimeException("Something went wrong!");
            return 10;
        }).exceptionally(ex -> {
            System.out.println("Caught Exception: " + ex.getMessage());
            return 0; // Trả về giá trị mặc định khi lỗi
        });

        System.out.println("Result: " + future.join());
    }
}
```
