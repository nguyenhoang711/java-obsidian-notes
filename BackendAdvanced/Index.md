Outline
1. Index introduction
2. Classification
	1. Data structure
	2. Physical storage index
	3. Characteristics
	4. Composite Index
3. Practices
	1. Index failure
	2. Best Practices
# Index Introduction
- Là **cấu trúc dữ liệu** dùng để tối ưu tốc độ truy vấn dữ liệu
- Hạn chế thao tác để tìm dữ liệu (vì có cấu trúc)
- Đa phần **index lưu ở ổ đĩa**
# 2. Classification
Các tiêu chí phân loại:
- By data structure: B+ tree index, Hash index, Fulltext (inverted) index, LSM tree
- By physical storage: clustered index, non-cluster index
- By number of columns: single-column index, composite index
- By characteristics: primary key index, unique column index, prefix index
## 2.1 Data structure
Cần quan tâm đến:
- Use case: mục đích sử dụng, có nhiều giải pháp cho cùng 1 vấn đề thì so tới các criteria khác
- **Time complexity**: các thao tác khác nhau có time khác nhau, chọn xem phù hợp cho trường hợp nào
- **Space complexity**:
- Complexity of implementation: logic có phức tạp để triển khai ko
### 2.1.1 B-Tree (Balanced Tree)
![[Pasted image 20260318231930.png]]
- Xây dựng cấu trúc dữ liệu làm index
- Tất cả nút lá cùng chung level, có thứ tự
- Mỗi node có thể chứa nhiều key
- Insert, delete và search có time complexity **O(LogN)**
### 2.1.2 B+Tree
- MySQL với engine InnoDB sử dụng B-Tree cấu trúc dữ liệu mặc định cho index
- Khác là ở các node lá có pointer để nối sang node cạnh
![[Pasted image 20260318232500.png]]
--> Có khả năng duyệt từ đầu đến cuối (Linked List) --> efficient for range queries
Quãng đường duyệt ít đi --> search nhanh, ít thao tác
![[Pasted image 20260318232816.png]]
**Một node có thể chứa nhiều key** 
1.  **depth giảm (ít level)**
Dữ liệu từng node trên disk --> phân tán nhảy gọi IO --> performance kém
2. **Giảm gọi xuống ổ đĩa**
Hạn chế số lần gọi xuống ổ đĩa lấy data
VD: muốn lấy dữ liệu số 99
Mỗi lần gọi xuống ổ đĩa là lấy nguyên 1 node --> đi kèm 30 và 90 cùng 1 node
Check 99 >30 sang phải --> không phải gọi 1 lần nữa xuống level thấp hơn
### 2.1.3 So sánh B-Tree và B+Tree (đọc thêm)
![[Pasted image 20260318233436.png]]
### 2.1.4 Hash Index
![[Pasted image 20260319000737.png]]
Cơ chế handle collision
Limitations:
- Phù hợp handle dạng bảng, không hợp sort, dạng range-base queries
- Collision
## 2.2 Physical index
### 2.2.1 Clustered index
![[Pasted image 20260319001030.png]]
Bất kì cấu trúc dữ liệu nào cũng có quy tắc tổ chức dữ liệu --> **clustered index.**
Mỗi bảng chỉ có 1 logic sắp xếp dữ liệu (c**lustered index**)
Mặc định, **primary key** là dùng cho **clustered index.**
Mỗi node lá trong B+Tree ngoài chứa key sẽ chứa luôn cả dữ liệu (actual data) của node đó. (name, email, ...)
Trong InnoDB, mặc định cluster index sử dụng B+Tree structure, không thể sử dụng hash
### 2.2.2 Non-clustered index
Những index tạo thêm ngoài clustered index
![[Pasted image 20260319001525.png]]
VD: đánh thêm index ở cột email --> secondary index
![[Pasted image 20260319001713.png]]
Câu truy vấn where = 'c@d.com' thì sẽ sử dụng secondary index tại email
--> tìm thấy key = 2346
![[Pasted image 20260319001803.png]]
Tìm được key sẽ duyệt lên clustered index
Tìm giá trị id primary key theo cluster index --> cách hoạt động của secondary index
Ít nhất có 2 lần call Disk IO
- Duyệt secondary index
- Duyệt clustered index
Lưu ý với secondary index
- Một table có nhiều secondary index
- Có 2 thao tác disk IO
### 2.2.3 Nhược điểm của index
- Khi insert thì cần biết vị trí và cập nhật vào cấu trúc index --> chậm
- Kích thước  lớn --> tốn nhiều không gian tổ chức index
- Tạo và maintain index mất thời gian --> khi dữ liệu lớn sẽ mất rất nhiều thời gian
Câu hỏi: Vì sao Secondary index chỉ lưu giá trị primary key mà không lưu pointer tới địa chỉ record trong ổ đĩa ?
![[Pasted image 20260319002822.png]]
Vì địa chỉ (address) trên disk có thể thay đổi
Theo thời gian disk bị fractment (phân mảnh) --> muốn gom data lại defractment (gom mảnh) địa chỉ record thay đổi
Các thao tác khác cũng có thể gây thay đổi địa chỉ

# 3. Checklist tạo index
## 3.1 Dựa theo độ phổ biến (frequent)
- Thường được đưa vào trong cột tìm kiếm thường xuyên (email, tên, ...)
- Đưa vào trong các mệnh đề where
## 3.2 Dựa trên liên kết
- Tạo cho các cột khóa ngoại (tăng tốc độ join)
## 3.3 Trường hợp không nên tạo index
- Đối với các bảng quá nhỏ (tăng thêm bộ nhớ khi ghi dữ liệu --> ghi chậm)
- Dữ liệu không phân mảnh, giá trị giới hạn (VD: giới tính, trạng thái)

**Đọc nhiều --> ưu tiên index**
**Ghi nhiều --> hạn chế index**

