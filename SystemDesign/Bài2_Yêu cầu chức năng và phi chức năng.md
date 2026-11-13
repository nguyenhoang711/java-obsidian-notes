Tưởng tượng bạn thuê thợ xây một căn nhà. Nếu bạn chỉ nói “xây cho tôi cái nhà” rồi bỏ đi, kết quả bạn nhận về là một canh bạc: có thể là biệt thự, cũng có thể là cái chòi. Người thợ giỏi sẽ hỏi lại: “Nhà mấy phòng ngủ? (nhà LÀM được gì cho anh), chịu được động đất mấy độ? cách âm tốt không? bao lâu xong?” (nhà TỐT tới đâu). Hai nhóm câu hỏi này chính là linh hồn của mọi buổi phỏng vấn System Design.

Ở **Bài 1**, bạn đã học quy trình 4 bước làm bài phỏng vấn thiết kế hệ thống, và bước **đầu tiên** luôn là **làm rõ yêu cầu (clarify requirements)**. Đừng bao giờ nhảy thẳng vào vẽ box và mũi tên. Bài hôm nay đào sâu đúng cái bước số 1 đó: phân biệt hai loại yêu cầu, cách bóc tách chúng, và cách đặt câu hỏi để thu hẹp phạm vi (scope) trước khi thiết kế.

> 🔑 **Một câu để nhớ:** Yêu cầu **chức năng (functional)** nói hệ thống **LÀM GÌ**; yêu cầu **phi chức năng (non-functional)** nói hệ thống làm việc đó **TỐT ĐẾN ĐÂU**.
# 1. Yêu cầu chức năng (Functional Requirements) - hệ thống LÀM GÌ
**Yêu cầu chức năng** mô tả những **hành vi, tính năng cụ thể** mà hệ thống phải làm được. Cách dễ nhất để nhận ra chúng: mỗi yêu cầu chức năng thường ghép được vào câu “Người dùng có thể **[động từ] [cái gì]**”.

Ví dụ với một app nhắn tin:

- Người dùng có thể **gửi** tin nhắn cho người khác.
- Người dùng có thể **thấy** trạng thái “đã xem”.
- Người dùng có thể **tạo** nhóm chat.

Analogy: yêu cầu chức năng giống **thực đơn** của nhà hàng - liệt kê rõ những món quán có thể làm. Món phở, món cơm, món chè… đó là “quán LÀM ĐƯỢC gì”.

> 💡 Trong phỏng vấn, bạn KHÔNG cần liệt kê mọi tính năng. Hãy hỏi người phỏng vấn để chốt **3-5 tính năng cốt lõi (core features)** và tạm gác các tính năng phụ (nice-to-have). Thiết kế cả rừng tính năng trong 45 phút là bẫy kinh điển khiến bạn không kịp đào sâu cái gì.
# 2. Yêu cầu phi chức năng (Non-Functional Requirements) - hệ thống TỐT ĐẾN ĐÂU
**Yêu cầu phi chức năng** không thêm tính năng mới nào, mà mô tả **chất lượng** của hệ thống: nó nhanh không, chịu tải bao nhiêu, có sập không, mất dữ liệu không… Đây thường là phần **quyết định độ khó** và làm nên chất “kiến trúc” của một thiết kế.

Quay lại analogy nhà hàng: hai quán cùng bán phở (cùng chức năng), nhưng một quán phục vụ trong 3 phút, mở 24/7, không bao giờ hết nguyên liệu; quán kia đợi 40 phút, hay đóng cửa bất chợt. Khác biệt đó chính là **phi chức năng**.

Dưới đây là 7 “trục” phi chức năng bạn phải thuộc lòng:

|Trục (thuật ngữ)|Nghĩa dễ hiểu|Câu hỏi nó trả lời|
|---|---|---|
|**Scalability** (khả năng mở rộng)|Chịu thêm tải khi người dùng tăng|“1 triệu user thì có sập không?”|
|**Availability** (tính sẵn sàng)|Tỷ lệ thời gian hệ thống sống, phục vụ được|“Một năm được phép sập bao lâu?”|
|**Latency** (độ trễ)|Thời gian phản hồi một yêu cầu|“Bấm là ra kết quả trong bao nhiêu ms?”|
|**Consistency** (nhất quán)|Mọi người đọc có thấy cùng một dữ liệu mới nhất?|“Vừa đăng bài, bạn tôi thấy ngay chưa?”|
|**Durability** (bền vững)|Dữ liệu đã ghi có mất khi máy hỏng?|“Server cháy, dữ liệu còn không?”|
|**Reliability** (đáng tin cậy)|Hệ thống chạy đúng, ít lỗi theo thời gian|“Nó có làm sai/mất đơn hàng không?”|
|**Maintainability & Security**|Dễ sửa/nâng cấp; bảo vệ dữ liệu khỏi kẻ xấu|“Có dễ bảo trì và an toàn không?”|

Vài con số neo trực giác (chuẩn ngành, không phải bịa):

- **Availability** thường đo bằng “số 9”. 99.9%99.9% (“three nines”) nghĩa là được phép sập khoảng 8.768.76 giờ/năm; 99.99%99.99% (“four nines”) chỉ còn khoảng 52.652.6 phút/năm.
- **Latency** cho thao tác web tương tác thường nhắm dưới 200200 ms để cảm giác “mượt”.

> ⚠️ **Consistency và Availability hay xung đột nhau** khi hệ thống phân tán (định lý CAP - sẽ học sâu ở tuần sau). Đây chính là lý do bạn PHẢI hỏi rõ: “hệ thống này ưu tiên luôn-đúng-dữ-liệu hay luôn-phản-hồi?”. Không có bữa trưa miễn phí.
# 3. Bóc tách yêu cầu trong phỏng vấn: bộ câu hỏi làm rõ scope
Khi người phỏng vấn ném ra một đề mơ hồ như “Thiết kế Twitter”, đừng hoảng. Bạn có một checklist câu hỏi để **thu hẹp phạm vi**. Mục tiêu: biến một đề khổng lồ thành một bài toán vừa sức trong 45 phút.
```
   ĐỀ MƠ HỒ ("Thiết kế Twitter")
            │
            ▼
   ┌───────────────────────────────────────────┐
   │  HỎI LÀM RÕ SCOPE (Clarifying Questions)   │
   ├───────────────────────────────────────────┤
   │  1. AI dùng?        -> người dùng, use case │
   │  2. BAO NHIÊU user? -> quy mô (DAU/MAU)      │
   │  3. ĐỌC hay GHI     -> read-heavy?           │
   │     nhiều hơn?         write-heavy?          │
   │  4. Tính năng CỐT    -> chốt 3-5 core         │
   │     LÕI vs PHỤ?        gác nice-to-have       │
   │  5. Ràng buộc phi    -> latency? uptime?      │
   │     chức năng?         consistency?           │
   └───────────────────────────────────────────┘
            │
            ▼
   ĐỀ RÕ RÀNG, VỪA SỨC -> bắt đầu thiết kế
```
Giải thích từng câu hỏi:

|Câu hỏi|Vì sao quan trọng|
|---|---|
|**Ai dùng hệ thống?**|Xác định người dùng và các use case chính, tránh làm thừa.|
|**Bao nhiêu user?** (DAU/MAU)|Con số quy mô quyết định kiến trúc: một triệu hay một tỷ là hai thế giới khác nhau.|
|**Đọc nhiều hay ghi nhiều?**|Hệ **read-heavy** (như Twitter timeline) tối ưu bằng cache/replica; **write-heavy** cần chú ý sharding/hàng đợi.|
|**Tính năng cốt lõi vs phụ?**|Chốt phạm vi để đủ thời gian đào sâu; gác tính năng phụ.|
|**Ràng buộc phi chức năng?**|Latency mục tiêu, uptime, mức nhất quán - định hình toàn bộ lựa chọn thiết kế.|

> 🎯 **Mẹo vàng:** đọc-ghi lệch nhau là thông tin đắt giá nhất. Đa số hệ thống social là **read-heavy** (một tweet được đọc bởi hàng triệu người nhưng chỉ ghi một lần) - biết điều này, bạn định hình được ngay chiến lược cache và replica.
# 4. Ước lượng đối tượng cần lưu (Entities)
Sau khi chốt yêu cầu chức năng, bước tiếp là hỏi: “Để làm được những tính năng đó, mình cần **lưu những đối tượng (entity) gì**?”. Đây là cầu nối từ yêu cầu sang mô hình dữ liệu.

Mẹo: mỗi **danh từ chính** trong yêu cầu chức năng thường là một entity cần lưu.

Ví dụ từ yêu cầu “người dùng đăng tweet, follow người khác, xem timeline”:

- **User** (người dùng): id, tên, ngày tạo…
- **Tweet** (bài đăng): id, nội dung, tác giả, thời gian…
- **Follow** (quan hệ theo dõi): ai follow ai…
- **Timeline** (dòng thời gian): danh sách tweet hiển thị cho mỗi user…
```
   Yêu cầu chức năng            Entity cần lưu
   ─────────────────           ───────────────
   "đăng tweet"        ──────►  Tweet
   "follow người khác" ──────►  User, Follow (quan hệ)
   "xem timeline"      ──────►  Timeline (từ User + Follow + Tweet)
```
💡 Ở giai đoạn phỏng vấn, chỉ cần liệt kê entity và vài trường chính. Chưa cần bảng đầy đủ - đó là việc của bước “thiết kế chi tiết” sau này.
# 5. Ví dụ áp dụng: thiết kế mini-Twitter
Ghép tất cả lại. Người phỏng vấn nói “Thiết kế Twitter”. Bạn hỏi làm rõ scope, rồi chốt bảng yêu cầu như sau:

**Yêu cầu chức năng (LÀM GÌ):**

- Người dùng có thể **đăng tweet** (text ngắn).
- Người dùng có thể **follow** người khác.
- Người dùng có thể **xem timeline** gồm tweet của những người mình follow.

**Yêu cầu phi chức năng (TỐT ĐẾN ĐÂU):**

- **Latency:** timeline tải trong khoảng 200200 ms.
- **Availability:** high availability - thà thấy timeline hơi cũ còn hơn không thấy gì (ưu tiên availability hơn consistency chặt).
- **Scalability:** chịu được hàng trăm triệu user, **read-heavy** (đọc timeline nhiều hơn đăng tweet rất nhiều).
- **Durability:** tweet đã đăng không được mất.

**Entity cần lưu:** User, Tweet, Follow, Timeline.

|Loại yêu cầu|Nội dung (mini-Twitter)|
|---|---|
|Chức năng|Đăng tweet, follow, xem timeline|
|Phi chức năng|Latency ≈200≈200 ms, high availability, read-heavy, tweet bền vững|
|Entity|User, Tweet, Follow, Timeline|

Bảng này chính là “hợp đồng” bạn và người phỏng vấn thống nhất trước khi vẽ kiến trúc. Có nó, mọi quyết định sau (cache timeline, chọn DB, sharding…) đều có lý do rõ ràng.
# Nhìn sâu hơn
Một điều dân mới hay bỏ lỡ: **yêu cầu phi chức năng mới là thứ định hình kiến trúc, không phải chức năng.** Hầu như app nào cũng có chức năng na ná nhau (đăng, đọc, thích, theo dõi). Cái khiến thiết kế Twitter khác thiết kế một blog cá nhân chính là **quy mô và các ràng buộc phi chức năng**: hàng trăm triệu user, đọc nhiều gấp bội ghi, cần latency thấp.

Đó là lý do người phỏng vấn giỏi luôn “ép” bạn về con số: “nếu 500 triệu user thì sao?”. Họ không quan tâm bạn liệt kê được bao nhiêu tính năng - họ muốn xem bạn có biết **tính năng nào rẻ, tính năng nào đắt** khi scale không. Ví dụ, “xem timeline” nghe đơn giản nhưng ở quy mô Twitter là bài toán cực khó (fan-out) - đó mới là chỗ đáng đào sâu, chứ không phải nút “đăng tweet”.
# Thực hành
- **[Dễ]** Chọn app WhatsApp (nhắn tin). Viết ra **3 yêu cầu chức năng** dạng “Người dùng có thể…”.
- **[Trung bình]** Cho app đó, liệt kê **4 yêu cầu phi chức năng**, mỗi cái gắn với một trục (latency, availability, consistency, durability). Ghi rõ con số mục tiêu bạn đề xuất.
- **[Thử thách]** Bạn được đề “Thiết kế YouTube”. Viết ra bộ **5 câu hỏi làm rõ scope** bạn sẽ hỏi người phỏng vấn, rồi từ giả định của mình, chốt bảng: chức năng / phi chức năng / entity cần lưu. Hệ này read-heavy hay write-heavy? Vì sao?
# Hiểu lầm thường gặp
- ❌ “Cứ liệt kê thật nhiều tính năng là ghi điểm.” → ✅ Sai. Chốt **3-5 core features** rồi đào sâu; ôm đồm là bẫy hết giờ.
- ❌ “Phi chức năng là thứ yếu, làm sau cũng được.” → ✅ Ngược lại. Phi chức năng (scale, latency, availability) mới **định hình kiến trúc** và quyết định độ khó.
- ❌ “Availability và consistency luôn đi cùng nhau.” → ✅ Không. Trong hệ phân tán chúng thường **đánh đổi** nhau (CAP) - phải hỏi rõ ưu tiên cái nào.
- ❌ “Latency với availability là một.” → ✅ Khác. **Latency** = nhanh cỡ nào (ms); **availability** = sống bao nhiêu % thời gian.
- ❌ “Nhảy vào vẽ kiến trúc ngay cho nhanh.” → ✅ Không. Làm rõ yêu cầu và scope TRƯỚC, nếu không bạn thiết kế nhầm bài toán.
# Thực hành tốt nhất / Mẹo ghi nhớ
- Luôn bắt đầu buổi phỏng vấn bằng **hỏi làm rõ scope**, không vẽ box vội.
- Tách bảng hai cột **Functional (LÀM GÌ)** và **Non-Functional (TỐT ĐẾN ĐÂU)** ngay từ đầu.
- Hỏi 5 câu ruột: **ai dùng, bao nhiêu user, đọc/ghi lệch nhau ra sao, core vs phụ, ràng buộc phi chức năng.**
- **Mẹo nhớ 7 trục phi chức năng - “SALCDRM”:** Scalability, Availability, Latency, Consistency, Durability, Reliability, Maintainability (+ Security). Đọc như một câu thần chú trước mỗi bài.
- Biến danh từ trong yêu cầu chức năng thành **entity** để bắc cầu sang mô hình dữ liệu.
# Tóm tắt trực quan
|Khái niệm|Một dòng để nhớ|
|---|---|
|Yêu cầu chức năng|Hệ thống LÀM GÌ - “Người dùng có thể…”|
|Yêu cầu phi chức năng|Hệ thống TỐT tới đâu - scale/latency/uptime…|
|5 câu làm rõ scope|Ai dùng? Bao nhiêu user? Đọc/ghi? Core vs phụ? Ràng buộc phi chức năng?|
|Read-heavy vs write-heavy|Lệch đọc-ghi định hình cache, replica, sharding|
|Entity|Danh từ trong yêu cầu -> đối tượng cần lưu|
|Availability “số 9”|99.9%99.9% ~ 8.76h sập/năm; 99.99%99.99% ~ 52.6 phút/năm|
# Tài liệu
- System Design Primer - phần “Step 1: Outline use cases, constraints, and assumptions” (Donne Martin, GitHub): [https://github.com/donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer)
- Non-functional requirement - Wikipedia: [https://en.wikipedia.org/wiki/Non-functional_requirement](https://en.wikipedia.org/wiki/Non-functional_requirement)
- Functional requirement - Wikipedia: [https://en.wikipedia.org/wiki/Functional_requirement](https://en.wikipedia.org/wiki/Functional_requirement)
- Availability và “số 9” - Google SRE Book (chương Service Level Objectives): [https://sre.google/sre-book/service-level-objectives/](https://sre.google/sre-book/service-level-objectives/)
- 🎥 Video: “System Design Interview: A Step-By-Step Guide” - ByteByteGo: [https://www.youtube.com/watch?v=i7twT3x5yv8](https://www.youtube.com/watch?v=i7twT3x5yv8)
- 🎥 Video: “7 Must-know Strategies to Scale Your Database” (bối cảnh phi chức năng/scalability) - ByteByteGo: [https://www.youtube.com/watch?v=_1IKwnbscQU](https://www.youtube.com/watch?v=_1IKwnbscQU
# Sum up
- **Yêu cầu chức năng** = hệ thống LÀM GÌ (tính năng, “người dùng có thể…”).
- **Yêu cầu phi chức năng** = hệ thống TỐT tới đâu qua 7 trục: scalability, availability, latency, consistency, durability, reliability, maintainability (+ security).
- Trong phỏng vấn, luôn **hỏi làm rõ scope** bằng 5 câu ruột trước khi thiết kế; đặc biệt chú ý **đọc-ghi lệch nhau** và **quy mô user**.
- Từ yêu cầu chức năng, bóc ra **entity cần lưu** để bắc cầu sang mô hình dữ liệu; ví dụ mini-Twitter: User, Tweet, Follow, Timeline.

Giờ bạn đã chốt được “làm gì” và “tốt tới đâu”, cùng danh sách entity. Nhưng “hàng trăm triệu user”, “read-heavy”, “200 ms” - những con số đó dẫn tới câu hỏi tiếp theo: hệ thống cần bao nhiêu **máy chủ, băng thông, dung lượng lưu trữ**? Ở **Bài 3 - “Ước lượng dung lượng và tải (back-of-the-envelope)”**, bạn sẽ học cách nhẩm nhanh những con số này ngay trên giấy nháp - kỹ năng khiến người phỏng vấn gật gù. Hẹn gặp lại! 🚀