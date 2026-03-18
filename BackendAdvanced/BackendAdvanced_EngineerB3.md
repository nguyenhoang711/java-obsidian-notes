# Optimistic lock
![[Pasted image 20260317234337.png]]
Cho phép tất cả thực hiện chức năng update nhưng sẽ có update kèm 1 trường version (tăng version).
Kiểm tra version --> nếu ko đúng version --> throw exception --> rollback transaction
# Pessimistic lock
Chỉ có 1 thằng đầu tiên được quyền truy cập update dữ liệu
**Select for update lock**
update trường available từ true --> false
