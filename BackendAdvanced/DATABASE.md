# Partition
# Sharding
# JSON
Đặt vấn đề:
Hệ thống ERP nói riêng hoặc database nói chung khi tạo 1 record mới vào table,việc phát sinh thuộc tính mới xảy ra hàng ngày, như vậy sẽ luôn phải tạo thêm cột cho table --> độ lớn của bảng tăng lên, data rác, quá nhiều index. Có cách nào khắc phục không?
Solution:
- Tạo 1 bảng chứa field_id và column_name sau đó JOIN vào trong SELECT
- **JSON field**
![[Pasted image 20260618010826.png]]
Tạo 2 bảng 1 bảng sử dụng JSON, bảng còn lại không
trường `g INT GENERATED ALWAYS AS (content -> "$.id")` -> tượng trưng cho ID lấy trong JSON --> ko tăng kích thước trong DB, giống hệt VIEW
![[Pasted image 20260618012354.png]]
Cách SELECT các trường trong JSON MySQL
![[Pasted image 20260618012442.png]]
Sử dụng INDEX trong cả dữ liệu JSON và không dữ liệu JSON --> kết quả tương đối nhau
Không sử dụng INDEX (range) thì bình thường tốc độ lớn hơn so với dữ liệu JSON
![[Pasted image 20260618012620.png]]
với JSON tốn nhiều tài nguyên khi truy xuất hơn là không dùng JSON --> nên sử dụng INDEX với JSON.

# Interview
1. Khắc phục lỗi truy vấn chậm trong database, cụ thể là đơn hàng tiktok
Câu hỏi chưa tường minh, có nhiều nguyên do gây ra truy vấn chậm, sau đây là 3 trường hợp phổ biến:
- Dữ liệu trong bảng quá nhiều, hoặc data trong field quá nhiều
- Chưa tạo index hoặc chưa sử dụng index đúng
- Truy vấn đúng nhưng lấy dữ liệu quá lớn
# Cơ chế cách ly
Repeatable Read
Khi lượng đồng thời cao, có 2 transaction A và B cùng đọc 1 giá trị, trước khi B commit A lấy giá trị 100 (Read Committed) nhưng ở B update thành 200 thì đọc lại A thì là 200 --> gây ra sai lệch output khi transaction A xử lý tiếp ở những bước dưới.
-->Sinh ra repeatable read để đảm bảo dữ liệu của transaction không đổi trong suốt quá trình hoạt động của transaction đó.
Cho dù commit khác có thay đổi dữ liệu thì giá trị vẫn ko thay đổi từ khi bắt đầu transaction

Serializable
Tuần tự, xếp hàng
Default serializable sẽ lock toàn bộ bảng.
Có cách để không bị lock toàn bộ bảng:
- set index vào column đang cần truy xuất
- dùng câu lệnh select ... for update hoặc select ... fire share thay vì set transaction mode = serializable (khoá bi quan)
Sử dụng SELECT ... FOR UPDATE: cập nhật giá trị cột, khoá dòng (row)
![[Pasted image 20260619002528.png]]
Không cho phép sửa đổi (User Y) khi đang có transaction đang thực hiện update. (khoá row id = 1)
Lưu ý khi sử dụng SELECT... FOR UPDATE:
- Chỉ mục sử dụng trong WHERE thì sẽ chỉ khoá dòng, không thì sẽ khoá cả table
- Sử dụng trong ngăn chặn truy vấn đồng thời cao
- Khoá 1 phần của table (WHERE id > range)
--> deadlock
![[Pasted image 20260619002916.png]]
VD trên khoá các dòng id > 2
