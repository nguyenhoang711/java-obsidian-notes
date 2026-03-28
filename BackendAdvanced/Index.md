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
Theo thời gian disk bị fracment (phân mảnh) --> muốn gom data lại defractment (gom mảnh) địa chỉ record thay đổi
Các thao tác khác cũng có thể gây thay đổi địa chỉ
## 2.3 Characteristics
### 2.3.1 Characteristics
What is difference between key & index ?
- Key: nghĩa là ràng buộc, là behavior của 1 column
VD: primary key có tính chất là non nullable và unique
- Index: là cấu trúc dữ liệu mà **facilitates data search** (phục vụ việc tìm kiếm dữ liệu) trong table
### 2.3.2 Primary index
![[Pasted image 20260327000021.png]]
Primary index là kiểu index chỉ cho phép giá trị unique cho từng dòng dữ liệu
Khuyến khích sử dụng key sequential --> tối ưu cho việc đọc và ghi
VD: số tăng dần --> thao tác update vào B-Tree hạn chế trường hợp thay đổi cấu trúc B-Tree
khi key tăng dần thêm vào ô trống (không phải thêm node vào cây)
- Hạn chế thao tác phân mảnh (bản ghi tăng dần nằm liền kề nhau)
### 2.3.3 Unique index
CREATE UNIQUE INDEX index_name ON
table_name(index_col1, index_col2, ...)
## 2.4 Number of columns
### 2.4.1 Composite index
![[Pasted image 20260327000456.png]]
- Gồm nhiều cột
- càng nhiều cột đánh index, càng tốn space
- Nếu nhiều quá thì không giúp query tối ưu hơn
Mô tả Composite index:
![[Pasted image 20260327000611.png]]
n column được đánh index thì được sử dụng làm key cho 1 node
- Đến node lá (có cột màu đỏ) --> là primay key cầm đi để query trong clustered index
Đối với composite key sắp xếp:
- Theo cột đầu tiên index: sắp xếp tăng theo chiều trái sang phải
- Theo cột thứ 2: sắp xếp tăng dần (đk chung value cột 1) nested sort
- Cột n tương tự
![[Pasted image 20260326230104.png]]
Có 2 câu 1 và 4 sử dụng index (vì thứ tự trùng với composite key)
Lí do: 
- Giá trị key country thực hiện order đầu tiên --> phải chứa điều kiện về country thì mới sử dụng được index (cửa ngõ là country)
- Thứ tự key được gọi trong logic AND không quan trọng
Làm cách nào để đánh giá độ hiệu quả của 1 index? (VD index trên)
Với các cột tần suất query như nhau:
- Cột nào có số giá trị riêng biệt nhiều nhất thì đánh index hiệu quả tốt nhất (high cardinality)
Cách để định nghĩa thứ tự các cột trong index? (VD: index trên)
- Cột nào có **high cardinality** đưa lên đầu? B+ Tree tạo ra cây với nhiều nhánh, và mỗi node có ít key
VD: ![[Pasted image 20260328150326.png]]
Đánh index cho gender --> tạo ra 2 branch (male và female) và mỗi node có rất nhiều key
--> khi tìm giá trị theo index --> duyệt ngang qua các key rồi mới sang node sau --> rất chậm

Example **(name, province, country) index**
**Question 2: Composite index (a, b)**
![[Pasted image 20260328150719.png]]
1. Có phải tất cả query đều dùng index?
Q1: use (a) --> a query range còn b chỉ có 1 giá trị --> ko dùng index của (b)
Q2: use (a, b) --> a query range với (a = 1 AND b = 2) và (a > 1 and b = 2) (trường hợp cùng a thì b ko có ý nghĩa, nhưng khác a thì b dùng để xác định)
2 giá trị tuyệt đối --> build ra được key
Q3: use (a, b) --> 
2. Có bao nhiêu cột được sử dụng?
### 2.4.2 Covering index
Là index chứa tất cả các cột trong câu SELECT
Trả ra kết quả truy vấn sử dụng chỉ index, giảm I/O operations --> cải thiện performance
Recommended: <= 5 columns
![[Pasted image 20260328152115.png]]
VD: SELECT lấy 3 cột firstName, lastName và Country nhưng nó được đánh làm Index --> select secondary index là đã ra giá trị cần lấy rồi, ko cần vào **clustered index**
Index đã chứa giá trị rồi, giảm thiểu số lần gọi disk I/O
## 2.5 Others
Classified by data structure:
- Fulltext index
- Spatial index (Postgres): R-Tree index theo không gian (tìm điểm xung quanh trong map)
- Bitmap index:
	- Use case: read heavy on counting low-cardinality column (cáu trúc dạng mảng với các phần tử dạng 0 1 --> count nhanh chóng)
	- VD: counting số người trong hệ thống là nam, là nữ (gender)
# 3. Practices
## 3.1 Indexes in Use
When to used indexes?
- Read heavy: đọc dữ liệu thường xuyên với WHERE, JOIN, GROUP BY, ORDER BY
- Find small set of records (bảng 10tr bản ghi lấy 2k)
- Tìm kiếm với unique restrictions, VD product_id
When to not use indexes?
- Table có quá ít dữ liệu --> có index cũng ko tăng performance lắm (thêm index tức là thêm level vào tree --> có khi làm chậm hơn)
- Very low cardinality (VD: chỉ đánh index vào cột low cardinality --> mỗi node lá chứa toàn bộ nữ, toàn bộ nam --> vẫn lớn --> ko đem lại hiệu quả)
- Chỉ ghi nhiều, ko đọc nhiều lắm
## 3.2 Failures
### 3.2.1 Index Failures 01
Index trong cột 'name'
- SELECT * FROM users WHERE name like 'ronin%';
- SELECT * FROM users WHERE name like '%ronin%';
- SELECT * FROM users WHERE name LIKE '%ronin';
Đáp án: query 1 ăn index. 
![[Pasted image 20260328172407.png]]
Giải thích: tree kí tự tìm kiếm --> duyệt qua từng ký tự từ trái sang phải
### 3.2.2 Index Failures 02
Index trong cột 'id'
SELECT * from users WHERE id = 1 OR age = 18
Index ko active vì ko có composite index cho (id, age) --> điều kiện OR khiến cần 2 cột mới identify được --> cần duyệt toàn bộ bảng để lấy ra age = 18.
### 3.2.3 Index Failures 03
1. column 'id' int
SELECT * FROM users WHERE id = '10';
Đối với relational DB --> tự động ưu tiên convert string sang số --> compare OK
2. column 'id' varchar
SELECT * FROM users WHERE id = 10;
Tự convert record trường id từ string --> number --> mất index
### 3.2.4 Index Failures 04
Index on column 'name'
SELECT * FROM users WHERE length(name) = 6;
--> bởi index lưu trữ origional value --> ko active index
Oracle, Postgres, MySQL 8.0+ hỗ trợ index cho function
### 3.2.5 Index Failures 05
Index on 'id' column
SELECT * FROM users WHERE id + 1 = 10;
SELECT * FROM users WHERE id + 0 = 10 - 1;
--> index lưu giá trị original trong index field, 
# 4. Checklist tạo index
## 4.1 Dựa theo độ phổ biến (frequent)
- Thường được đưa vào trong cột tìm kiếm thường xuyên (email, tên, ...)
- Đưa vào trong các mệnh đề where
## 4.2 Dựa trên liên kết
- Tạo cho các cột khóa ngoại (tăng tốc độ join)
## 4.3 Trường hợp không nên tạo index
- Đối với các bảng quá nhỏ (tăng thêm bộ nhớ khi ghi dữ liệu --> ghi chậm)
- Dữ liệu không phân mảnh, giá trị giới hạn (VD: giới tính, trạng thái)

**Đọc nhiều --> ưu tiên index**
**Ghi nhiều --> hạn chế index**

Sum Up:
- Giảm thiểu số lượng index
- Primary key ưu tiên để tự tăng vì:
	- KO cần move existing data
	- Giảm page split, fragmentation
- Index là best cho giá trị NOT NULL
	- Value compare not be complicated
	- NULL meaning worthless, 
- Covering index:
	- Reduce a lot I/O operations
- Prefix index:
	- Reduce size of index storage
- Regularly optimize the indexes
	- B+Tree có thể bị imbalanced overtime --> rebuild index
- Prevent index failure

## 3.4 Note
Index giúp giảm thiểu số lượng search operation (On -> OlogN)
Index giảm thiểu số lượng gọi tới disk I/O
MySQL != Postgres
- Architecture: Postgres value nằm ở bất kì node nào

# Recap
- Tất cả node lá linked together --> best for range search
- Accessing data sử dụng secondary index cần ít nhất 2 lần đọc. 1 lần đọc từ secondary index và another access tới clustered index
- Tận dụng composite index, convering index --> tránh tạo nhiều index lẻ
- High cardinality first