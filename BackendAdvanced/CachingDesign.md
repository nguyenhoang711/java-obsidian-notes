# 1. Cache Introduction
- Là phần cứng hoặc phần mềm lưu trữ tạm thời dữ liệu
- Future request có thể faster
- Trong cache có thể là:
	- A copy of data từ data source
	- Kết quả của tính toán trước đó (earlier computation)
- Cache là shield của DB: thay vì gọi thẳng vào DB thì sẽ kiểm tra cache trước
Handle lượng lớn request so với DB
## 1.1 Cache node
Trade-off
- Performance or cost: đắt đỏ (RAM)
- Performance and consitency (sometime)
Cache suitable for **hotspot data** (dữ liệu được truy cập thường xuyên)
Cache hit: là metric quan trọng trong caching (VD: 10 lần req tới cache thì chỉ 9 lần lấy đc dữ liệu --> 90% cache hit)
- 80/20 principles để đảm bảo hit rate cao
VD: hệ thống có 1 tr users, nhưng 200.000 thường xuyên --> chỉ cache 200.000 người sử dụng thường xuyên --> high cache hit
Cache != buffer
- buffer là vùng nhớ tạm thời, nhưng dữ liệu này được di chuyển tới thành phần khác. (giống như queue, sau 1 khoảng thời gian bị deque)
- VD: ghi dữ liệu vào ổ đĩa, OS ko ghi trực tiếp memory app  và bộ nhớ máy mà ghi vào buffer OS. Rồi gom 1 cục ghi xuống ổ đĩa 1 lần
1.2 Where is Cache Used?
![[Pasted image 20260225232344.png]]
Local cache: lưu trong memory app (VD: memory config, ít thay đổi giá trị, size nhỏ)
VD: chỉ số lãi suất (thay đổi ít, truy xuất nhiều, size bé)
Remote cache: có thể kiếm soát được đối với dev backend
# 2. Caching strategies
## 2.1 Read strategies
![[Pasted image 20260225233835.png]]
Read-through
**Read-aside**
- App đọc dữ liệu từ trong cache
- Nếu cache hit thì trả ra luôn data
- Ngược lại cache miss --> read trong DB
- Trả dữ liệu về cho app --> ghi lại vào cache
Ưu / Nhược điểm
Ưu:
- Handle được khi cache bị lỗi
- 2 model trong DB và cache khác nhau --> flexible in data models
Nhược:
- Phức tạp hơn so với read-through
- Data inconsistency
## 2.2 Write strategies
Strategies:
- Write-through app-> cache --> db
- Write-back  app --> cache --> db
- Write-around
Cho phép không valid cache 1 cách bất đồng bộ (asynchronously)
Thao tác invalidate thực hiện sau hoặc trước khi ghi (nét đứt)
![[Pasted image 20260225235026.png]]
Ưu/ Nhược điểm:
Ưu điểm:
- Phân tách rõ giữa DB và cache (độc lập)
Nhược điểm:
- Không phù hợp cho frequent accessed data
- Data inconsistency (chưa thực hiện **invalidate cache**)
**Data inconsistency:**
- Là vấn đề chung giữa các strategies
- Solution: **Cache Invalidation**
- Cache Invalidation: xóa data mà không còn được sử dụng hoặc không còn hữu dụng
![[Pasted image 20260226221140.png]]

## 2.3 Cache Invalidation
Có các cách nào để xóa dữ liệu không cần thiết:
- Time-based (bị động TTL)
- Command-based (chủ động: app chủ động xóa cache)
- Event-based (khi update DB bắn ra event để cập nhật vào cache)
- Group-based
### Vì sao Cache Invalidation so Hard?
The reason for complexity:
- Timing: how long enough for Time-To-Live (TTL)
	- Ngắn quá: không hiệu quả
	- Dài quá: tốn memory
- Concurrency: race condition
- Data relationships
	- Giả sử dữ liệu A quan hệ với B, A thay đổi thì B cũng phải thay đổi.
	- Trong thực tế quan hệ dữ liệu rất phức tạp
- Cache not same with DB: nơi lưu trữ 
	- Nơi lưu trữ: cache ở mọi nơi, DB cố định chỗ
	- Phân tán --> 
- Hard to find root causes --> lưu cache ở khắp mọi nơi
### How to solve data inconsistency
![[Pasted image 20260226222244.png]]
**First try: Update cache**
![[Pasted image 20260226231420.png]]
Update cache hay không không giải quyết được vấn đề inconsistency
**Second try: delete cache**
![[Pasted image 20260226231514.png]]
2 lần write-around req gây ra race-condition:
- Req1 ghi vào db, chưa xóa cache
- Req2 chen vào vừa ghi vào db Age = 2 -> xóa cache (Age = 1)
- Req1 xóa cache (no value)
Kết luận: chỉ có data DB đúng, cache không giá trị --> xử lý được race data
Delete trước hay sau đều ko có dữ liệu trong cache
**Third-try: Read-Aside + Write-Around với Deleting cache (call RA + DWA)**
![[Pasted image 20260226231947.png]]
TH1: race condition (delete cache first)
+ Request 1: Write
+ Request 2: Read
![[Pasted image 20260226232217.png]]
- Khi request2 chen vào giữa Req1 --> race condition (cache wrong, DB right)
![[Pasted image 20260226232332.png]]
Delete cache later
- Request 2 đến trước đọc (miss hit) --> trọc vào DB read Age = 1
- Request 1 chen giữa ghi vào DB và cache (Age = 2)
- Request 2 update lại value vào cache (Age = 1)
Kết luận: **DB right, cache wrong** (cache: Age = 1, DB: age = 2)

#### 2 cách đều gây khả năng sai --> trong Write-Around nên delete Cache trước hay sau ?
Trả lời: vì tốc độ đọc ghi memory >> DB --> nên delete cache trước
![[Pasted image 20260226233046.png]]

![[Pasted image 20260226233213.png]]
Trả lời: update DB trước, delete Cache sau vì
- Cache luôn có tốc độ ghi nhanh hơn DB --> time range between write và read sẽ ngắn --> giảm thiểu tối đa khả năng bị chen vào giữa các req tới Cache

#### How we can mitigate the impact?
![[Pasted image 20260226233528.png]]
- Giảm thời gian TTL (time-to-live) --> tránh data gây ra race condition
- Giảm thiểu impact data inconsitency
# 3. Challenges
## 3.1 Reliable challenges
#### 3.1.1 Problem 1: No atomicity (Không nguyên tử)
![[Pasted image 20260226234702.png]]
Problem context:
- Customer X updates age từ 34 -> 44
- Nhưng Customer X complain rằng sau khảng thời gian chưa thấy cập nhật giá trị
- Đã sử dụng Read Aside + Write-Around
- Profile of customer X trong Cache
Nguyên nhân:
- Do đang ghi vào DB thì được, nhưng invalidation cache thì lỗi (ghi cache)
- Old value is in cache still --> take some time due to TTL
Solutions:
- **Retry: lưu lại cache**
- Subscrible to binlog of DB: tạo 1 event check binlog DB --> xem các thay đổi rồi trigger event update  cache
#### 3.1.2 Problem 02: Cache Avalanche (cache lở tuyết)
Problem context 01:
- Cache goes down for some reasons
- There are a large of requests at this time
- DB down
![[Pasted image 20260226235636.png]]
Đây là sự cố --> cần có phương án ứng phó từ trước khi sự cố xảy ra.
Solutions
- **Cache Cluster -> High Availability**: tạo thêm các cache khác để sẵn phòng khi cache A hỏng thì cache B ra hứng
- Rate Limit/ Circuit Breaker: tạo 1 queue để chờ load request, hoặc ngắt luôn chức năng bị down --> tránh ảnh hưởng use case khác
- Cache Recovery: cơ chế khôi phục cache (sau sự cố)
Problem context 02:
- A large amout of cache keys are expired
- There a large number of req to DB --> DB Down
![[Pasted image 20260227000504.png]]
Solutions:
- **Even distribution** for the expiration time: tạo cơ chế để làm sao ko cùng lúc expired tất cả keys (random TTL)
#### 3.1.3 Problem 03: Cache breakdown
Problem:
- A hotspot data được truy cập lượng lớn, cache expired
- Trỏ vào DB lấy --> DB down
![[Pasted image 20260227000928.png]]
Solutions:
- không set expire time cho nó
- **Background job để update cache periodically**: tạo 1 job tăng thời gian hết hạn, tránh bị key hết hạn
- Locking/ mutex: không cho truy cập đồng thời vào key đó

## 3. High traffic challenges
