# SQL
## Subquery
### Khi nào ko sử dụng?
Độ lớn bảng trong sub-query lớn, ta tránh sử dụng subquery vì:
- Bảng phụ (**inlined view**) tạo ra trong subquery, index không còn
- Truy vấn, join sẽ chậm

Sử dụng **join** thay thế

## ACID: tính chất của transaction
- Atomic: tất cả hoặc không gì cả
- Consistency: nhất quán với database
- Isolation: transaction đồng thời chạy, môi trường độc lập, ko can thiệp nhau
- Durability: hoàn thành transaction --> thay đổi đảm bảo xảy ra
VD trong trường hợp chuyển tiền
![[Pasted image 20260320230643.png]]

Atomic: tiền vào ngân hàng kia thì đã phải trừ vào ngân hàng chuyển, hoặc là transaction bị hủy
Consistency: ràng buộc balanceAccount >= 0 hoặc transaction bị hủy
Isolation: chuyển tiền đồng thời thì cũng như chạy tuần tự, nếu có 2 giao dịch chuyển tiền đồng thời thì phải trừ lần lượt như tuần tự

## Isolation level
- Dirty read

## Concurrency Problems
Nghiệp vụ book phòng, vé
- Kiểm tra phòng có id = 123 có tồn tại hay không
- Nếu có thì mới vào đặt
![[Pasted image 20261010231436.png]]
Vấn đề: khi cả 2 thằng đều thao tác chung để đặt cùng phòng này
Cách giải quyết
+ Ngăn chặn concurrency xảy ra (pessimistic lock)
+ Để concurrency xảy ra, kiểm tra sau đó (optimistic lock)

## MySQL Lock
Lock: ưu tiên row-level lock **(SELECT ... FOR UPDATE)**

### Pessimistic lock
- Khóa không cho truy cập data đang có transaction và ko cho update
- đảm bảo tại 1 thời điểm, chỉ có 1 transaction truy cập data, các transaction khác phải đợi

### Optimistic lock
- Ngược lại pessimistic, cho phép nhiều transaction truy cập và update data
- Kiểm tra conflict trước khi thay đổi
- Cách kiểm tra đơn giản là tạo cột version --> sau mỗi update tăng version lên
- Sau khi tăng version, transaction chạy đồng bộ khác không thể update đc 
![[Pasted image 20260321000405.png]]
Đảm bảo chỉ có 1 dòng được update ( tận dụng tính atomic của transaction)
### Pessimistic lock: Ưu/ nhược điểm
- Data integrity: tính toàn vẹn lock data trước khi đọc hoặc sửa, tránh transaction khác xử lý đồng thời.
- Đơn giản: cài đặt dễ dàng
- Built-in DB support: hệ quản trị hỗ trợ
Nhược:
- Bottleneck nếu lock quá lâu (VD bài toán đặt phòng 100 người cùng lúc thì 1 người sớm nhất vào thao tác,  99 ng chờ, khi xong thì cũng 1 ng select, 98 ng chờ, ...)
- Deadlock: 2 hoặc nhiều transaction đợi lẫn nhau (giữ 1 lock nhưng chờ transaction khác)
### Optimistic lock: Ưu nhược
Ưu:
- Performance: Không phải chờ đợi lock
- Concurrency: tốt hơn vì ko có deadlock hoặc lock
Nhược:
- Xử lý conflict: đến cuối transaction mới biết
- Buộc maintain cột version (reset value)
Dead lock:
- 2 thằng transaction A và B 
- Cùng giữ 1 cái và cùng đợi 1 cái
- 2 ông cùng giữ và cùng đợi chéo nhau
Optimistic Lock: đôi lúc khi transaction quá dài, nhiều thao tác trong cùng 1 transaction thì sẽ bị lâu, gây mất hiệu quả về mặt performance.