# 1, Estimation
## 1.1 Mục đích của việc estimation (GOAL)
- làm rõ vấn đề (Functional requirement, non-functional requirement) --> bổ trợ cho bước làm rõ vấn đề
Từ đó biết được kích cỡ của hệ thống (storage, bandwith, memory)
- Đánh giá được thiết kế hệ thống được bao nhiêu % muc tiêu đề ra
- Xác định được potential bottlenecks của hệ thống (về tính concurrency, storage lớn, hot data lớn) --> cơ chế  xử lý (caching, size of memory)
- Decide whether DB should be partitioned (shards)
## 1.2 Principles
- Thực tế estimation không hề đơn giản --> có rất nhiều biến số ảnh hưởng (unknown parameters) chỉ ước lượng tương đối
- Cần có mục tiêu (goal): để biết kích thước của design
- Ước tính dựa trên kinh nghiệm, hệ thống có sẵn làm references
- 80/20 principle: 
	- 
# 2. Trade-off
# 3. Design Principles
