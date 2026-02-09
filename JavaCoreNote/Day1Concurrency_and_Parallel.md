![[Pasted image 20260205225758.png]]
Concurrency: tính đồng thời, chỉ 1 CPU nhưng đan xen nhau xử lý trong cùng 1 thời gian
- làm 1 món ăn luộc rau 5 phút, thái thịt 2 phút, chiên thịt 3 phút, nhặt rau 1 phút --> xử lý đồng thời trong vòng 6 phút thay vì 11 phút nếu làm tuần tự.
- Dựa trên thời gian chờ của I/O, tính I/O
Parallel: tính song song: có nhiều core trong GPU hoặc CPU xử lý song song, chia các task thành các phần nhỏ rồi xử lý, thực hiện nhiều tác vụ trên cùng 1 thời điểm.
- Tận dụng multi-core tính song song
- Có 3 đầu bếp cùng làm, và quan trọng đầu bếp khỏe
