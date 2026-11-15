# Logging
̈ Sử dụng @Slf4j để tạo ra thuộc tính log cho class
 ̈ SLF4J = Simple Logging Facade for Java
 ̈ SLF4J là abstraction của các framework logging như log4j, hay logback.
 ̈ Mặc định khi sử dụng “starter” packages, logging sẽ sử dụng logback
 ![[Pasted image 20261114155724.png]]
 Command đọc file log bằng tail, grep
 - tail -f log/application.log: đọc log file các dòng cuối
 - grep "INFO" log/application.log: đọc các dòng log có từ khoá INFO
 - grep "2024-04-25T05:54" log/application.log: đọc các dòng log ngày 25/04/2024
Trường hợp service nhiều node thì làm sao?
# ELK stack
Elasticsearch | Logslash | Kibana
Logging gồm 3 yếu tố: collection, indexing và visualization
Quản lý, phân loại log với hệ thống lớn:
- Nhiều instance cùng 1 app nhưng trên các server khác nhau, hoặc log từ nhiều service khác nhau
- Nhiều loại log: nginx, application, ...
- Dung lượng cần lưu trữ lớn
![[Pasted image 20261114160905.png]]
## ELK stack
- Logstash: tải nhập, chuyển đổi và gửi dữ liệu vào Elasticsearch
- Elasticsearch: lập index, phân tích và tìm kiếm theo yêu cầu
- Kibana: giao diện hiển thị kết quả tìm kiếm, hoặc lập dashboard
Tra cứu application log bằng query, filter
![[Pasted image 20261114161151.png]]
# Một số gợi ý tạo monitor dashboard
¤ Dashboard đếm số lượng request mỗi phút theo status code
¤ Dashboard đếm số lượng request mỗi phút theo API endpoint
¤ Dashboard đếm số lượng log info, error theo mỗi phút
Ngoài ra:
¤ Có thể thêm tracing để trace request (bằng 1 generated request ID) giữa các micro-services
¤ Có thể tích hợp với Sentry để quản lý log error
# Vì sao cần monitoring
Đảm bảo cấu trúc dữ liệu nhất quán, dễ dàng theo dõi
Có các library như Prometheus, Elastic stack (APM), StatsD, ...
# Prometheus
![[Pasted image 20261114164644.png]]
Cơ chế:
- Đi đến các service thu thập log ra (khoảng time cụ thể) sau đó lưu lại vào Prometheus server
- Service cần tạo http endpoint API cấu hình để Prometheus vào thu thập hệ thống
- Prometheus Server cung cấp UI, kèm với Graphana để visualize ra
