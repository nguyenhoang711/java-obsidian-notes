![[Pasted image 20260322003819.png]]

# Message broker
## 1. Request - Response Model
![[Pasted image 20260322004129.png]]
Mô hình truyền thống:
- Đồng bộ (Synchronous): service A gửi request, chờ service B trả về response
- Blocking: khi đang chờ service B --> bị block
- Coupling: 2 service A và B phụ thuộc nhau

## 2. Problem 1:
### 2.1 Problem context
Service A gửi message tới 3 services --> B, C và D.
--> Với cơ chế request, response pattern -->Service A phải gửi message 3 times
![[Pasted image 20260322004903.png]]
### 2.2 Solution 1
![[Pasted image 20260322004929.png]]
Có 1 message broker đứng giữa các service:
- Service A chỉ gửi 1 message
- B, C, D truy cập vào pull message về (pull/ push mechanism), hoặc broker push message cho B, C và D
- Asynchronous Communication (giao tiếp bất đồng bộ): không phải đợi
![[Pasted image 20260322005215.png]]
Giao tiếp bất đồng bộ:
- Đảm bào tài nguyên được sử dụng hiệu quả
- Không cần quan tâm đến thứ tự response về
## 3. Problem 02
### 3.1 Problem context
![[Pasted image 20260322140554.png]]
- Service A phải biết địa chỉ tất cả service trong system (hạn chế cho việc nâng cấp, mở rộng scalability)
- Service A call service B, nếu B fails --> A fails
![[Pasted image 20260322140936.png]]
Solution:
- Service A đóng vai trò producer, chỉ cần biết IP của MS broker
- Tương tự B, C và D đóng vai trò consumer, chỉ cần biết địa chỉ IP của Mess Broker
- Giảm coupling, dễ scaling, 1 consumer lỗi ko ảnh hưởng producer và consumers khác
## 4. Problem 03
![[Pasted image 20260322141150.png]]
- Phải thêm 1 request tới service thêm mới
- Load balancing tới Service A là impractical (không thực tế)
![[Pasted image 20260322142105.png]]
Solution:
- Pub/sub pattern, fan out mechanism: tất cả consumer đều nhận được message
	- Service A produce event push vào Topic X
	- Các service subscribe thì nhận message
	- Message broker đóng vai trò load balancer
- Không ảnh hưởng **existed flow**
## 5. Problem 4
![[Pasted image 20260322143129.png]]
### Problem context
- Ta muốn lưu dữ liệu IoT với data người dùng behavior lớn xuống DB
--> DB down
Solution
![[Pasted image 20260322143144.png]]
- Giảm tải cho DB bằng cách kiểm soát luồng dữ liệu
- Rate Limiting + Message queue
Reliability: hệ thống ổn định, an toàn
## 6. Message broker
Software component đóng vai trò là intermediary layer, trao đổi message giữa các services, system.
Pros:
- Asynchronous Communication
- Decoupling
- Scalability
- Reliability
Cons:
- Complexity: khi tăng 1 componenet trong system tăng phức tạp 
- Latency: gửi message qua bên trung gian --> tăng latency
- Operation Overhead
- Cost
# Kafka
## 1. Definition
### 1.1. Different between event and response

| Criteria      | Response                       | Event                                             |
| ------------- | ------------------------------ | ------------------------------------------------- |
| Scope affect  | specific client (sent request) | Business fact have value to more than one service |
| Changable     | Variable (over time)           | Just a thing happened (immutable)                 |
| Communication | Synchronous communication      | Asynchronous communication                        |
### 1.2 Event Streaming
- Streaming: continuous flow của data (event) trong real-time
- Event streaming
	- Cách để event in/out
	- Store events ?
	- Ordering of events?
- Streaming != Stream processing
	- Stream processing: chỉ xử lý data trên 1 stream cụ thể
	- Streaming: luồng dữ liệu hoặc event
### 1.3 Platform
Kafka là platform cung cấp dịch vụ:
- Data integration
- Stream processing
- Ecosystem và tools,
Các API:
- **Producer API**: cung cấp tính năng gửi tin
- **Consumer API** : cung cấp tính năng nhận tin
- Streams API: stream processing
- Connect API: tích hợp hệ thống big data, database
- AdminClient API: cung cấp APi quản lý cụm (cluster)
## 2. Use cases
## 2.1.1 Case study: Fraud detection
### 2.1.2 Request-Response processing
![[Pasted image 20260322153524.png]]
Mỗi request, query để sum transactions bằng account number
Pros:
- Đơn giản
- Data integrity (dữ liệu đảm bảo toàn vẹn)
- Low latency khi low throughput (thao tác sum tốn kém)
Cons:
- Higher latency khi có nhiều throughput
- Low throughput (do latency từng transaction tăng)
### 2.1.3 Batch processing
Có job chạy để tổng transaction trên từng tài khoản
Pros:
- Simple
- Low cost
- High throughput
Cons:
- Data integrity (có độ trễ vì transaction thêm liên tục)
### 2.1.4 Stream processing
![[Pasted image 20260322224721.png]]
Source stream trong Transaction Topic
Tính tổng transaction rồi map tới từng key account
--> lọc ở trong rule hit topic --> sink lại các giá trị hit để mark vào DB
### 2.1.5 So sánh 3 phương pháp

| Criteria   | Request/ Response Processing | Batch processing                | Stream processing           |
| ---------- | ---------------------------- | ------------------------------- | --------------------------- |
| Latency    | Low (real-time)              | High (depend on scheduled time) | **Medium (near real-time)** |
| Throughput | Low (calculate)              | High                            | **High**                    |
### 2.1.6 Vì sao ko apply Stream processing cho mọi scenario?
Cost: chi phí cao
Error handling: khó (retry, check log)
Complexity: high (phức tạp cao, long learning-curve)
## 2.2 Log aggregation
![[Pasted image 20260322230714.png|697]]
Ứng dụng trong ghi log tổng hợp.
- Tất cả log tập trung ghi vào topic
- Topic push và sink vào DB
## 2.3 Log shipping
![[Pasted image 20260322230748.png]]
Trong hệ thống **CQRS** (tách nghiệp vụ ghi và đọc ra)
- Hệ thống command handler, đầu ghi vào MasterDB
- Logs sẽ được đẩy vào topic
- Topic sẽ thực hiện gửi event ghi log vào View DB
- Đầy đọc sẽ đọc vào ViewDB này
## 2.4 Event-Driven Architecture (EDA)
![[Pasted image 20260322231150.png]]
Stream Kafka luôn là đầu vào hoặc đầu ra của 1 stage
Stage: stream processing
Thường được áp dụng trong hệ thống Big Data dùng để report hoặc phân tích dữ liệu
## 3. Worst cases
### 3.1 Simple Request - Response 
- Examples:
	- Get product information
	- Get user profile
- Reason
	- Complex to implement the Request - Response mechanism with Kafka
![[Pasted image 20260322232334.png]]
VD: Producer A gửi command tới topic X, topic gửi cho Consumer C nhưng ko trả về gì
Tích hợp Kafka chỉ là bước chung gian chứ ko thể là bước đầu cuối của Request-Repsonse Mechanism
Alternatives:
- Rest API
- gRPC
### 3.2 Low latency requirement
Example:
- Trading
- Execute a payment between 2 systems
- Check permission
Reasons:
- Low, predictable latency là yêu cầu cần thiết (kafka không thể đáp ứng)
- Kafka latency là unpredictable (tùy thuộc số lượng data trong stream)
Alternatives:
- Redis Pub/Sub
- Socket
- gRPC
### 3.3 Non-requirement Real-time data
Example:
- Long-running job
- Reports
Reason:
- High operational overhead
- Cost
Alternatives:
- Cron job
- ZeroMQ, RabbitMQ (message queue đơn giản hơn)
### 3.4 Dự án nhỏ và vừa
Reasons:
- Cost
- Learning efforts
- Some non-functional requirements are skipped (yêu cầu phi chức năng)
Alternatives:
- Redis Pub/Sub
- ZeroMQ, SQS

## Tóm tắt
- Apache kafka là distributed event streaming platform
- An event is a business fact that immutable and have value to more than one service
- Kafka has pros and cons of a message broker. Ngoài ra có chức năng: High-throughput data streaming and long-term data retention
# 3. Core concept
## 3.1 Architecture
![[Pasted image 20260323001438.png]]
- Broker
	- Là kafka cluster có chứa multiple brokers
	- Responsible for nhận/ gửi message và lưu trữ message
	- Điều phối group consumers
	- Chọn ra consumer leader
- Zookeeper:
	- Quản lý brokers và quản lý chung trong cụm
## 3.2 Topic
Là luồng các message/ event không có điểm dừng
Producer ghi message vào topic, consumer đọc message từ topic
1 cluster có nhiều topics, số lượng tùy vào config
## 3.3 Partition
![[Pasted image 20260323001847.png]]
Một topic là 1 nhóm partitions
Kafka sử dụng partitions để tăng throughput và spread (phân tán) messages tới tất cả brokers
![[Pasted image 20260323001951.png]]
Mỗi broker xử lý độc lập và song song
VD: cụm cluster có 3 broker, topic X có 3 partition được trải đều trên 3 brokers.
## 3.3 Partition
- Được sắp xếp có thứ tự
- Consumer sẽ đọc giá trị theo đúng thứ tự được ghi vào 
![[Pasted image 20260323003116.png]]
- Partition giuống 1 queue FIFO
Với đầu ghi vào (producer)
- Case 1: producer write to topic with only one partition (message theo đúng thứ tự ghi vào topic X)
![[Pasted image 20260323003316.png]]
- Case 2: nhiều producer ghi vào trong topic chỉ 1 partition
![[Pasted image 20260323003343.png]]
- Thứ tự ko đảm bảo (tùy thằng nào đến trước)
![[Pasted image 20260323003553.png]]
- Case 3: ghi vào multiple partitions
### 4. Offset
![[Pasted image 20260323225723.png]]
- Là primary key cho record/ message trong partition
- Ghi sao khi writen record/ message to partition
- Start from 0
- Fast lookup: O(1)
- Advanced: gap between two adjacent records (offset gap)
### 5. Records
![[Pasted image 20260323230022.png]]
Trong kafla nó là message, event
Record là **immutable**
Có 6 thuộc tính:
- Key
	- Optional, non-unique
	- Nhóm các records (user_id)
	- Tùy chọn kiểu dữ liệu
- Value
	- Phần quan trọng nhất: value describe event
	- Nullable
	- Tùy kiểu dữ liệu
![[Pasted image 20260323231713.png]]
Record có 6 tính chất
- Header: các cặp key-value free style 
- Partition: nơi records ở bên trong
- Offset: 64-bit signed để locate record trong partition
- Timestamp:
	- Được set bởi broker (khi ghi vào broker)
### 6.1 Producer
Producer tạo message/ records và ghi vào topics
Sử dụng Producer API (viết bởi ngôn ngữ lập trình chọn) để implement producer
Vì sao cần Producer API: có các cơ chế phức tạp --> cần để đơn giản hóa
- Partitioning
- Serialization
- Compression
- Retry
#### 6.1.11 TH partition được tạo bởi ProducerRecord
![[Pasted image 20260323235044.png]]
Partition luôn phải có để xác định insert record vào partition nào

#### 6.1.2 Key khác null
Key sẽ được hash để xác định partition insert vào
Những record nào same key sẽ được sent vào same partition
![[Pasted image 20260323235255.png]]
Các record cùng key sẽ cùng trên 1 (key null nằm rải rác)
#### 6.1.3 key == null
![[Pasted image 20260323235411.png]]
- version < 2.4.0 : sử dụng round-robin strategy
- version > 2.4.0: Sticky (improved mechanism)
## 6.2 The number of partitions changes
Các Message cùng key có thể đặt cùng nhau trên partition khác với trước đó
![[Pasted image 20260323235821.png]]
VD:
Do số lượng partition thay đổi nên công thức tính chọn partition insert vào cũng bị thay đổi
## 7. Consumers
Read message/ records từ topic sử dụng cơ chế pull/push
Consume không remove khỏi topic
Cần có Consumer API để giảm thiểu sự phức tạp vì có các cơ chế:
- Consumer Group
- Committing Offset
- Rebalance
- Liveness
### 7.1 Consumer group
Mỗi consumer đều nằm trong 1 consumer group
Consumer Group đều có cơ chế load-balancing
Phân bố partition thường là đều nhau với consumers trong consumer group
![[Pasted image 20260324001756.png]]
VD: Mỗi consumer đều gán số partition bằng nhau

Khi start 1 consumer mà không xác định consumer group  --> tạo **random consumer group** tự động subscribe topic
![[Pasted image 20260324002216.png]]
Khi mà tạo consumer xác định group name --> consumer đầu tiền sẽ hứng tất cả message trong topic và consumer đó 
![[Pasted image 20260324002430.png]]
First consumer trong group sẽ subscribe topic, sẽ nhận tất cả partitions
Khi có consumer thứ 2 join group, cơ chế **rebalane is triggered**. Khi đó second customer sẽ lấy nửa partition từ first consumer
![[Pasted image 20260324002740.png]]
Có thể có 1 partition gán cho nhiều hơn 1 consumer?
![[Pasted image 20260324002848.png]]
Không thể vì cơ chế hoạt động kafka chỉ cho phép tối đa 1 partition với 1 consumer
Nếu số lượng consumer > partition --> không được 
# 8. Replication (Đồng bộ)
## 8.1 Problem context
![[Pasted image 20260325222224.png]]
Partition có broker bị lỗi, mất kết nối
- Durability: mất data
- Availability: không thể ghi hoặc đọc vào partition trong broker
## 8.2 Solution (Replication)
- Đồng bộ: đáp ứng được yêu cầu về durability và availability
- A replica là copy of a partition
- Replicas được phân bố giữa các broker (mỗi broker giữ bản replica của broker còn lại)
![[Pasted image 20260325222547.png]]
## 8.3 Replication factor
- Là số lượng replicas, được cấu hình ở **topic level**
- Replication factor default is 1 (Tối ưu: >1 và nhỏ hơn kích thước của **cluster**)
## 8.4 Leader & Follower
- Trong số các replica của partition, thằng nào là gốc gọi là leader ( duy nhất ) và còn lại là follower
- Các replicas được đồng bộ cách asynchronously
![[Pasted image 20260325223610.png]]
- Khi mà follower nào đồng bộ xong với leader --> In-Sync replica (ISR). Bao gồm cả leader 
## 8.5 Leadership
Producer ghi vào leader, consumer chỉ đọc từ leader partition
- Consumer có thể đọc từ 1 replica gần nhất (cấu hình consumer) --> lưu ý cần nắm cách thức hoạt động của nó
