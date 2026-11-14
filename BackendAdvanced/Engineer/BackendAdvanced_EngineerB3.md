# Tại sao cần cache
- Tăng tốc độ truy cập (In memory > Disk)
- Không cần tính toán lại
# Principles
![[Pasted image 20261011222351.png]]
1 redis node chỉ dùng **1 thread/ core** để xử lý logic
Nhưng vẫn hỗ trợ nhiều server kết nối tới Redis
Redis là CSDL trên bộ nhớ dựa trên key-value (không phải table)
Key là duy nhất.
Redis cũng hỗ trợ các thao tác nguyên tử (atomic operations)
# Cơ chế single-thread Redis
![[Pasted image 20261011234808.png]]
Có thể nhận nhiều yêu cầu cùng lúc nhưng các yêu cầu lần lượt xử lý chứ không xen vào nhau.
Có thể hình dung như một nhân viên thu ngân:

- Nhiều khách hàng có thể xếp hàng.
    
- Nhân viên xử lý một giao dịch thanh toán gồm nhiều thao tác liên tiếp.
    
- Khách tiếp theo phải đợi giao dịch hiện tại hoàn tất.

Các khách hàng không thanh toán đồng thời trên cùng một quầy, dù có nhiều người cùng yêu cầu.
## 1. Nhưng `MULTI` có nghĩa là Redis khóa dữ liệu ngay từ đầu không?

Không. Đây là điểm nhiều người mới học Redis thường nhầm.

Giả sử client A thực hiện:

```
MULTI
SET stock 10
SET order:1 "pending"
```

Ở thời điểm này, các lệnh `SET` chỉ được xếp hàng. Redis chưa thực hiện các thay đổi đó.

Trong lúc A chưa gửi `EXEC`, client B vẫn có thể thực hiện:

```
SET stock 20
```

Lệnh của B có thể được thực thi bình thường vì transaction A chưa bắt đầu thực thi nhóm lệnh.

Sau đó A gửi:

```
EXEC
```

Redis mới thực thi nhóm lệnh của A, và `stock` trở thành `10`.

Như vậy:

- `MULTI` không khóa các key.
    
- Việc xếp hàng lệnh không giữ độc quyền dữ liệu.
    
- Tính không xen ngang áp dụng trong thời gian Redis thực thi nhóm lệnh tại `EXEC`, chứ không phải suốt khoảng thời gian từ `MULTI` đến `EXEC`
## 2. Vậy Redis transaction có giống transaction trong Oracle không?

Không hoàn toàn.

|Tình huống|Oracle / PostgreSQL|Redis|
|---|---|---|
|Transaction đang mở nhưng chưa commit|Có thể giữ khóa và tác động đến giao dịch khác, tùy thao tác và mức cô lập|Các lệnh sau `MULTI` chỉ được xếp hàng|
|Nhiều transaction cùng sửa dữ liệu|Có cơ chế khóa, MVCC và mức cô lập tùy hệ quản trị|Nhóm `EXEC` được thực thi liên tiếp trên luồng xử lý lệnh|
|Phát hiện xung đột khi đọc rồi ghi|Có thể dùng khóa, mức cô lập hoặc cơ chế khác|Có thể dùng `WATCH` để phát hiện key bị thay đổi trước `EXEC`|
|Một lệnh trong transaction bị lỗi khi thực thi|Thường có thể rollback toàn transaction khi ứng dụng xử lý đúng|Không tự động rollback các lệnh đã thực thi thành công trong nhóm|

Redis không cần cơ chế khóa transaction giống database quan hệ để đảm bảo các lệnh trong `EXEC` không bị xen ngang. Tuy nhiên, điều đó không tự động đảm bảo logic nghiệp vụ đúng trong mọi tình huống.
# ACID: Redis không hỗ trợ đầy đủ

| tính chất       | CSDL                                                | Redis                                                                       |
| --------------- | --------------------------------------------------- | --------------------------------------------------------------------------- |
| Atomic          | toàn bộ lệnh trong transaction coi là 1 single-unit | single-thread --> không ảnh hưởng bởi các command khác                      |
| Non-Consitent   | dữ liệu khi mất điện vẫn được lưu                   | dữ liệu có thể bị mất (RAM, TTL)                                            |
| Isolation       |                                                     | Hỗ trợ transaction, single-thread nên các transaction không ảnh hưởng nhau  |
| Semi-Durability |                                                     | node khi bị shut-down thì dữ liệu sẽ bị mất (Default) --> có thể config lại |
# Replication
Dùng replica để đảm bảo performance và durability (master-slave)
Cơ chế đồng bộ
Khi một node không available --> backup
# Scalability
# Persistent
Cơ chế cấu hình Redis lưu vào đĩa :
- RDB: lưu lại snapshot tại 1 thời điểm theo chu kì nào đó (lưu file)
- AOF: (append-only file) lưu khi có write operation log lại --> khởi động lại thì dùng log để build lại data (performance thấp)
- No persistent: có thể dùng khi caching only
- RDB +AOF: kết hợp
# Optimistic lock
![[Pasted image 20260317234337.png]]
Cho phép tất cả thực hiện chức năng update nhưng sẽ có update kèm 1 trường version (tăng version).
Kiểm tra version --> nếu ko đúng version --> throw exception --> rollback transaction
# Pessimistic lock
Chỉ có 1 thằng đầu tiên được quyền truy cập update dữ liệu
**Select for update lock**
update trường available từ true --> false
# Use case
## Use case 1: Cache Data
Giúp tăng tốc độ đọc, thay vì truy xuất trong DB có thể lưu ở cache lấy cho nhanh
## Use case 2: Data dùng chung
Hệ thống phân tán (distributed) có nhiều instance cùng truy cập 1 dữ liệu chung (role, permission, ...) và ít thay đổi.
![[Pasted image 20261112230210.png]]
## Use case 3: Distributed lock
Tương tự cơ chế pessimistic lock: sử dụng hàm **setnx()** để tạo key. Key này là duy nhất chỉ 1 ông được vào tạo
![[Pasted image 20261112230833.png]]
Những thằng cùng vào tạo key ko tạo được thì sẽ báo lỗi để thử lại sau.
**select for update nowait:** trả về exception mà không phải chờ thằng tạo key thành công xử lý xong.
## Use case 4: Rate limiter
GIới hạn số lượng request nào đó trong hệ thống
### Rate limiter để:
- Trống tấn công DDoS
- Bruteforce attack
- Data scraping
### Phân loại:
- IP-based
- User-based
- App-based
- token bucket
Đếm số lần: hàm **incr()** đếm số lần action được thực hiện, kết hợp expire time để giới hạn thời gian thử lại (2p, 5p, 10p)
![[Pasted image 20261114141858.png]]
Ngoài ra có thể kết hợp thuật toán sliding window/ leak bucket để tối ưu rate limiter.
### Implement với Redis
VD: cần limit 20req/phút
Tạo key cho mỗi API được request đến theo mẫu
[API_KEY] : [current_time_number] là key độc lập với từng phút
Expire key sau mỗi phút
![[Pasted image 20261114144056.png]]

Pseudo-code:
- GET [user-api-key]:[current minute number]
- Nếu result từng dòng < 20 thì thực thi ngay--> tới line 4, không thì line 3
- Show error message và end connection
1. MULTI
2. INCR [user-api-key]:[current minute number]
3. EXPIRE [user-api-key]:[current minute number]
4. EXEC
## Use case 5: Leaderboard
̈ Bài toán: có nhiều người chơi, mỗi người chơi có 1 điểm số nhất định thay đổi
theo thời gian.
 ̈ Cần update: điểm của người chơi (có thể 1 ng nhiều điểm thay đổi theo thời
gian, chỉ ghi nhận kết quả cuối cùng của ng đó)
 ̈ Cần truy vấn:
¤ top 10 người chơi có điểm bé nhất
¤ truy vấn rank của 1 người chơi bất kì
![[Pasted image 20261114151347.png]]
YC1: đặt index vào cột điểm và sort theo SCORE ASC
YC2: khó để làm với DB

### Implement Redis:
̈ Sử dụng cấu trúc sortedset trong redis
¤ https://redis.io/docs/data-types/sorted-sets

 ̈ Update: ZADD key score member: https://redis.io/commands/zadd/
¤ ZADD leaderboard 359 100.
¤ ZADD: nếu member đã có trong sorted set
 ̈ Top 10 người chơi có điểm cao nhất:
¤ ZRANGE key start stop : https://redis.io/commands/zrange/
¤ ZRANGE leaderboard 0 9
 ̈ Truy vấn rank của 1 người chơi bất kì:
¤ ZRANK key member : https://redis.io/commands/zrank/
¤ ZRANK leaderboard 100: trả về rank của user 100
## Use case 6: Real-time update
Sử dụng redis pub/sub kết hợp với socketio hoặc websocket để cập
nhật realtime trên nền tảng web
![[Pasted image 20261114151549.png]]

VD: hệ thống chatroom
### Pub/Sub khác Queue
Pub/Sub: subcriber lỗi ko nhận đc, nhưng thằng khác subcribe nhận đc
Queue: subcriber lỗi, khi khôi phục task vẫn ở trong queue
# Bài tập
1. Viết service rate limiter dựa trên địa chỉ IP của người dùng. Trong 1
phút không quá 30 requests.
 2. Viết 1 service hỗ trợ distributed lock
 