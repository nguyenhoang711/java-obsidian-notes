Giải phóng lock cách tự động
Câu hỏi PV: Sự khác nhau Synchronized và RetrantLock, use case từng loại này.
## 2.3.Synchronized_Locks_Atomic_Variables_ForkJoinPool
2.3.1 Synchronized
- Là cơ chế cho phép **chỉ 1 thread** mới có thể truy cập đoạn code cụ thể
Java cung cấp 3 cách sử dụng đồng bộ
+ Synchronized method
+ Synchronized block (tăng hiệu suất)
+ Synchronized static method
# Phần 3: Locks – Kiểm soát đồng bộ nâng cao
### 3.1. Phân biệt lock và synchronized

| Lock (ReentrantLock)            | Synchronized                             |
| ------------------------------- | ---------------------------------------- |
| Kiểm soát chi tiết hơn          | Dễ sử dụng                               |
| Phải unlock() thủ công          | Tự động giải phóng                       |
| Có thể kiểm tra trạng thái lock | Ko thể kiểm tra thread bị lock hay khong |
- trylock(): tránh deadlock khi khóa không thể unlock
```java
if (lock.tryLock()) { 
    try {
        // Thực hiện công việc
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("Không thể lấy lock, thử lại sau");
}
```
🔹 **synchronized**: Đồng bộ hóa cơ bản, dễ dùng nhưng có thể làm giảm hiệu suất.
🔹 **Lock** (**ReentrantLock**): Kiểm soát tốt hơn, tránh **deadlock** với *tryLock*().

2️⃣ Khi Nào Nên Sử Dụng Cơ Chế Nào?
🔹 **synchronized** 👉 Dùng khi cần đơn giản và hiệu suất không phải là vấn đề lớn.
🔹 **ReentrantLock** 👉 Dùng khi cần kiểm soát chi tiết hơn, như thử lock (tryLock()) hoặc hỗ trợ nhiều điều kiện chờ (Condition).