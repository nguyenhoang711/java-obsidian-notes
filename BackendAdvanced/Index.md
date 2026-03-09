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
