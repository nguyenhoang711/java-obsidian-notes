![[Pasted image 20260205225758.png]]
Concurrency: tính đồng thời, chỉ 1 CPU nhưng đan xen nhau xử lý task trong cùng 1 thời gian
- làm 1 món ăn luộc rau 5 phút, thái thịt 2 phút, chiên thịt 3 phút, nhặt rau 1 phút --> xử lý đồng thời trong vòng 6 phút thay vì 11 phút nếu làm tuần tự.
- Dựa trên thời gian chờ của I/O, tính I/O
Parallel: tính song song: có nhiều core trong GPU hoặc CPU xử lý song song, chia các task thành các phần nhỏ rồi xử lý, thực hiện nhiều tác vụ trên cùng 1 thời điểm.
- Tận dụng multi-core tính song song
- Có 3 đầu bếp cùng làm, và quan trọng đầu bếp khỏe
# 1. Java Concurrency và JVM
## JVM và Concurrency
- Heap and stack memory: stack cho từng thread, heap dùng chung các thread
- Synchronization: monitor lock, synchronized blocks, volatile, atomic variables.
- **Garbage collection**: ảnh hưởng Performance
## 2. JVM thread Memory model
### 2.1. Stack memory và heap memory
- Stack: chứa **local variables**, **method call frames** và riêng cho từng thread.
- Heap memory: chứa objects được chia sẻ giữa các threads, có thể gây ra **race condition** nếu không đồng bộ đúng cách.
**2.2. JMM (Java memory model) & Synhronization**
JMM: định nghĩa cách **threads** đọc ghi dữ liệu vào bộ nhớ.
3 vấn đề lớn trong JMM:
- **Visibility**: khi 1 thread thay đổi giá trị có thể thread khác không thấy sự thay đổi đó.
- **Atomicity**: phép toán có thể bị gián đoạn giữa hừng
- **Ordering**: Compiler có thể gây thay đổi thứ tự thực thi code gây lỗi.
# Phần 3: Atomic Variables – Giải pháp tối ưu cho biến dùng chung
## 3.1 AtomicInteger - Thay thế synchronized
Nếu chỉ cần cập nhật giá trị đơn giản --> **AtomicInteger** nhanh hơn **synchronized** và **lock**
Nhanh hơn synchronized vì không cần lock --> CPU cache với compare-and-swap (CAS)
# Phần 4: ForkJoinPool – Xử lý song song mạnh mẽ
- Là ThreadPool tối ưu cho các tác vụ đệ quy (divide and conquer)
- **ForkJoinTask** để chia công việc thành các task con xử lý song song
**ForkJoinPool** tận dụng đa lõi CPU hiệu quả hơn khi xử lý tác vụ song song
# Tổng kết
🔹 synchronized: Đồng bộ hóa cơ bản, dễ dùng nhưng có thể làm giảm hiệu suất.
🔹 Lock (ReentrantLock): Kiểm soát tốt hơn, tránh deadlock với tryLock().
🔹 AtomicInteger: Cách tối ưu để cập nhật biến đơn giản mà không cần lock.
🔹 ForkJoinPool: Mô hình xử lý song song mạnh mẽ với thuật toán chia để trị.

## **1️⃣ Bảng So Sánh Tổng Quan**

[](https://github.com/TaiTitans/Experience/blob/main/Documents/5.%20Java%20Core/2025/Core/Synchronized_Locks_Atomic_Variables_ForkJoinPool.md#1%EF%B8%8F%E2%83%A3-b%E1%BA%A3ng-so-s%C3%A1nh-t%E1%BB%95ng-quan)

|**Tiêu chí**|**synchronized**|**ReentrantLock**|**AtomicInteger**|**ForkJoinPool**|
|---|---|---|---|---|
|**Loại cơ chế**|Đồng bộ hóa|Khóa linh hoạt|Biến nguyên tử|Xử lý song song|
|**Cơ chế hoạt động**|Chặn thread khác truy cập|Chặn thread khác, hỗ trợ thử lock|Sử dụng Compare-And-Swap (CAS)|Chia nhỏ task và thực thi song song|
|**Hiệu suất**|Thấp khi có nhiều thread tranh chấp|Tốt hơn `synchronized` nhờ kiểm soát chi tiết|Tốt nhất cho biến đơn giản|Tối ưu cho công việc lớn cần song song|
|**Tính năng nâng cao**|Không có|Hỗ trợ `tryLock()`, `lockInterruptibly()`|Hỗ trợ update không cần lock|Hỗ trợ tự động chia nhỏ task|
|**Xử lý deadlock**|Dễ bị deadlock nếu không cẩn thận|`tryLock()` giúp tránh deadlock|Không có deadlock|Không bị deadlock do chia task tự động|
|**Độ phức tạp**|Đơn giản|Trung bình|Rất đơn giản|Phức tạp hơn|
|**Ứng dụng phù hợp**|Bảo vệ toàn bộ method hoặc block|Cần kiểm soát chi tiết hơn về lock|Khi chỉ cần cập nhật biến đơn giản|Khi xử lý dữ liệu lớn, công việc đệ quy|

## **2️⃣ Khi Nào Nên Sử Dụng Cơ Chế Nào?**

[](https://github.com/TaiTitans/Experience/blob/main/Documents/5.%20Java%20Core/2025/Core/Synchronized_Locks_Atomic_Variables_ForkJoinPool.md#2%EF%B8%8F%E2%83%A3-khi-n%C3%A0o-n%C3%AAn-s%E1%BB%AD-d%E1%BB%A5ng-c%C6%A1-ch%E1%BA%BF-n%C3%A0o)

🔹 **`synchronized`** 👉 Dùng khi cần đơn giản và hiệu suất không phải là vấn đề lớn.  
🔹 **`ReentrantLock`** 👉 Dùng khi cần kiểm soát chi tiết hơn, như thử lock (`tryLock()`) hoặc hỗ trợ nhiều điều kiện chờ (`Condition`).  
🔹 **`AtomicInteger`** 👉 Dùng khi chỉ cần tăng/giảm biến số nguyên mà không cần lock, tối ưu hiệu suất.  
🔹 **`ForkJoinPool`** 👉 Dùng khi xử lý công việc lớn có thể chia nhỏ, như thuật toán đệ quy hoặc xử lý song song dữ liệu lớn.