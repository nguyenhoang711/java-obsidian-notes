**Thread là gì**
- Thread đơn vị nhỏ nhất thực thi chương trình
- Hỗ trợ đa luồng (multithreading) --> chạy song song các task thay vì tuần tự
Đặc điểm
- Nhẹ hơn process (lightweight process)
- Chia sẻ chung memory trong process
- Đường dẫn thực thi độc lập: mỗi chuỗi lệnh được CPU handle thứ tự riêng (có **stack** riêng nhưng share **heap** với process)
- Context switching nhanh hơn process
*Khái niệm Context switching*
**Context switching** =  
👉 CPU **tạm dừng** execution path (đường thực thi) hiện tại  
👉 **lưu trạng thái lại**
👉 **chuyển sang execution path khác**
Context = trạng thái hiện tại của thread/process:
- Program Counter (đang chạy dòng code nào)
- Register CPU
- Stack
- Trạng thái thread/process
Thread Context switching:
- Không đổi bộ nhớ
- Chỉ đổi stack và program counter
![[Pasted image 20260210092856.png]]
**Vì sao Context Switching lại quan trọng**
- CPU không chạy song song vô hạn
	- 1 core CPU --> 1 thread tại 1 thời điểm
	- Nhiều thread --> CPU liên tục context switchng
- Do đó cần **ThreadPool, ExecutorService**
VD với process và thread
Process: Chrome Browser
├── Thread 1: UI rendering
├── Thread 2: Network requests
├── Thread 3: JavaScript execution
└── Thread 4: File downloads
Nguy hiểm: share tài nguyên, lock--> hiểu vòng đời thread (life cycle)
1. Cách khởi tạo thread
![[Pasted image 20260205230357.png]]
- **Cách 1 tạo 1 class kế thừa từ Thread**
Ghi đè phương thức Run()
- **Runnable Interface Implementation**
Truyền 1 object được implement từ class Runnable, vì create thread có thể thực thi được Runnable Object
Để thực thi phương thức run() bởi thread, Chúng ta sẽ truyền instance của MyRunnable vào trong constructor của Thread
- **Vì sao Runnable lại tốt hơn?**
Vì Java ko support multiple inheritance
Tách logic (task) khỏi thread management
Có thể dùng với ExecutorService
Flexible
- **Sử dụng lambda expression**
1. Phương thức quan trọng của Thread
![[Pasted image 20260205230430.png]]

2. Quản lý và đồng bộ hóa Threads:
- **Đồng bộ hóa**: Sử dụng từ khóa `synchronized` hoặc các cơ chế khác như `Locks` để đảm bảo chỉ một thread có thể truy cập vào một phần của code vào một thời điểm.
- **Deadlock**: Khi hai hoặc nhiều thread bị mắc kẹt vì đang chờ đợi tài nguyên mà thread khác đang giữ.
- **Race Condition**: Khi nhiều thread cùng truy cập và cập nhật một tài nguyên mà không có đồng bộ hóa, có thể dẫn đến kết quả không đoán trước được.
4. Executor Framework:
Là một cách tiện lợi để quản lý và thực thi các thread trong Java sử dụng các interface như Executor, ExecutorService, và các lớp như ThreadPoolExecutor.
5. Thread life cycle (five state of state)
**NEW → RUNNABLE → RUNNING → TERMINATED
         ↓           ↓
      BLOCKED/WAITING
- **New state**
	- Thread được tạo trong memory
	- Chưa allocate resource
	- start method chưa gọi (lưu ý start() chỉ được gọi 1 lần --> **IllegalThreadStateException**)
	- Thread chưa được phép chạy
- Runnable state (Ready state)
	- Sau khi gọi hàm start(), thread sẵn sàng để chạy
	- Wait thread scheduler phân phối CPU time
	- Có thể cùng lúc nhiều thread trong state này
	- Có thể đang chạy hoặc waiting for CPU allocation
- Running state
	- Khi CPU đã allocate processor vào thread --> thực thi đoạn code trong hàm run()
Lưu ý: trong Java model, RUNNABLE hoặc RUNNING state được cho là giống nhau dưới góc nhìn của API
- Blocked/Waiting States (Non-Runnable States)
	- Blocked state: khi mà cố gắng truy cập vào resource synchronized và bị giữ bởi another thread
	- Waiting state: method sleep(), wait(timeout) hay join(timeout)
		- Transitioning back: Threads return to the RUNNABLE state when:
		- Mở khóa (lock become available) from LOCKED
		- Có thread khác gọi hàm notify()/ notifyAll() (from WAITING)
		- Timeout expire hoặc sleep duration complete (TIMED_WAITING)
- Terminated state
	- run() method hoàn thành
	- uncaught exception ném ra khỏi hàm run()
	- Resource đc release, không thể gọi lại hàm start()
### Daemon Thread
Chạy trong backgroud, tự động terminate khi tất cả user threads kết thúc
*Thread daemon = new Thread(() -> {*
    *while (true) {*
        *System.out.println("Daemon running...");*
        *try {*
            *Thread.sleep(1000);*
        *} catch (InterruptedException e) {}*
    *}*
*});*

*daemon.setDaemon(true);  // Phải set trước start()*
*daemon.start();*

*// Main thread kết thúc → daemon thread tự động terminate*
- Use cases
Garbage Collector
Background monitoring
Auto-save
