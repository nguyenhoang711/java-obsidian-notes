# 1. Phân tích tình huống giả định
## 1.1 TH1: cửa hàng bánh mì giao hàng
![[Pasted image 20260520002820.png]]
VD: hệ thống bán bánh mì cung cấp cho các chi nhánh
Nhưng số lượng chi nhánh có thể tăng giảm tùy thời điểm --> khó quản lý
![[Pasted image 20260520003037.png]]
Tạo 1 hàng đợi tin nhắn --> tự phân phát bánh mì tới các chi nhánh (MQ)
- Tự đến lấy
- Cơ chế giao hàng tới các chi nhánh
**Ưu điểm 1: Tách kiến trúc**

## 1.2 Xử lý tuần tự
![[Pasted image 20260520003435.png]]
Có các tác vụ xử lý khác nhau --> muốn tăng tốc độ --> thay vì chờ đợi bước trước hoàn thành rồi làm bước sau, ta xử lý bất đồng bộ
![[Pasted image 20260520003525.png]]
Đưa vào MQ xử lý bất đồng bộ sau (async)
Ưu điểm 2: xử lý không đồng bộ

## 1.3 TH3 xử lý lượng đồng thời tăng đột biến
![[Pasted image 20260520004218.png]]
Số lượng request vào ồ ạt trong thời gian ngắn --> sập hệ thống
Số lượng có hạn: chỉ chấp nhận số lượng cụ thể ghi vào DB thôi

Giải pháp
![[Pasted image 20260520004338.png]]
Hàng đợi: mỗi hàng đợi 2k req/s 
Kéo số lượng yêu cầu tối đa MySQL có thể xử lý --> 2000/s từ MQ vào MySQL, phần còn lại bị stuck ở MQ
Giải thích: hết thời gian cao điểm số lượng req gửi tới giảm, nhưng MySQL vẫn có thể tiêu thụ 2k req/s --> trở về cân bằng nhanh chóng

Nhược điểm:
- Tăng độ phức tạp của hệ thống
- Rủi ro miss message --> BE, kiến trúc hệ thống
- Tính nhất quán: ở MQ báo nhận voucher thành công nhưng bên MySQL thì chưa

## 3. Kafka và RabbitMQ

| Tiêu chí  | Kafka                                                                                   | RabbitMQ                                                       |
| --------- | --------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| Bandwidth | Gấp 10 lần                                                                              | Gần 10k                                                        |
| Khả dụng  | Rất rất cao<br>Độ phân tán tốt, ít mất dữ liệu hơn RabbitMQ<br>Tin cậy: rất cao, dễ mất | Rất cao<br>Có hỗ trợ hệ thống phân tán<br>Cơ bản: không bị mất |
| Latency   | Cao hơn (>ms)                                                                           | Thấp (<ms)                                                     |
|           |                                                                                         |                                                                |
# 2. Giới thiệu Queue và Pub/Sub
![[Pasted image 20260521234709.png]]
## 2.1 Mô hình Ngang hàng (Đơn giản nhất)
Tất cả Producer đều có thể gửi tin vào hàng đợi
Tất cả đều có thể nhận message
## 2.2 Mô hình Pub/Sub
![[Pasted image 20260521235223.png]]
1 tin nhắn có thể nhiều người đăng ký nhận tin (đồng thời)
2 Kiểu đăng ký (tạm thời và lâu dài)
- Tạm thời: chỉ tồn tại khi người dùng đăng ký và sử dụng, thoát ra không nhận được tin nữa, tin bị mất đi
- Lâu dài: người dùng thoát ra, tin vẫn được gửi tới người dùng, các tin tiếp theo vẫn chờ để xử lý
Lưu ý: nếu hệ thống đơn giản ko cần setup mô hình này 
## 2.3 Cách thức hoạt động của RabbitMQ
![[Pasted image 20260522001101.png]]
Exchange: là thông điệp, trong có các định tuyến (routing) để tới các hàng đợi (queue) khác nhau
3 Phần chính
-  Producer
- Consumer
- Server (broker)
### 2.3.1 Producer
Giống như service của chúng ta, cần thiết lập kết nối + mở channel (TCP) tới Broker
### 2.3.2 Consumer
THiết lập kết nối tạo 1 kênh tới consumer
### 2.3.3 Server
Có 4 loại exchange:
- Direct
- Topic
- Headers
- Fanout
## 2.4 Các vấn đề trong nhận/ gửi message với RabbitMQ
Send và Receive một Message vào QUEUE với NODEJS Và tôi đã thấy một số vấn đề sau
1. Producer gửi quá nhiều tin mà bên consumer không xử lý kịp?
Latency cao --> sử dụng **prefetch** để xác nhận số lượng tối đa message mà consumer có thể giữ trong 1 lần gửi
Tức làm việc tuần tự: khi xong message này rồi mới gửi message kế tiếp
2. Giả sử việc consume message bị xử lý lỗi có gây mất tin không hay chạy tiếp message hiện tại?
Set TTL nếu message nào xử lý lỗi hoặc ko kịp xử lý thì thu hồi nó đi
3. Khi server broker, queue bị sập thì message còn không?
# 3.Giải quyết vấn đề độ tin cậy trong queue (noAck, ttl, durable, persistent)
## 3.1 SET TTL
Khi producer gửi 1 tin cho consumer, giả sử consumer xác nhận đã nhận (ack == true) cho RabbitMQ hiểu client đã nhận tin --> phải xóa trong hàng đợi
Nếu ack == false: chưa xử lý đc message này --> rabbitMQ chuyển message qua consumer khác xử lý tới khi được thì thôi?
Giả sử: nếu message lỗi thật thì sao --> message vẫn ở hàng đợi sang consumer khác, full hàng đợi message, tràn bộ nhớ
## 3.2 Durable (bền bỉ)
Khi restart service rabbitMQ thì cơ chế durable cho phép message tồn tại trong 1 khoảng thời gian cho phép tới khi khởi động lại
durable == true: khi start lại queue không mất message
persistent == true: queue được lưu vào ổ đĩa hoặc cache (nếu 1 trong 2 có vấn đề thì lấy còn lại)
--> hỗ trợ cho việc lấy tin khi restart

# 4. Đỉnh cao mô hình Publish Subscribe với Node.js và so sánh với mô hình của Redis
![[Pasted image 20260522001101.png|697]]
## 4.1 Cơ chế Fanout
- Exchange: nhận tin từ producer và đẩy tin vào queue, phân bố tin vào 1 hàng đợi hay nhiều hàng đợi (4 cơ chế) --> cơ chế Fanout
- Mối quan hệ giữa exchange và queue gọi là **binding** (ai muốn nhận thì cứ regist dịch vụ thoải mái)
- Option **exclusive** của exchange: cho phép xóa hàng đợi khi mà consumer không còn subscribe nữa
Open Questions: TH tôi chỉ muốn đăng ký 1 phần trong các video ,chỉ nhận thông báo với 1 số video quan tâm như là Redis chẳng hạn thì sử dụng cơ chế Exchange nào?
## 4.2 Cơ chế topic
Topic: là 1 hoạt động sendMail có thể send bất kì consumer nào muốn nhận, hoặc send 1 bộ phận hoặc nhiều mà đăng ký (regist)
Được sử dụng rộng rãi nhất
![[Pasted image 20260527005720.png]]
Giống hệ thống tra soát ngân hàng:
- gửi tiền đi sai --> yêu cầu tra soát ngân hàng
- Đặt lệnh gửi email cho các cơ quan liên quan tới lệnh gửi tiền sai của bạn
![[Pasted image 20260527010304.png]]
Nhận tin từ topic.
Các định dạng của topic:
- Kí tự * có nghĩa là phù hợp với bất kì từ nào
- Kí tự # có nghĩa khớp với 1 hoặc nhiều từ bất kì
THiết kế topic ăn nhau ở # và * : binding routing key
![[Pasted image 20260527010725.png]]
Đăng ký nhận tin với các topic định dạng trên
![[Pasted image 20260527010851.png]]
Gửi tin với topic dev.test
--> subscribe topic '* .test' nhận được
![[Pasted image 20260527011029.png]]
VD2: gửi tin topic dev.test.lead
--> có 2 consumer nhận được vì * .test chỉ tới đoạn đầu, thiếu .lead cuối
# Các cách xử lý Message error 
Có thể auto hoặc thủ công
## Xử lý với lỗi tạm thời
Tạo 1 hàng đợi chứa các message gây lỗi tạm thời.
VD: lỗi kết nối, many connection, --> cần đảm bảo lỗi đó chuyển tới hàng đợi lỗi (Dead Letter Exchanges) -- hàng đợi của 1 thư chết
Thử gửi lại các tin trong hàng đợi này --> nếu các tin vẫn gửi lỗi --> đưa vào đối tượng monitor + cảnh báo tới các nhóm liên quan để can thiệp thủ công (hotfix)
## Dead Letter Exchanges
Là 1 loại chuyển đổi: Sau 1 thời gian nếu message ko được tiêu thụ trở thành trash --> tái chế lại nếu nằm trong hàng đợi DLEx của chúng ta.
Là 1 exchange phổ biến trong RabbitMQ và các phần mềm hiện nay
### khi nào thì đưa vào DLEx
- Xử lý lỗi: logic YES --> true còn FAIL --> đưa vào DLEx
- Hết hạn tin nhắn (TTL): đặt hàng trong 30 phút phải thanh toán nhưng để quá --> đưa vào hộp thư rác --> gửi thư tự động xin lỗi, ....
- Limit hàng đợi: thư mới đến nằm trong noti chết
![[Pasted image 20260602142528.png]]
## 1.1 Hộp thư chết do lỗi lập trình
![[Pasted image 20260602144242.png]]
Quy trình các bước xử lý thông báo:
- Nghiệp vụ cần gửi thông báo (new product)
- Gửi vào exchange1 đưa vào queue1
- Load balance đưa vào send User notification
- Process logic:
	- Thành công: kết thúc
	- CHưa thành công: retry (tùy số lần)
## 1.2 Hộp thư chết do TTL
![[Pasted image 20260602144831.png]]
Node process logic đang bị lỗi, chưa có
