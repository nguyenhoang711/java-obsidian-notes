# 1. Introduction
Cung cấp nhiều cấu trúc dữ liệu--> phục vụ nhiều bài toán khác nhau
Cung cấp tính năng: Transaction, persistence, Lua script, Redis Cluster, pub/sub
Use-cases: caching, key-value database, message queue
## 1.2 Redis vs Memcached
- Similarities
In-memory database và đều được sử dụng khá phổ biến
Cơ chế expire dữ liệu
High-performance
- Differences
Redis: single thread --> run on single core
Memcached: multi-core

| Criteria         | Redis                                               | Memcached                      |
| ---------------- | --------------------------------------------------- | ------------------------------ |
| Thread           | Single-thread                                       | Multi-core (run multi threads) |
| Operations       | read operations better<br>(> 16GB giảm performance) | Better on Write operation      |
| Data structure   | Variety of data structures                          | hạn chế                        |
| Data persistence | Support data persistence (lưu dữ liệu xuống ổ đĩa)  | không hỗ trợ                   |
| Cluster          | Hỗ trợ cluster --> mở rộng                          | Không hỗ trợ mở rộng           |
| Other features   | Transaction, pub/sub, LUA script                    |                                |
## 1.3 Architecture
### 1.3.1 Master-slave
Replication: bất đồng bộ (client ghi xong master --> response cho client luôn)
Replication bất đồng bộ sau
![[Pasted image 20260404150757.png]]
Ưu điểm:
- Scalable cho đọc
Nhược điểm:
- Data inconsistency (CAP: chỉ chọn 2 trong 3 tính chất (availability and partical failure))
- Failover: nếu 1 master down thì slave nào lên làm master mới?
Solution:
- Sentinal
- Cluster
### 1.3.2 Sentinal (lính gác)
- Theo dõi hệ thống master and slave server (con nào đã đồng bộ xong, chưa xong)
- TH master down --> chọn 1 slave lên làm master 
![[Pasted image 20260405005415.png]]
TH có nhiều con slave đủ điều kiện làm master mới --> random

Ưu điểm:
- Giải quyết failover
Nhược điểm:
- Tăng kích thước hệ thống (node sentinal)
- Data inconsistency
- Chỉ 1 master --> hứng tất cả thao tác ghi
### 1.3.3 Cluster
![[Pasted image 20260405153752.png]]
Phân tán data chứa trong nhiều server
Master hoạt động độc lập với nhau --> dữ liệu riêng
- Dữ liệu chia tương đối đều trên các node master
Ghi vào nhiều master, có khả năng mở rộng write/read
Có thể đọc từ các node slave
Ưu điểm:
- Giải quyết được vấn đề Failover (các node giao tiếp với nhau với giao thức riêng gossip: kiểm tra trạng thái từng node) tất cả các node được broadcast tất cả trạng thái node còn lại. Các node Slave có thể giao tiếp nhau để make sure là node Master trạng thái như đúng k --> giao thức gossip (đồng thuận)
- Giảm phụ thuộc vào 1 node master duy nhất
- Scalable for read and write
Nhược điểm:
- Data inconsistency
- Nhiều node --> resource operational overhead
Highly recommended: Cluster --> tuy nhiên tốn tài nguyên
Trong thưc tế: chỉ cần 1 node ghi thì **Sentinel** đáp ứng được --> vì node sentinel nhẹ
Sentinel giống như Zookeeper --> là 1 chương trình khác ngoài Redis
#### Hash Slots
- Handle mapping relationship giữa data và các node
- Một cluster sẽ có **16384 hash slots**
- Mỗi con master sẽ nắm 1 dải hash slots
![[Pasted image 20260405163732.png]]
Khi connect tới cluster --> map giữa instance_id (key) và hash slots (value)
1. Write data
![[Pasted image 20260405211447.png]]
Tính ra hash slots cần ghi vào: hash key module hash slots --> dựa vào map --> sent data tơi instance đích
Dữ liệu sau khi hash được phân bố đều
Một slot có thể chứa nhiều key --> con số trên do turning để chọn ra con số tối ưu.
## 1.4 Redis Pub/Sub
![[Pasted image 20260428101926.png]]
Cung cấp pipeline để gửi message tới subscriber lắng nghe kênh đó
Pipeline: hỗ trợ đóng gói nhiều command vào 1 lệnh duy nhất --> tiết kiệm bandwith
Khác với transaction,  pipeline không thực thi các command liên tục và cũng ko chờ command trước hoàn thành để gọi command tiếp.
# 2. How Redis works ?
## 2.0 Redis Principles
Redis không hỗ trợ  ACID đầy đủ
- Atomic: mỗi node chỉ dùng single-thread --> mỗi command chạy riêng lẻ và không bị ảnh hưởng bởi command khác.
- **Non-Consistent**: ghi dữ liệu vào Redis có thể bị lỗi và mất bất kì lúc nào
- Isolation: hỗ trợ Transaction (không gioonbgs transaction của DBMS), vì single-thread --> transaction không làm ảnh hưởng transaction khác
- **Semi-Durability**: vì lưu trữ trên memory --> node bị shut down --> mất mát dữ liệu
## 2.1 Why is Redis so fast?
### 2.1.1 Redis bottleneck
- Giới hạn memory
- Network bandwidth
### 2.1.2 Why is Redis so fast?
- Lưu ở memory --> truy xuất dữ liệu nhanh, do memory là phần cứng --> giới hạn
- Single thread model
	- CPU is not a bottleneck: tác vụ nhỏ nhẹ (get/set), tính toán cơ bản --> không cần đa luồng (workload nhỏ) 
	- Tránh multi-thread switching (context switching) --> throughput tăng đáng kể
	- Multi-thread app cần cơ chế bảo toàn dữ liệu (lock hoặc synchronize mechanism) --> bug prone, complexity --> difficult to gain performance
- Multiplexing I/O
	- HĐH có 5 I/O model trong đó có multiplexing
	- One thread process multiple IO streams
	- VD: spring framework mỗi connection tạo ra 1 thread để xử lý req --> thực tế hạn chế tạo riêng từng thread cho 1 req (tốn tài nguyên, không tối ưu --> multiplexing)
- Efficient data structures
### 2.1.3 Note
Cách để maximize CPU usage?
- Tạo nhiều instance Redis trên máy --> tận dụng tất cả core trong CPU
- Cần CPU có xung nhịp lớn --> thực hiện đc nhiều phép toán trong 1 đv thời gian
- Sau Version 6.0 --> ko hoàn toàn single-thread 
	- Main thread execute command
	- Other thread handle data persistent, network I/O
## 2.2 How Redis store data?
Ngoài lề: tool tracing các bước khi chạy 1 API trên distributed service
- APM: New Relic (mất phí)
- Temporal: quản lý trạng thái khi call sang service khác
- OpenSource khác: Jenkins
![[Pasted image 20260405233821.png]]
Dictionary là hash table --> quick search O(1)
Sử dụng hash table --> lưu các pointer tới từng key và value
Giải quyết vấn đề phân mảnh, giải quyết vấn đề tốt hơn
## 2.3 Expired Deletion
### 2.3.1 How to dertermine if the key has expired?
![[Pasted image 20260409003757.png]]
Phần khoanh tròn đỏ --> dict chứa key value
Redis chứa 2 dict
- Key dictionary
- Expire dictionary (chứa thời gian hết hạn của key)
Khi Redis truy xuất theo key:
- Đầu tiên key kiểm tra còn hạn ko (trong expire dictionary - chứa expire time)
- Nếu ko --> key ko có TTL --> đọc như bình thường
- Nếu có trong expire dict --> kiểm tra time to live
	- Nếu chưa đến hạn: truy vào dict key
	- Nếu đã đến hạn: return null (expire dict) --> rồi xóa key (async)
Nếu mình cứ để dữ liệu đã expire vào dictionary --> tốn bộ nhớ --> cần cơ chế dọn dẹp chủ động
### 2.3.2 Strategies xóa dữ liệu
- Cách trên gọi là bị động (gọi key rồi check rồi xóa)
ưu/ Nhược điểm:
Key ko bao h được truy cập --> tốn tài nguyên
- Chủ động: xóa trước khi key được get (periodic job)
Random select 20 keys from expired dictionary
Cái nào đến hạn thì xóa đi
Nếu số key xóa >25% --> quay lại bước đầu random 20 keys ....
Ưu/ Nhược điểm:
- Active job long --> active job block các req khác (single thread)
--> Solution: set timeout để job ko chạy quá lâu
- Combine passive way + active way
Problem: finding expired key is not effective
New approach: Expired key is stored in a Sorted Set (ZSET) sort theo time hết hạn
--> find expired key hiệu quả hơn
#### Chuyện gì xảy ra khi memory Redis đầy?
### 2.3.4 Memory Eviction (Cơ chế xóa trên memory)
- **Noeviction (Default)**: không xóa dữ liệu trên memory --> hết storage ko tiếp nhận req ghi 
- Random: xóa các key random
- **TTL**: ưu tiên xóa key có time gần hết hạn nhất
- **LRU (Least recently used)**: xóa key ít truy cập gần đây
- **LFU (Least Frequently used)**: ưu tiên key ít truy cập nhất (tần suất)
Cách này cần đẻ thêm trường lưu số lần truy cập
--> hiệu quả về mặt xóa dữ liệu, tuy nhiên có thể overhead
## 2.4 Data Persistence
### 2.4.1 AOF (Append Only File)
![[Pasted image 20260409235336.png]]
Ưu điểm:
- Tránh additional checking gây overhead
- Không block luồng ghi vào Redis: do sync vào disk là bất đồng bộ
- Less data loss
Nhược điểm:
- Data loss
- Có thể **block other operations**
- Nếu có nhiều AOF logs --> **slow recovery**
### 2.4.2 RDB (Redis database)


# 3. In practices
## 3.0 Question
Có object như sau:
{uid, username, email, age}
Requirements:
- C1: access username only
- C2: update age only
- C3: get all fields
Kiểu cấu trúc dữ liệu nào đáp ứng được các y/c trên?
- Hash (key: uid, value: hash_map<field, value>)
HGET 34 username --> value2
HGETALL
Ưu điểm:
- Không cần patch sang object
- String Json
Ghi vào path ra object json --> đọc ra path lại dạng user object --> đọc lấy ra username
{
	"uid": 34,
	 "username": "john"
}

## 3.1 Data structures
### 3.1.1 String
Implementation: SDS (simple dynamic String)
Application:
- Cache object (JSON)
- Count
- Share session
- **Distributed lock (important)**
### 3.1.2 List
Mảng các chuỗi
Applications:
- Store list of elements
- Message Queue
Limitation:
- Giới hạn độ dài
- Không hỗ trợ multiple consumers
- Dữ order kém
### 3.1.3 hash
Implementation: **Hash table**
Applications:
- Store objects mà frequently access hoặc dùng để update attributes của objects
- Shopping cart
	- Add item: HSET cart: {user_id} {product_id} 1 (giỏ hàng user X, hash có key là product_id và số lượng là 1)
	- Increase quantity: HINCRBY cart:{user_id} {product_id} 1
	- Total number of items: HLEN cart:{user_id}
	- Delete item: HDEL cart:{user_id} {product_id}
	- Get an item: HGET cart:{user_id} {product_id}
	- get all items in shopping cart: HGETALL cart:{user_id}
### 3.1.4 Set
Chứa phần tử ko trùng lặp
Implementation: hash table
Applications:
- Deduplicate phần tử trùng lặp
- Function: difference, union (nối lại) and intersection (trùng lặp)
- Likes, common followers
Limitation: 
- Length max: 2^32 -1 element
- Độ phức tạp Tính toán tương đối cao --> block the main thread
### 3.1.5 Sorted set (ZSET)
Không trùng lặp và được sắp xếp
Implementation: **Skip list** (giống tree)
![[Pasted image 20260410232255.png]]
Cách tìm phần tử số 13
- Đi từ 1. kiểm tra 1 < 13 --> sang phải
- Gặp 9 < 13 --> sang phải
- Gặp 31 > 13 --> sang trái + đi xuống
- Xuống layer dưới + sang phải 18 > 13 --> xuống + sang phải
- Gặp 13 (layer cuối)
Duyệt tương tự cây --> search O(logN)
Applications:
- Sorting
- Ranking, leaderboard
## 3.2 Practices
### 3.2.1 How to implement a delay task?
#### Context:
- Khi ordering taxi cần có cơ chế ko có tài nhận xe trong 10 phút thì hủy chuyến tự động
- Thời điểm đó không có tài nhận chuyến
System có job chờ trong 10 phút tính từ thời điểm tạo order --> ko ai nhận sẽ automatic cancel
#### Implementation: Sorted set (ZSET)
![[Pasted image 20260410233013.png]]
Lưu dữ liệu như sau:
Key: ngẫu nhiên
Value: order_id
Score: thời gian hết hạn order
Dữ liệu được sắp xếp theo thứ tự tăng dần timestamp
Sau mỗi giây bốc từ trong ZSET 1 giá trị:
- Nếu score(timestamp nhỏ hơn hiện tại) --> expire --> xóa đi
- Lần lượt với các order tiếp theo
### 3.2.2 How to deal with big key in Redis?
Big key:
- Value of string với hơn 10kB
- Number elements trong hash, list, set và ZSet vượt quá 5000
Effects:
- Time-consuming --> timeout
- Network congestion (băng thông)
- Block the worker thread when deleting (single thread in Redis)
- Memory  ko phân bố đều (cluster: có 1 số node big key)
Approach:
- Tìm ra big key: SCAN, HLEN, SCARD, MEMORY USAGE command để tìm big key
- Không sử dụng DEL (blocking)
- Asynchronous deletion: sử dụng UNLINK key O(1) --> sử dụng thread khác xóa, ko block thread chính (non-blocking) sau đó dữ liệu được xóa bất đồng bộ
### 3.2.3 How to put different keys trong các node khác nhau?
Limitation:
- Function ở trong 2 Set/ List khác node --> ko work
- 2 sets trong cùng 1 hash slot --> work
Làm sao để đảm bảo 2 key khác nhau đấy ở cùng hash slot?
Cơ chế: sử dụng **HashTag**
Đánh 1 giá trị chung cho cặp key-value
![[Pasted image 20260410235357.png]]
Như vậy thì lấy giá trị Hashtag đưa vào hash --> chung hash slot
Hạn chế này có trên các tính năng khác:
- Redis Transaction
- Redis Set
### 3.2.4 How to implement distributed locks
![[Pasted image 20260419001105.png]]
Problem context: Race Condition
- 2 reqs vào 2 instance của App concurrency
- Expectation: chỉ có 1 req đc xử lý tại 1 thời điểm (gọi xuống DB)
Phương án (Solution): Redis (**SETNX**) set not exist
Các bước:
- SET lock_key value NX PX 10000 (10000: time to live --> tránh dead lock)
	- Value: để số mặc định 
- DEL/ UNLINK lock_key (nhả lock)
Implement distributed lock:
![[Pasted image 20260419002153.png]]
- Thêm 1 layer: Redis để tạo distributed lock
- INstance nào đến trước sẽ tạo lock_key trước
- Rồi thao tác với DB
- Sau khi xử lý xong trong DB --> xóa lock_key vào Redis --> release khóa
### 3.2.5 Best practices
- Sử dụng Redis cluster (tốn bộ nhớ, tuy nhiên nếu cần High availability --> Redis SEntinel)  
- Tận dụng multi get, pipelines: do bandwidth của Redis nhỏ --> gom lại
- Tránh long-running tasks
- Normally set short TTL --> 
- Distribute TTL --> tránh việc tất cả key hết hạn cùng lúc
- Pick đúng data structure
- Tận dụng hash tag --> đảm bảo các tính năng hoạt động đc
- Nắm được time complexity của từng câu lệnh (doc Redis)

