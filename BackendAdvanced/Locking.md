# 1. Phân biệt  transaction và locking
- Transaction: giải quyết vấn đề về atomic (xử lý nguyên khối), consistency (tính nhất quán dữ liệu)
- Locking: giải quyết vấn đề về race condition
![[Pasted image 20260316231929.png]]
VD: bài toán chuyển tiền
Số dư của A: 50
Thực hiện chuyển tiền 2 lần (gần như đồng thời):
- A --> B: 30
- A --> C: 40
Lệnh update số dư chạy sau khi thực hiện request số 2 --> race condition
Cơ chế **locking**: đảm bảo không update dư thừa

# 2. Tái hiện context failure
Tạo 2 session trong mysql database
- Thực hiện 2 req đồng thời trên 2 session
![[Pasted image 20260316232517.png]]
Commit trước bên session 1
- Thực hiện update trên session 2 ( - 20) rồi commit 
![[Pasted image 20260316232741.png]]
Kết quả: **số dư âm** --> transaction không giải quyết vấn đề **race condition** (thứ tự thực hiện)
Sử dụng lock
# 3. Locking
## 3.1 Type of locks in Mysql
![[Pasted image 20260316232856.png]]

Có 2 level lock:
- Shared (Read): cho phép đọc nhưng lock với ghi
- Exclusive (Update ): lock read and write
### Select for share
Tạo khóa:
![[Pasted image 20260316234321.png]]

Câu lệnh để đọc ra các đoạn lock:
![[Pasted image 20260316234349.png]]
- lock_type: TABLE là khóa của bảng, RECORD là khóa record (data = 5) và lock_type = S (share)

Thực hiện chạy lệnh update:
![[Pasted image 20260316234545.png]]
Phải chờ: do có session khác đang cầm khóa (lock for share) --> không cho phép update.
Cách xử lý: bỏ lock (**rollback**)

### Select for update
![[Pasted image 20260316234917.png]]
Khi 1 session cầm lock for update --> các session khác không thể select hoặc update

Thông tin lock:
![[Pasted image 20260316235039.png]]
Khi session 2 muốn thêm lock for share --> lock_status = WAITING vì đang có lock_mode = IX (lock for update)
![[Pasted image 20260316235152.png]]
Khi transaction nhả lock (**commit**) --> được thực thi tạo lock bên session còn lại

TH đặc biệt:
![[Pasted image 20260317000229.png]]
Khi có 1 lock for update nhưng thực hiện select bình thường vẫn ok
CHỉ chặn tạo thêm khóa --> chặn req yêu cầu lock khác.
# 4.  Loại lock nào phù hợp giải bài toán ở đầu
![[Pasted image 20260317001448.png]]
**Sử dụng Select for update**
![[Pasted image 20260317001649.png]]
Sử dụng **lock select for update**
Khi 1 bên commit rồi thì bên kia mới select được 
Đến đoạn check balance < amount ? --> exception + rollback
