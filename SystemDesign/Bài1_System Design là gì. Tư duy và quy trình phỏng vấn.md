# 1. System Design là gì và vì sao quan trọng?
**System Design (thiết kế hệ thống)** là quá trình định nghĩa **kiến trúc**, **các thành phần** và **cách chúng giao tiếp** để một hệ thống phần mềm đáp ứng yêu cầu về **quy mô, hiệu năng và độ tin cậy**. Ngắn gọn: nó trả lời _“Làm sao ghép các mảnh (server, database, cache…) lại để cả cỗ máy chạy tốt ở quy mô lớn?”_

Analogy: viết code một hàm giống **nấu một món ăn ngon**; còn system design giống **thiết kế cả một chuỗi nhà hàng** - bếp trung tâm, kho, chuỗi cung ứng, quy trình phục vụ giờ cao điểm. Món ngon là điều kiện cần, nhưng chuỗi vận hành lộn xộn thì cả hệ thống vẫn sập.

Vì sao quan trọng?

|Lý do|Giải thích|
|---|---|
|**Quyết định lên senior**|Middle/senior không chỉ code, mà **thiết kế** phần cho cả đội xây|
|**Vòng phỏng vấn bắt buộc**|Hầu hết công ty lớn có vòng “system design interview” riêng|
|**Tránh sập ở quy mô lớn**|Thiết kế sai từ đầu -> chi phí sửa cực đắt khi đã có triệu user|
|**Ra quyết định có cơ sở**|Biết vì sao chọn cache Redis chứ không chọn cái khác|

> 🎯 System Design không phải “vẽ vời cho đẹp” - đó là kỹ năng ra **quyết định kỹ thuật có lý do** dưới ràng buộc thực tế (tiền, người, thời gian, quy mô).
# 2. Low-Level Design vs High-Level / System Design
Đây là chỗ nhiều bạn nhầm. Có hai “tầng” thiết kế phục vụ hai câu hỏi khác nhau: **HLD (High-Level Design / System Design)** nhìn từ **trên cao** - có những **service lớn** nào (server, database, cache, queue…) và chúng **giao tiếp ra sao**; **LLD (Low-Level Design)** nhìn **cận cảnh** - **class**, method, design pattern, cấu trúc dữ liệu bên trong một module.

|Tiêu chí|High-Level Design (System Design)|Low-Level Design|
|---|---|---|
|Góc nhìn|Toàn hệ thống, tổng thể|Bên trong một module/service|
|Quan tâm|Service, database, cache, giao tiếp, scale|Class, method, pattern, cấu trúc dữ liệu|
|Câu hỏi mẫu|“Thiết kế Twitter”|“Thiết kế class Tweet và cách lưu trữ nó”|
|Đầu ra|Sơ đồ kiến trúc, luồng dữ liệu|Sơ đồ class (UML), pseudo-code|
|Vòng phỏng vấn|System Design interview|OOD / Coding interview|

Analogy nhớ lâu: **HLD là quy hoạch cả thành phố** (đường lớn, khu dân cư, nhà máy điện đặt ở đâu); **LLD là bản vẽ chi tiết một ngôi nhà** (phòng ngủ ở đâu, ổ điện chỗ nào). Cả khóa này tập trung chủ yếu vào **HLD / System Design**.

> 💡 Trong phỏng vấn system design, người ta muốn nghe bạn nói về **thành phần và trade-off ở tầng cao**, đừng vội nhảy vào viết class. Ngược lại, phỏng vấn OOD lại muốn bạn đi sâu class - đọc đúng đề bài!
# 3. Tư duy trade-off: không có "câu trả lời đúng duy nhất"
Đây là **linh hồn** của system design, cũng là thứ khiến người mới sốc nhất. Bài tập code có đáp án đúng/sai rõ ràng. System design thì **không** - mọi lựa chọn đều **được cái này, mất cái kia**.

Analogy: chọn phương tiện đi làm. Xe máy **rẻ, linh hoạt** nhưng **dầm mưa nắng**. Ô tô **êm, an toàn** nhưng **đắt, kẹt xe khó luồn**. Không có “phương tiện đúng nhất” - chỉ có lựa chọn hợp **hoàn cảnh** (túi tiền, quãng đường, thời tiết). System Design y hệt.

Vài trade-off kinh điển bạn sẽ gặp:

|Đánh đổi|Được|Mất|
|---|---|---|
|**Cache** dữ liệu|Đọc rất nhanh|Có thể trả dữ liệu cũ (không tươi 100%)|
|**SQL vs NoSQL**|SQL: nhất quán mạnh|NoSQL: scale dễ nhưng nhất quán yếu hơn|
|**Nhất quán vs Sẵn sàng** (CAP)|Chọn nhất quán|Phải hy sinh một phần khả năng sẵn sàng khi mạng lỗi|
|**Thêm server** (scale ngang)|Chịu tải cao hơn|Phức tạp hơn, khó đồng bộ dữ liệu|

> ⚠️ Khi phỏng vấn, câu trả lời “chuẩn” KHÔNG phải là “dùng công nghệ X”. Câu trả lời chuẩn là: _“Tôi chọn X vì… nhưng đánh đổi là… còn nếu ưu tiên khác thì tôi sẽ chọn Y.”_ Người phỏng vấn chấm **lập luận về trade-off**, không chấm việc bạn thuộc tên công nghệ.
# 4. Quy trình một buổi phỏng vấn System Design (5 bước)
```markdown
  ┌────────────────────────────────────────────────────────────┐
  │  1. LÀM RÕ YÊU CẦU   ->  hỏi lại! chức năng + phi chức năng │
  │       │                  (functional / non-functional)     │
  │       ▼                                                     │
  │  2. ƯỚC LƯỢNG        ->  số user, QPS, dung lượng lưu trữ   │
  │       │                  (back-of-the-envelope estimation)  │
  │       ▼                                                     │
  │  3. VẼ HIGH-LEVEL    ->  các box lớn + mũi tên: client,     │
  │       │                  LB, server, DB, cache...           │
  │       ▼                                                     │
  │  4. ĐI SÂU COMPONENT ->  chọn 1-2 phần quan trọng đào sâu   │
  │       │                  (schema DB, cách cache, API...)    │
  │       ▼                                                     │
  │  5. BÀN BOTTLENECK   ->  điểm nghẽn ở đâu? trade-off? scale?│
  │                          (single point of failure, tối ưu) │
  └────────────────────────────────────────────────────────────┘
```
Đi qua từng bước:

|Bước|Làm gì|Ví dụ với đề “Thiết kế URL Shortener”|
|---|---|---|
|**1. Làm rõ yêu cầu**|HỎI LẠI trước khi vẽ. Chức năng (rút gọn URL, chuyển hướng) + phi chức năng (nhanh, sẵn sàng cao)|“Cần thống kê lượt click không? Link có hết hạn không?”|
|**2. Ước lượng**|Đoán số user, số request/giây (QPS), dung lượng|“100 triệu link/tháng -> bao nhiêu QPS đọc? Cần bao nhiêu GB?”|
|**3. Vẽ high-level**|Đặt các box lớn và nối mũi tên|Client -> Load Balancer -> App Server -> Database + Cache|
|**4. Đi sâu component**|Chọn phần cốt lõi mổ xẻ|Sinh mã ngắn thế nào? Lưu bảng gì? Cache ở đâu?|
|**5. Bàn bottleneck**|Chỉ ra điểm nghẽn + cách khắc phục|“DB đọc quá tải -> thêm read replica + cache”|

> 💡 **Bước 1 quan trọng nhất mà hay bị bỏ.** Người mới lao vào vẽ ngay sẽ bị coi là “không làm rõ yêu cầu” - điểm trừ nặng. Người phỏng vấn thích ứng viên **hỏi lại** trước khi thiết kế, đúng như dev thật ngoài đời.
# 5. Tư duy scale từ 1 user đến hàng triệu user
Bí quyết lớn nhất của system design: **hệ thống lớn không sinh ra đã lớn - nó lớn dần lên**. Ta hình dung một hành trình tiến hóa:
```
  1 user:            [Client] ── [1 Server + DB chung máy]

  1.000 user:        [Client] ── [App Server] ── [Database riêng]

  100.000 user:      [Client] ─ [Load Balancer] ─┬─ [App Server 1] ─┐
                                                  └─ [App Server 2] ─┴─ [DB] + [Cache]

  1.000.000+ user:   [Client] ─ [CDN] ─ [Load Balancer] ─ [nhiều App Server]
                                  │                            │
                              (ảnh/tĩnh)              [DB master + replica] + [Cache] + [Queue]
```
Các bước “lớn lên” điển hình: tách DB ra máy riêng khi quá tải -> thêm **cache** (Redis) bớt gánh đọc -> thêm **load balancer + nhiều server** (scale ngang) -> thêm **read replica** khi đọc nhiều -> thêm **CDN** cho file tĩnh -> thêm **message queue** xử lý việc nặng bất đồng bộ.

> 🎯 Trong phỏng vấn, bắt đầu bằng thiết kế **đơn giản chạy được**, rồi khi người phỏng vấn tăng quy mô (“giờ 10 triệu user”), bạn **tiến hóa** từng bước. Đừng vẽ ngay hệ thống siêu phức tạp - dễ sa lầy và lộ chỗ hổng.
# 6. Các thành phần cơ bản của một hệ thống
```
   ┌────────┐    ┌─────┐    ┌──────────────┐    ┌────────────┐
   │ Client │───►│ CDN │───►│ Load Balancer│───►│ App Server │
   │(web/app)│   │(tĩnh)│   │  (điều phối) │    │  (xử lý)   │
   └────────┘    └─────┘    └──────────────┘    └─────┬──────┘
                                                      │
                                        ┌─────────────┼─────────────┐
                                        ▼             ▼             ▼
                                   ┌─────────┐   ┌────────┐   ┌──────────┐
                                   │ Database│   │ Cache  │   │  Queue   │
                                   │ (lưu bền)│  │(nhanh) │   │(bất đồng bộ)│
                                   └─────────┘   └────────┘   └──────────┘
```
|Thành phần|Vai trò|Analogy đời thường|
|---|---|---|
|**Client**|Nơi người dùng tương tác (web, app di động)|Khách hàng đứng trước quầy|
|**Server**|Máy xử lý logic, trả kết quả|Nhân viên pha chế xử lý đơn|
|**Database**|Lưu trữ dữ liệu **bền vững** (không mất khi tắt máy)|Kho nguyên liệu + sổ ghi chép|
|**Cache**|Lưu tạm dữ liệu hay dùng để **đọc siêu nhanh**|Quầy sẵn món hot khỏi phải pha lại|
|**Load Balancer**|**Chia** request đều cho nhiều server|Bảo vệ điều phối khách vào quầy trống|
|**CDN**|Đặt bản sao file tĩnh **gần người dùng**|Điểm giao hàng gần nhà khách|
|**Message Queue**|Nhận việc nặng, xử lý **sau** (bất đồng bộ)|Phiếu đặt hàng để làm dần, khách không phải đứng chờ|

> 💡 Đừng cố thuộc lòng ngay - mỗi thành phần có hẳn bài riêng ở các tuần sau. Giờ chỉ cần nhận ra: một hệ thống lớn = ghép các “khối lego” này, mỗi khối giải một vấn đề.
# Nhìn sâu hơn
Một hiểu lầm phổ biến của người mới: nghĩ “thiết kế tốt = dùng nhiều công nghệ xịn”. Thực tế **ngược lại**. Kỹ sư giỏi luôn hỏi _“Mình có THẬT SỰ cần cái này không?”_ trước khi thêm thành phần nào. Mỗi khối lego thêm vào (cache, queue, service…) đều **trả giá**: thêm điểm có thể hỏng, thêm chi phí vận hành, thêm độ phức tạp.

Có câu châm ngôn: **“Đơn giản là đỉnh cao của tinh vi.”** Ứng viên nhồi 10 công nghệ vào một sơ đồ URL shortener thường bị đánh giá **thấp hơn** người vẽ đơn giản nhưng giải thích được vì sao mỗi phần tồn tại - đây là chống **over-engineering** (thiết kế thừa). Nguyên tắc vàng: **thêm thành phần chỉ khi ước lượng chứng minh bạn cần nó**. Đó là lý do bước “ước lượng” đứng trước bước “vẽ kiến trúc” - con số quyết định bạn cần gì, chứ không phải sở thích.
# Thực hành
- **[Dễ]** Vẽ tay sơ đồ 4 box cho một hệ thống bạn quen (ví dụ app đọc tin tức): Client -> Server -> Database, và nói rõ mỗi box làm gì.
- **[Trung bình]** Lấy một ứng dụng bất kỳ (Grab, Shopee…). Với chức năng “xem trang chủ”, hãy chỉ ra chỗ nào **cache** giúp ích, chỗ nào cần **database**, và vì sao có thể cần **CDN**.
- **[Thử thách]** Bạn được hỏi “Thiết kế hệ thống rút gọn URL”. Viết ra **danh sách 5 câu hỏi làm rõ yêu cầu** bạn sẽ hỏi ở Bước 1, và một **ước lượng thô** cho số request đọc mỗi giây nếu có 100 triệu link tạo mỗi tháng và mỗi link được đọc trung bình 100 lần.
# Hiểu lầm thường gặp
- ❌ “System design có một đáp án đúng duy nhất.” → ✅ Sai. Luôn là **trade-off**; câu trả lời tốt = lập luận rõ vì sao chọn và mất gì.
- ❌ “Phải vẽ kiến trúc ngay khi nghe đề.” → ✅ Không. Bước 1 là **làm rõ yêu cầu** (hỏi lại); lao vào vẽ ngay là điểm trừ.
- ❌ “Thiết kế tốt = dùng nhiều công nghệ xịn.” → ✅ Ngược lại. **Đơn giản, đủ dùng** được đánh giá cao hơn; tránh over-engineering.
- ❌ “System design và low-level design (OOD) là một.” → ✅ Khác. HLD nhìn **toàn hệ thống** (service, DB, cache); LLD nhìn **class, method** bên trong.
- ❌ “Cache luôn tốt, cứ thêm là nhanh.” → ✅ Không miễn phí. Cache đổi lấy nguy cơ **dữ liệu cũ** và thêm độ phức tạp - phải cân nhắc.
# Thực hành tốt nhất / Mẹo ghi nhớ
- Gặp đề system design, luôn chạy **5 bước**: làm rõ -> ước lượng -> high-level -> đi sâu -> bottleneck.
- **Nói to suy nghĩ** (think out loud): người phỏng vấn chấm quá trình tư duy, không chỉ kết quả.
- Với mỗi lựa chọn, tự nói câu: _“Tôi chọn X vì…, đánh đổi là…”_ - phản xạ trade-off.
- Bắt đầu **đơn giản**, tiến hóa dần khi quy mô tăng. Đừng vẽ hệ thống khủng ngay từ đầu.
- **Mẹo nhớ 5 bước:** _“Rõ - Lượng - Vẽ - Sâu - Nghẽn”_ (Làm **Rõ** yêu cầu, ước **Lượng**, **Vẽ** high-level, đi **Sâu** component, bàn **Nghẽn** bottleneck).
# Tóm tắt trực quan
|Khái niệm|Một dòng để nhớ|
|---|---|
|System Design|Ghép các thành phần để hệ thống chạy tốt ở quy mô lớn|
|HLD vs LLD|Toàn cảnh (service/DB/cache) vs cận cảnh (class/method)|
|Trade-off|Mọi lựa chọn đều được cái này mất cái kia - không có đáp án duy nhất|
|5 bước phỏng vấn|Rõ - Lượng - Vẽ - Sâu - Nghẽn|
|Scale|Hệ thống lớn dần: DB riêng -> cache -> LB + nhiều server -> replica -> CDN -> queue|
|Thành phần cơ bản|Client, Server, Database, Cache, Load Balancer, CDN, Queue|
# Tài liệu tham khảo
- The System Design Primer (kho học system design nổi tiếng nhất, GitHub): [https://github.com/donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer)
- System design interview - Wikipedia (tiếng Anh): [https://en.wikipedia.org/wiki/Systems_design](https://en.wikipedia.org/wiki/Systems_design)
- Scalability - Wikipedia: [https://en.wikipedia.org/wiki/Scalability](https://en.wikipedia.org/wiki/Scalability)
- AWS - What is System Design (khái niệm nền tảng, tiếng Anh): [https://aws.amazon.com/what-is/](https://aws.amazon.com/what-is/)
- 🎥 Video (ByteByteGo): “20 System Design Concepts Explained in 10 Minutes”: [https://www.youtube.com/watch?v=i53Gi_K3o7I](https://www.youtube.com/watch?v=i53Gi_K3o7I)
- 🎥 Video (Gaurav Sen): “System Design BASICS: Horizontal vs. Vertical Scaling”: [https://www.youtube.com/watch?v=xpDnVSmNFX0](https://www.youtube.com/watch?v=xpDnVSmNFX0)
- 🎥 Kênh ByteByteGo (nhiều video system design chất lượng): [https://www.youtube.com/@ByteByteGo](https://www.youtube.com/@ByteByteGo)
# Sum Up
- **System Design** = ghép các thành phần để hệ thống chạy tốt ở quy mô lớn - kỹ năng cốt lõi để lên senior và vượt vòng phỏng vấn.
- **HLD** nhìn toàn cảnh (service, DB, cache); **LLD** nhìn cận cảnh (class, method). Khóa này tập trung HLD.
- **Trade-off** là linh hồn: không có đáp án đúng duy nhất, chỉ có lựa chọn hợp lý và biết mình đánh đổi gì.
- **5 bước phỏng vấn:** Rõ - Lượng - Vẽ - Sâu - Nghẽn.
- Hệ thống **lớn dần** từ 1 đến hàng triệu user, ghép dần các khối lego: client, server, DB, cache, load balancer, CDN, queue.

Bạn vừa có bức tranh toàn cảnh và bộ đồ nghề. Nhưng trước khi thiết kế bất cứ thứ gì, ta phải trả lời câu hỏi tối quan trọng: **hệ thống này cần làm gì, và cần làm tốt tới mức nào?** Ở **Bài 2 - “Yêu cầu chức năng và phi chức năng”**, ta sẽ học cách bóc tách yêu cầu functional (hệ thống làm gì) và non-functional (nhanh cỡ nào, sẵn sàng ra sao, chịu tải bao nhiêu) - nền móng cho mọi quyết định thiết kế về sau. Hẹn gặp lại! 🚀