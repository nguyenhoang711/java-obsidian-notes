# Process vs Thread
## Làm sao để chạy nhiều tiến trình khi chỉ có 1 CPU
- Time-slicing: thực thi 1 process, tạm dừng nó lại rồi lại và chạy 1 process khác
- Context switching: khả năng dừng 1 process và chạy 1 process khác
![[Pasted image 20261010172003.png]]
Khoảng thời gian context switching ngắn --> ko dễ cảm nhận được
## Trạng thái Process
Khi có Input/ Output: cần nhiều thời gian --> context switching sang process khác --> status block --> switch sang process khác
## Race condition
VD: liên tục WithDraw và Deposit đồng thời trên cùng 1 thread --> Expect Result balance = 0
Interleave: 2 hoặc nhiều thread xen vào nhau chạy đồng thời
Trong mỗi thread sẽ thực hiện tuần tự 3 bước sau:
- Lấy giá trị hiện tại của balance
- Thêm 1
- GHi kết quả (balance = 1)
![[Pasted image 20261010174045.png]]
Đôi lúc các lệnh lại chạy như bên phải, ko có thứ tự khi lấy giá trị **balance**
--> gây ra Race condition
Java cung cấp các Collection **thread-safe** (StringBuffer, BlockingQueue, ConcurrentMap....) tuy nhiên performance sẽ giảm đi.
Keywork: synchronized method, Javascript single-thread
# Network Concepts
IP address: nhận diện network interface
Hostname: tên chuyển đổi thành địa chỉ IP. 1 hostname có thể trỏ đến nhiều IP và nhiều hostname có thể trỏ đến cùng 1 IP.
Port: kết nối tới 1 process đang chạy. Network interface có nhiều port, từ 1 - 65535. Các port mặc định như 
+ 22: SSH
+ 25: email server
+ 80: web
+ 443: HTTPS
Socket: Điểm nối client và server
Protocol: tập các messages được trao đổi 2 bên. 1 số protocol phổ biến như HTTP, SMTP.
# Code review
Những vấn đề cần lưu ý:
- Lặp code (DRY - don't repreat yourself)
- Không nhất quán code và yêu cầu nghiệp vụ
- Off-by-one-error: vòng for, i = 0, i == 1, x < arr.length
- Scope biến quá lớn
- Magic numbers
Code không minh bạch:
- Tên biến, tên hàm ko rõ ràng
- Thụt đầu dòng
- Biến/ Hàm sử dụng cho nhiều mục đích

Tham khảo: https://mtlynch.io/code-review-love/

