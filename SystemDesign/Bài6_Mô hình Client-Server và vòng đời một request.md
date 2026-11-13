Tưởng tượng bạn vào một nhà hàng. Bạn (khách) không lao vào bếp nấu ăn, mà gọi món với anh phục vụ; anh chuyển yêu cầu xuống bếp; bếp nấu xong bưng ra cho bạn. Bạn chỉ cần biết “gọi món - nhận món”, còn bếp vận hành thế nào thì khỏi lo. Đó chính là tinh thần của **mô hình client-server** - trái tim của gần như mọi hệ thống bạn dùng hằng ngày: web, app điện thoại, game online.

Chào mừng bạn đến với **Tuần 2 - Mạng và giao tiếp**! Tuần 1 chúng ta đã xây **tư duy thiết kế**: bóc tách bài toán, ước lượng quy mô, phân biệt yêu cầu chức năng và phi chức năng, và nghĩ theo kiểu “đánh đổi” (trade-off) thay vì “đúng/sai”. Giờ ta ghép những viên gạch hạ tầng thật, bắt đầu từ viên gạch nền tảng nhất mà mọi kiến trúc sau này đều dựng lên: **hai máy tính nói chuyện với nhau bằng cách nào?**

> 🔑 **Một câu để nhớ:** Client gửi request, server trả response - toàn bộ Internet là hàng tỷ cuộc hội thoại “hỏi - đáp” như vậy chạy trên nền TCP/IP.

Trong bài này ta sẽ mổ xẻ **vòng đời đầy đủ của một HTTP request**: từ khoảnh khắc bạn gõ một URL và bấm Enter, cho tới khi trang web hiện ra trước mắt. Đây là câu hỏi phỏng vấn kinh điển (“What happens when you type a URL?”) và cũng là bản đồ giúp bạn định vị mọi thành phần hạ tầng sẽ học ở các bài sau.
# Sẽ học được gì
> 📌 **Yêu cầu trước:** đã học xong Tuần 1 (tư duy thiết kế). Không cần giỏi mạng máy tính, chỉ cần biết “trình duyệt gọi tới server để lấy trang web” là đủ để bắt đầu.

- [ ] Phân biệt **client-server** và **peer-to-peer (P2P)**, biết khi nào dùng cái nào
- [ ] Kể lại **vòng đời đầy đủ của một HTTP request** qua 7 bước (DNS -> TCP -> TLS -> request -> xử lý -> response -> render)
- [ ] Định vị các **tầng hạ tầng**: client, DNS, load balancer, web server, app server, database
- [ ] Nắm **mô hình OSI và TCP/IP** ở mức đủ dùng cho System Design
- [ ] Phân biệt **TCP và UDP**, biết chọn cái nào cho từng bài toán
- [ ] Hiểu **port** và **socket** là gì, vì sao một server phục vụ được nhiều client cùng lúc
# 1. Client-Server vs Peer-to-Peer
**Mô hình client-server** chia các máy thành hai vai rõ ràng: **client** (bên yêu cầu dịch vụ - trình duyệt, app điện thoại) và **server** (bên cung cấp dịch vụ - máy chủ web). Client luôn là bên mở lời hỏi; server ngồi chờ và trả lời. Giống nhà hàng: khách hỏi, bếp phục vụ, hai vai không lẫn lộn.

Trái lại, **peer-to-peer (P2P)** không có ông chủ trung tâm. Mọi máy (**peer**) vừa là client vừa là server, trực tiếp chia sẻ tài nguyên cho nhau. Giống một nhóm bạn cùng góp món ăn tới bữa tiệc chung: ai cũng vừa cho vừa nhận. BitTorrent, blockchain (Bitcoin) là ví dụ P2P.

|Tiêu chí|Client-Server|Peer-to-Peer (P2P)|
|---|---|---|
|**Vai trò**|Tách bạch: client hỏi, server trả|Mỗi peer vừa hỏi vừa trả|
|**Trung tâm**|Có server trung tâm|Không có (phi tập trung)|
|**Quản lý, bảo mật**|Dễ (kiểm soát tại server)|Khó (dữ liệu rải rác)|
|**Điểm chết đơn (SPOF)**|Server chết là sập|Bền hơn, không có 1 điểm chết|
|**Ví dụ**|Web, REST API, database|BitTorrent, blockchain|
```
   CLIENT-SERVER                         PEER-TO-PEER (P2P)

   [Client]  [Client]  [Client]          [Peer] ─────── [Peer]
       │        │        │                  │  ╲       ╱  │
       └────────┼────────┘                  │    ╲   ╱    │
                ▼                            │      ╳      │
           [ SERVER ]                        │    ╱   ╲    │
           (trung tâm)                     [Peer] ─────── [Peer]
```
💡 Đa số hệ thống bạn thiết kế trong khóa này là **client-server** vì dễ kiểm soát, dễ bảo mật, dễ mở rộng theo tầng. P2P chỉ tỏa sáng khi cần phi tập trung hoặc chia tải file khổng lồ.
# 2. Vòng đời đầy đủ của một HTTP request
Đây là phần “xương sống” của bài. Bạn gõ `https://www.example.com` rồi bấm Enter - bên trong xảy ra một chuỗi 7 bước như dây chuyền domino:

```
 Bạn gõ URL + Enter
        │
   (1)  ▼  DNS LOOKUP: "www.example.com" -> IP 93.184.216.34
        │       (hỏi cache trình duyệt -> OS -> DNS resolver)
   (2)  ▼  TCP HANDSHAKE (3 bước): SYN -> SYN/ACK -> ACK
        │       (client và server "bắt tay" mở kênh tin cậy)
   (3)  ▼  TLS HANDSHAKE (nếu HTTPS): trao khóa, mã hóa kênh
        │
   (4)  ▼  GỬI HTTP REQUEST:  GET / HTTP/1.1  Host: www.example.com
        │
   (5)  ▼  SERVER XỬ LÝ: LB chọn server -> app server chạy logic
        │                  -> truy vấn database -> dựng HTML/JSON
   (6)  ▼  TRẢ HTTP RESPONSE: 200 OK + nội dung (HTML/JSON)
        │
   (7)  ▼  TRÌNH DUYỆT RENDER: dựng cây DOM, tải CSS/JS/ảnh, vẽ trang
        │
      🎉 Trang hiện ra
```
Cùng đi qua từng bước cho chắc:

|Bước|Tên|Chuyện gì xảy ra|
|---|---|---|
|1|**DNS Lookup**|Đổi tên miền (chữ) thành địa chỉ IP (số). Trình duyệt hỏi cache của mình, rồi cache OS, rồi mới hỏi DNS resolver ngoài mạng|
|2|**TCP Handshake**|Client và server “bắt tay 3 bước” (SYN, SYN/ACK, ACK) để mở một kênh truyền tin cậy|
|3|**TLS Handshake**|Chỉ với HTTPS: hai bên trao khóa, thống nhất thuật toán mã hóa để kênh được bảo mật|
|4|**Gửi request**|Client gửi dòng `GET / HTTP/1.1` kèm các header (Host, Cookie, User-Agent…)|
|5|**Server xử lý**|Load balancer chọn một server; app server chạy logic nghiệp vụ, có thể truy vấn database; dựng ra nội dung|
|6|**Trả response**|Server gửi lại status code (200, 404, 500…) kèm body (HTML/JSON)|
|7|**Render**|Trình duyệt phân tích HTML thành cây DOM, tải thêm CSS/JS/ảnh, rồi vẽ trang ra màn hình|

> 🎯 Ba bước đầu (DNS -> TCP -> TLS) là **thiết lập kết nối**, chưa hề trao đổi dữ liệu ứng dụng. Đây là lý do request đầu tiên tới một site “lạ” luôn chậm hơn các request sau (kết nối đã mở sẵn, được tái dùng).
# 3. Các tầng hạ tầng một request đi qua
Bước 5 (“server xử lý”) thực ra ẩn chứa cả một dàn nhạc. Một hệ thống web quy mô thật không phải “một cái máy chủ” mà là nhiều tầng nối tiếp:
```
 [Client]
    │  request
    ▼
 [DNS] ── phân giải tên -> IP
    │
    ▼
 [Load Balancer] ── chia đều request cho nhiều server
    │        ╲          ╲
    ▼         ▼           ▼
 [Web/App   [Web/App   [Web/App
  Server]    Server]    Server]   ← chạy logic nghiệp vụ
    │            │           │
    └────────────┼───────────┘
                 ▼
           [ Database ]  ← lưu và truy vấn dữ liệu
```
|Tầng|Vai trò (analogy nhà hàng)|
|---|---|
|**DNS**|Danh bạ - tra “tên quán” ra “địa chỉ nhà”|
|**Load Balancer**|Anh quản lý đón khách, chia bàn cho nhiều phục vụ để không ai quá tải|
|**Web Server**|Anh phục vụ - nhận request, phục vụ file tĩnh (ảnh, HTML, CSS)|
|**App Server**|Đầu bếp - chạy logic nghiệp vụ, tính toán, quyết định|
|**Database**|Kho nguyên liệu - nơi cất và lấy dữ liệu|

> 💡 Ranh giới **web server** và **app server** đôi khi nhòe: web server (như Nginx) giỏi phục vụ file tĩnh và làm reverse proxy; app server (Node.js, Django, Spring) chạy code nghiệp vụ động. Nhiều hệ thống gộp cả hai, nhưng khi thiết kế lớn ta tách ra để mở rộng độc lập.

# 4. Mô hình OSI và TCP/IP (mức đủ dùng)
Để hai máy khác hãng vẫn nói chuyện được, người ta chia việc giao tiếp thành **các tầng chồng lên nhau**, mỗi tầng lo một việc. Giống gửi bưu kiện quốc tế: bạn lo nội dung thư, bưu điện lo đóng gói, hãng vận chuyển lo đường bay - mỗi bên một khâu.

**Mô hình OSI** có 7 tầng (lý thuyết), còn **TCP/IP** có 4 tầng (thực tế Internet đang chạy). Với System Design bạn chỉ cần nhớ vài tầng chủ chốt:

|TCP/IP (4 tầng)|OSI tương ứng|Lo việc gì|Ví dụ giao thức|
|---|---|---|---|
|**Application**|5-6-7|Dữ liệu ứng dụng, ngữ nghĩa|HTTP, HTTPS, DNS, WebSocket|
|**Transport**|4|Vận chuyển tin cậy/nhanh, port|**TCP**, **UDP**|
|**Internet**|3|Định tuyến gói qua các mạng|IP|
|**Link**|1-2|Truyền bit trên dây/sóng thật|Ethernet, Wi-Fi|

> ⚠️ Đừng học thuộc lòng 7 tầng OSI như con vẹt. Điều quan trọng cho System Design là hiểu: **HTTP nằm ở tầng Application, chạy trên TCP ở tầng Transport, TCP lại chạy trên IP ở tầng Internet**. Khi ai đó nói “load balancer tầng 4” (L4) hay “tầng 7” (L7), họ đang chỉ tầng Transport (TCP/UDP) hay tầng Application (HTTP) - đó là lúc kiến thức này thành hữu dụng thật sự.
# 5. TCP vs UDP
Cả hai đều là giao thức tầng Transport, nhưng triết lý ngược nhau. **TCP** giống gọi điện thoại: phải “bắt tay” kết nối trước, nói xong bên kia xác nhận, mất tiếng thì nói lại - **tin cậy, đúng thứ tự, nhưng chậm hơn**. **UDP** giống gửi bưu thiếp: cứ ném đi, không bắt tay, không đảm bảo tới nơi - **nhanh, nhẹ, nhưng có thể mất gói**.

|Tiêu chí|TCP|UDP|
|---|---|---|
|**Kết nối**|Có bắt tay (connection-oriented)|Không (connectionless)|
|**Tin cậy**|Đảm bảo tới nơi, đúng thứ tự|Không đảm bảo|
|**Tốc độ**|Chậm hơn (nhiều overhead)|Nhanh, độ trễ thấp|
|**Kiểm soát tắc nghẽn**|Có|Không|
|**Ví dụ**|Web (HTTP), email, tải file|Video call, game online, DNS, streaming|

> 🎯 Quy tắc chọn nhanh: **cần đúng và đủ -> TCP** (trang web thiếu 1 chữ là hỏng); **cần nhanh và real-time, mất chút không sao -> UDP** (video call rớt 1 khung hình còn hơn khựng cả cuộc gọi để chờ khung hình cũ).
# 6. Port và Socket
Một máy chủ chỉ có một địa chỉ IP nhưng chạy nhiều dịch vụ cùng lúc (web, email, database). Làm sao gói tin biết đi vào đúng dịch vụ? Câu trả lời là **port** (cổng) - một con số phân biệt các dịch vụ trên cùng một IP. Giống tòa chung cư: IP là địa chỉ tòa nhà, port là số căn hộ.

Các port phổ biến cần nhớ: **80** (HTTP), **443** (HTTPS), **22** (SSH), **53** (DNS).

Khi ghép **IP + port** của cả hai đầu lại, ta có một **socket** - điểm cuối định danh duy nhất một kết nối. Nhờ vậy, một server web ở port 443 phục vụ được **hàng nghìn client cùng lúc**: mỗi client có port riêng ở đầu của họ, nên mỗi kết nối là một socket khác nhau, server không lẫn lộn.
```
 Client A (1.2.3.4:51000) ──┐
 Client B (5.6.7.8:62000) ──┼──► Server (93.184.216.34 : 443)
 Client C (9.9.9.9:53000) ──┘
   (mỗi client 1 port riêng -> mỗi kết nối là 1 socket khác nhau)
```
💡 Đây là lý do một server nghe ở duy nhất port 443 vẫn phục vụ được cả triệu người: server phân biệt các kết nối bằng **bộ 4** (IP+port nguồn, IP+port đích), chứ không phải chỉ bằng port của nó.
# Nhìn sâu hơn
Một điều khiến nhiều người bất ngờ: **HTTP/2 và HTTP/3 đã thay đổi luật chơi ở bước kết nối.** HTTP/2 cho phép **nhiều request “ghép kênh” (multiplexing)** trên một kết nối TCP duy nhất - nhanh hơn hẳn HTTP/1.1. Còn HTTP/3 táo bạo hơn: nó **bỏ TCP, chuyển sang chạy trên UDP** (qua giao thức QUIC), tự xây lại phần tin cậy ở tầng ứng dụng để tránh “head-of-line blocking” của TCP và rút ngắn thời gian bắt tay.

Nghe ngược đời đúng không - “cần tin cậy sao lại dùng UDP”? Bài học ở đây: **các tầng và giao thức không phải luật bất biến, mà là những đánh đổi.** Khi web ngày càng cần real-time, người ta sẵn sàng xây lại phần tin cậy ở chỗ khác để lấy tốc độ - đúng tinh thần “trade-off” của Tuần 1: không có lựa chọn hoàn hảo, chỉ có lựa chọn phù hợp bối cảnh.
# Thực hành
- **[Dễ]** Vẽ lại sơ đồ 7 bước vòng đời của một HTTP request từ trí nhớ, ghi rõ mỗi bước làm gì. Sau đó đối chiếu với bài.
- **[Trung bình]** Với 3 ứng dụng sau, chọn TCP hay UDP và giải thích: (a) tải file PDF, (b) gọi video Zoom, © tra cứu DNS. Nêu lý do dựa trên đánh đổi tin cậy vs tốc độ.
- **[Thử thách]** Bạn gõ `https://shopee.vn` và trang tải chậm bất thường. Hãy liệt kê **từng bước** trong vòng đời request có thể là “thủ phạm” gây chậm (DNS chậm? TLS handshake? database query lâu? render nặng?) và cách bạn sẽ kiểm tra từng nghi phạm.
# Hiểu lầm thường gặp
- ❌ “Gõ URL xong là trình duyệt nối thẳng tới server ngay.” → ✅ Không. Phải **DNS lookup** đổi tên miền ra IP trước đã, rồi mới bắt tay TCP.
- ❌ “HTTPS chỉ là HTTP có thêm chữ S cho vui.” → ✅ Sai. HTTPS thêm hẳn bước **TLS handshake** để mã hóa kênh - tốn thêm vòng đi lại nhưng bảo mật.
- ❌ “TCP luôn tốt hơn UDP vì nó tin cậy.” → ✅ Không. Video call, game real-time dùng **UDP** vì tốc độ quan trọng hơn việc không mất một gói nhỏ.
- ❌ “Web server và app server là một.” → ✅ Không hẳn. Web server giỏi phục vụ file tĩnh/reverse proxy; app server chạy logic nghiệp vụ động. Có thể gộp, nhưng vai trò khác nhau.
- ❌ “Một server chỉ phục vụ được một client tại một thời điểm ở mỗi port.” → ✅ Sai. Nhờ **socket** (bộ 4 IP+port hai đầu), một server nghe ở một port phục vụ được hàng nghìn client cùng lúc.
# Thực hành tốt nhất / Mẹo ghi nhớ
- Nhớ vòng đời request bằng chuỗi: **DNS -> TCP -> TLS -> Request -> Xử lý -> Response -> Render**.
- Nhớ tầng hạ tầng theo dòng chảy: **Client -> DNS -> Load Balancer -> Web/App Server -> Database**.
- Mẹo chọn giao thức: **TCP = gọi điện thoại (tin cậy, có xác nhận); UDP = gửi bưu thiếp (nhanh, ném là xong)**.
- Mẹo port/socket: **IP là địa chỉ tòa nhà, port là số phòng, socket là một cuộc gọi cụ thể giữa hai phòng**.
- Khi phỏng vấn hỏi “chuyện gì xảy ra khi gõ URL?”, cứ đọc thuộc 7 bước ở trên - điểm gần như tối đa.
# Tóm tắt trực quan
|Khái niệm|Một dòng để nhớ|
|---|---|
|Client-Server|Client hỏi, server trả, vai trò tách bạch|
|Peer-to-Peer|Mỗi máy vừa hỏi vừa trả, không trung tâm|
|Vòng đời request|DNS -> TCP -> TLS -> Request -> Xử lý -> Response -> Render|
|Tầng hạ tầng|Client -> DNS -> Load Balancer -> Web/App Server -> Database|
|OSI / TCP-IP|HTTP (App) chạy trên TCP (Transport) chạy trên IP (Internet)|
|TCP|Tin cậy, đúng thứ tự, chậm hơn - dùng cho web, email, file|
|UDP|Nhanh, có thể mất gói - dùng cho video call, game, DNS|
|Port + Socket|Port = số phòng; socket = bộ 4 IP+port định danh 1 kết nối|
# Tài liệu tham khảo
- System Design Primer (mục Communication, TCP/UDP, HTTP) - GitHub: [https://github.com/donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer)
- What happens when you type a URL into your browser? - ByteByteGo (bài giải thích chuẩn): [https://blog.bytebytego.com/p/what-happens-when-you-type-a-url](https://blog.bytebytego.com/p/what-happens-when-you-type-a-url)
- OSI model - Wikipedia: [https://en.wikipedia.org/wiki/OSI_model](https://en.wikipedia.org/wiki/OSI_model)
- Transmission Control Protocol - Wikipedia: [https://en.wikipedia.org/wiki/Transmission_Control_Protocol](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)
- 🎥 Video: “What happens when you type a URL into your browser?” - ByteByteGo (YouTube): [https://www.youtube.com/watch?v=AlkDbnbv7dk](https://www.youtube.com/watch?v=AlkDbnbv7dk)
# Sum Up
- **Client-server** (client hỏi, server trả) là mô hình nền tảng của web; **P2P** phi tập trung dùng cho chia sẻ file, blockchain.
- **Vòng đời một HTTP request** gồm 7 bước: DNS -> TCP handshake -> TLS -> gửi request -> server xử lý -> trả response -> render. Ba bước đầu chỉ để thiết lập kết nối.
- Một request đi qua nhiều **tầng hạ tầng**: client, DNS, load balancer, web server, app server, database - mỗi tầng một vai như một nhà hàng có tổ chức.
- **TCP** đảm bảo tin cậy (web, email), **UDP** ưu tiên tốc độ (video call, game). **Port** phân biệt dịch vụ, **socket** định danh từng kết nối để một server phục vụ được cả triệu người.

Chúng ta đã thấy bước đầu tiên của mọi request là **DNS lookup** - đổi tên miền thành IP. Nhưng cái “danh bạ khổng lồ” của cả Internet đó hoạt động ra sao, vì sao nó nhanh đến vậy, và có thể làm gì với nó để tăng tốc/cân tải hệ thống? Hẹn gặp bạn ở **Bài 2 - “DNS và phân giải tên miền”**, nơi ta bóc tách từng lớp cache và cơ chế định tuyến của DNS! 🚀
