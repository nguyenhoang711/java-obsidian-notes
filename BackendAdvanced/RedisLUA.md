- Transaction trong MySQL và transaction trong Redis
Lệnh MULTI: mở trạng thái transaction trong redis (Tương tự START TRANSACTION)
Lệnh không thực hiện ngay lập tức mà đưa vào hàng đợi (QUEUE)
EXEC: thực thi lệnh toàn bộ trong Redis (COMMIT)
DISCARD: hủy toàn bộ lệnh (pop khỏi queue)

Q1: Khi 1 luồng trong Redis đang thực thi, có 1 luồng khác vào sửa lại key?
Khóa có thể sửa đổi được ở bất kì luồng nào trước khi thực hiện lệnh EXEC (COMMIT)
--> Cần cơ chế theo dõi thay đổi của key trước khi EXEC --> WATCH
WATCH [keyname] 
- chặn transaction khi có luồng khác can thiệp vào luồng đang chạy transaction
tính ATOMIC:
- Tất cả hoạt động  trong TRANSACTION được thành công hoặc thất bại tất cả
- Nếu có 1 lỗi xảy ra --> ROLLBACK trạng thái trước TRANSACTION

# LUA script
Có 2 tham số:
- EVAL: tạo ra 1 key của hàm băm, khi sử dụng lại key đó, khởi chạy lại hàm đó, ko phải viết lại
EVAL: là tham số đầu tiên của tập lệnh LUA
script: đoạn code chứa tập lệnh
VD: EVAL "return ARGV[1]" 0 anonystick
Ý nghĩa: trả về tham số thứ 2 của các key
 EVAL "redis.call('SET', KEYS[1], ARGV[1])" 1 "TICKET:1" 10000

EVAL "if redis.call('EXISTS', KEYS[1]) == 0 then return redis.call('SET', KEYS[1], ARGV[1]) else return -1"
Q2: Nếu lệnh LUA script có lỗi thì có thực hiện rollback không?
Trả lời: không có cơ chế rollback khi gặp lỗi giống như transaction. Các sự kiện atomic không rollback
Q3: phân biệt redis.call() và redis.pcall()?
redis.call(): execute lệnh Redis, nếu gặp lỗi thì execute dừng lại và trả ra lỗi luôn (các lệnh chạy trước đó vẫn lưu kết quả) --> không phải rollback
VD: EVAL "redis.call('SET', 'k1', 'v1'); redis.call('INCRBY', 'k2', 1/0); redis.call('SET', 'k3', 'v3');" 0
Lỗi ở dòng INCRBY --> vẫn lưu giá trị key k1

redis.pcall(): khi xảy ra lỗi trong quá trình thực thi script thì vẫn thực thi bình thường, các hàm kế tiếp vẫn chạy bình thường. (ko gây block luồng)


Case thực tế:
- Khi muốn nó chắc chắn xảy ra và hoàn thành  -->**redis.pcall()**
- Khi cần nhất quán cao hơn --> **redis.call()**

Tập lệnh LUA thực thi theo thứ tự hàng đơi (QUEUE) theo thứ tự đầu vào--> viết nối tiếp --> tập lệnh không bị gián đoạn bởi client khác --> đảm bảo tính nguyên tử 

# Master - Slave Redis
Setup Master - Slave Redis:
``` yml
version: '3.7'

services:

  master:

    image: redis:5.0.9

    container_name: redis_master

    restart: always

    command: redis-server --appendonly yes

    ports:

      - "6379:6379"

  

  slave1:

    image: redis:5.0.9

    container_name: redis_slave1

    ports:

      - "6380:6379"

    command: redis-server --appendonly yes --slaveof redis-master 6379

  

  slave2:

    image: redis:5.0.9

    container_name: redis_slave2

    restart: always

    command: redis-server --appendonly yes --slaveof redis-master 6379

    ports:

      - "6381:6379"
```
--appendonly: đồng bộ các log từ master sang slave

Master Redis: ghi vào, Slave Redis: đọc ra
TH master die --> redis-slave cố connect tới master để đọc --> cần phải auto set 1 master mới.

# Redis Sentinel
``` cnf
bind 0.0.0.0
port 26379
sentinel monitor redis-master redis-master 6379 2
sentinel down-after-milliseconds redis-master 1000

```
config cách này có thể gặp lỗi:
**"Cannot resolve master instance hostname."**
Giải thích nguyên do:
- Sentinel không biết redis-master: do docker compose tạo nhiều container cùng lúc
Nếu muốn các container giao tiếp đc với nhau thì phải cùng 1 mạng LAN, mạng cục bộ
--> config network cho chúng

**redis-cli INFO replication**: kiểm tra cấu hình redis và replication của node hiện tại

TH master down --> sentinel đẩy 1 thằng từ slave lên master
```log
redis-sentinel2  | 1:X 19 May 2026 03:01:23.658 # +failover-end master redis-master 172.21.0.4 6379
redis-sentinel2  | 1:X 19 May 2026 03:01:23.658 # +switch-master redis-master 172.21.0.4 6379 172.21.0.2 6379
redis-sentinel2  | 1:X 19 May 2026 03:01:23.659 * +slave slave 172.21.0.3:6379 172.21.0.3 6379 @ redis-master 172.21.0.2 6379
```


``` cmd
redis-slave1  | 1:S 19 May 2026 03:01:23.498 # Master replication ID changed to 42ab97c0cc8346f3b12e380090e078cf7b9a2c8d
redis-slave1  | 1:S 19 May 2026 03:01:23.498 * MASTER <-> REPLICA sync: Master accepted a Partial Resynchronization.
```

TH master recover : trở thành slave
