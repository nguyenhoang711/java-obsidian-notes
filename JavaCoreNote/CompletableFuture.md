Tính năng mới trong JDK 8
Interface "Future" cũ kĩ bắt buộc phải block thread để kiểm tra tác vụ đã hoàn thành
CompleteFuture (Java 8+) đưa vào callback không chặn và xử lý ngoại lệ trong pipeline.
![[Pasted image 20260319090534.png]]
