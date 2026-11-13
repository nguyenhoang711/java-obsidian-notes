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
