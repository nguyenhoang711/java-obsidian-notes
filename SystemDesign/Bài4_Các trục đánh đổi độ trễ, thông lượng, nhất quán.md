Tưởng tượng bạn đi ăn buffet lẩu. Bạn muốn **món ra thật nhanh**, vừa muốn **bếp phục vụ cả trăm bàn cùng lúc**, lại còn muốn **menu app luôn khớp 100% với món còn trong kho**. Nhưng đời không như mơ: bếp làm nhanh cho bàn bạn thì trăm bàn kia phải chờ; bếp gom đơn xử lý hàng loạt cho hiệu quả thì món bạn ra chậm hơn; menu app “real-time” tuyệt đối thì tốn kém kinh khủng. **Bạn không thể có tất cả cùng lúc - phải chọn, phải đánh đổi.**

Ở [Bài 3](https://learningvn.com/learn/system-design-zero-to-interview/lesson/2f36fc2d-040e-45d8-b559-cf49eb43e931#), bạn đã học **ước lượng “back-of-the-envelope”**: tính ra hệ thống cần bao nhiêu QPS, GB lưu trữ, băng thông. Nhưng ước lượng chỉ cho bạn _các con số_. Câu hỏi tiếp theo mới thú vị: **có con số rồi, giờ chọn thiết kế thế nào?** Đây là sự thật cốt lõi của System Design: **mọi lựa chọn đều có cái giá.** Làm nhanh chỗ này thì chậm chỗ kia; chắc về dữ liệu thì mất tốc độ; rẻ tiền thì hy sinh hiệu năng.

> 🔑 **Một câu để nhớ:** System Design không có đáp án “đúng tuyệt đối”, chỉ có những **đánh đổi (trade-off)** phù hợp với bài toán - và việc của kỹ sư là chọn đúng cái giá để trả.

Bài này dạy các trục đánh đổi quan trọng nhất mà mọi buổi phỏng vấn System Design đều hỏi: **latency vs throughput**, **consistency vs availability**, và cách nói về trade-off một cách chuyên nghiệp.
# 1. Độ trễ (Latency) vs Thông lượng (Throughput)
Đây là cặp khái niệm bị nhầm nhiều nhất, nên ta dùng analogy đời thường: **quầy tính tiền siêu thị**.

- **Độ trễ (Latency)** = thời gian **một** yêu cầu đi từ đầu tới cuối. Là thời gian **riêng bạn** chờ từ lúc vào hàng tới lúc thanh toán xong.
- **Thông lượng (Throughput)** = **số** yêu cầu xử lý xong **trong một đơn vị thời gian**. Là “siêu thị này tính tiền được bao nhiêu khách mỗi phút”.

Hai thứ này **không phải một**:
```
   BĂNG CHUYỀN
   Sản phẩm A ──►[ trạm 1 ]──►[ trạm 2 ]──►[ trạm 3 ]──► xong
                  1 phút        1 phút        1 phút

   LATENCY của A = 3 phút (A đi hết băng chuyền)
   THROUGHPUT    = 3 sp chạy song song (pipeline) -> 1 sp/phút = 60 sp/giờ
```
Điểm mấu chốt: bạn có thể **tăng throughput mà không giảm latency** (thêm băng chuyền song song - mỗi sp vẫn 3 phút, nhưng tổng ra/giờ tăng gấp bội). Hai bài toán khác nhau.

|Tiêu chí|Latency (độ trễ)|Throughput (thông lượng)|
|---|---|---|
|Đo cái gì?|Thời gian xử lý **1** yêu cầu|**Số** yêu cầu/đơn vị thời gian|
|Đơn vị|ms, μs, s|requests/s, QPS, MB/s|
|Người dùng cảm nhận|“App nhanh/chậm”|“Hệ thống chịu được bao nhiêu tải”|
|Cách cải thiện|Cache, CDN, gần user hơn|Thêm server, song song hóa, batch, hàng đợi|
|Analogy|Bạn chờ bao lâu ở quầy|Quầy phục vụ bao nhiêu khách/phút|

> ⚠️ **Bẫy kinh điển:** “Tôi thêm server thì latency sẽ giảm.” SAI trong nhiều trường hợp! Thêm server chủ yếu tăng **throughput** (xử lý được nhiều request hơn), chứ không tự làm **một** request chạy nhanh hơn. Giảm latency cần hướng khác: cache, đặt server gần user, tối ưu truy vấn.
# 2. Latency numbers every programmer should know
Để **ước lượng** thiết kế, bạn cần “cảm giác” về cái gì nhanh, cái gì chậm - lệch nhau hàng nghìn, hàng triệu lần. Bảng số kinh điển (bắt nguồn từ Jeff Dean của Google):

|Thao tác|Thời gian (xấp xỉ)|So sánh tương đối|
|---|---|---|
|Truy cập **L1 cache**|~0.5 - 1 ns|mốc gốc (1x)|
|Truy cập **L2 cache**|~4 - 10 ns|~10x L1|
|Truy cập **RAM** (main memory)|~100 ns|~100x L1|
|Đọc **1 MB** tuần tự từ RAM|~10 - 100 μs||
|Đọc ngẫu nhiên từ **SSD**|~16 - 100 μs|~1.000x RAM|
|**Round-trip trong cùng data center**|~0.5 ms|mạng nội bộ, cùng DC|
|Đọc ngẫu nhiên từ **ổ đĩa cứng (HDD)**|~1 - 10 ms|~10.000x RAM|
|**Round-trip cross-region** (California -> Hà Lan)|~100 - 150 ms|~200.000x round-trip nội DC|

Mẹo nhớ theo **bậc độ lớn** (mỗi bậc chậm hơn khoảng nghìn lần):
```
   NHANH ──────────────────────────────────────► CHẬM
   nanosecond (ns)   microsecond (μs)   millisecond (ms)
   ├─ L1/L2 cache    ├─ RAM đọc 1MB     ├─ cùng DC ~0.5ms
   ├─ RAM ~100ns     └─ SSD đọc         ├─ HDD ~1-10ms
                                        └─ cross-region ~100ms
```
> 🎯 Ba con số VÀNG cần thuộc lòng: **RAM ~100ns**, **round-trip trong data center ~0.5ms**, **round-trip cross-region ~100ms**. Chỉ cần ba mốc này bạn đã ước lượng được đa số bài toán và quyết định đặt cache ở đâu, dùng CDN hay không.

**Bài học thiết kế rút ra:**

- **Đọc memory nhanh gấp hàng chục nghìn lần đọc disk** -> đó là lý do **cache** (giữ data nóng trong RAM) là vũ khí số một để giảm latency.
- **Mạng cross-region cực đắt** (~100ms) -> muốn user toàn cầu nhanh thì **đặt dữ liệu gần họ** (CDN, replica).
- **Đừng gọi mạng/disk trong vòng lặp** - mỗi lần hàng ms, lặp 1000 lần thành hàng giây.

> 💡 Con số cụ thể thay đổi theo phần cứng. Cái **không đổi** là **tỷ lệ tương đối**: cache < RAM < SSD < mạng nội bộ < disk quay < mạng liên vùng. Nhớ _thứ tự_ quan trọng hơn nhớ _con số chính xác_.
# 3. Nhất quán (Consistency) vs Sẵn sàng (Availability)
Analogy: hai chi nhánh ngân hàng ghi chung sổ tiết kiệm của bạn. Bạn rút tiền ở chi nhánh A. Ngay lúc đó, đường truyền giữa A và B **bị đứt**. Chi nhánh B nhận yêu cầu xem số dư - phải làm gì?

- **Chọn nhất quán (Consistency):** B **từ chối phục vụ** (“hệ thống bận, thử lại sau”) vì không chắc số dư mới nhất -> thà không trả lời còn hơn trả lời sai.
- **Chọn sẵn sàng (Availability):** B **vẫn trả lời** bằng số dư cũ đang có -> có thể sai (chưa cập nhật lần rút ở A). Luôn sống, nhưng dữ liệu có thể cũ.
```
   [ Đường mạng ĐỨT giữa 2 node ]
   Node A  ✕────────────✕  Node B
   (vừa ghi)               (nhận đọc)

   CHỌN CONSISTENCY:          CHỌN AVAILABILITY:
   B: "Xin lỗi, tạm ngừng"    B: "Số dư = <giá trị cũ>"
   -> luôn đúng, có thể       -> luôn trả lời, có thể
      từ chối request            trả dữ liệu cũ
```
- **Consistency (Nhất quán):** mọi lần đọc đều thấy dữ liệu **mới nhất**, mọi node “đồng ý”. Kiểu ngân hàng: số dư phải chính xác tuyệt đối.
- **Availability (Sẵn sàng):** mọi request đều **nhận được phản hồi**, hệ thống luôn “sống”. Kiểu mạng xã hội: thà thấy bài chậm vài giây còn hơn báo lỗi.

|Tình huống|Ưu tiên|Vì sao|
|---|---|---|
|Chuyển khoản ngân hàng, đặt vé, kho hàng|**Consistency**|Sai số dư/bán trùng vé là thảm họa|
|Feed mạng xã hội, đếm like, gợi ý|**Availability**|Trễ vài giây không ai chết; sập mới đau|
|Giỏ hàng thương mại điện tử|Tùy phần|Xem sản phẩm: availability; thanh toán: consistency|

> ⚠️ Đây là dẫn nhập của **định lý CAP** (Consistency - Availability - Partition tolerance) bạn sẽ học **sâu ở Tuần 4**: **khi mạng bị chia cắt (partition), hệ phân tán buộc phải chọn** giữa nhất quán và sẵn sàng - không thể có cả hai. Giờ chỉ cần nhớ trực giác “đứt mạng thì phải chọn”.
# 4. Các trục đánh đổi khác
### 4.1. Latency vs Consistency

Muốn **nhất quán mạnh** (strong consistency) thì mỗi lần ghi phải chờ **tất cả bản sao** xác nhận -> **chậm hơn**. Nếu chấp nhận **nhất quán cuối cùng** (eventual consistency - “rồi sẽ đồng bộ, chỉ trễ chút”), ta trả lời ngay từ bản sao gần nhất -> **nhanh hơn** nhưng có thể đọc phải dữ liệu hơi cũ. Ví dụ: bạn đổi ảnh đại diện, bạn bè ở nước khác vài giây sau mới thấy - đổi _một chút_ nhất quán lấy _tốc độ_.

### 4.2. Performance vs Cost (Hiệu năng vs Chi phí)

Muốn nhanh hơn thường phải **trả tiền nhiều hơn**: thêm server, thêm cache RAM, dùng SSD thay HDD, đặt CDN toàn cầu. Câu hỏi thiết kế luôn là: **“giảm 50ms latency này có đáng chi phí gấp đôi không?”** Một startup chấp nhận chậm chút để tiết kiệm; một sàn chứng khoán thì 1ms cũng là tiền tỷ. Mỗi mức hiệu năng cao hơn thường tốn thêm tiền theo cấp số nhân.

> 🎯 Kỹ sư giỏi không phải người làm hệ thống **nhanh nhất** bằng mọi giá, mà là người chọn được **điểm cân bằng đúng** giữa hiệu năng và chi phí cho **đúng bài toán kinh doanh**.
# 5. Không có bữa trưa miễn phí (No Free Lunch)
Nguyên tắc bao trùm: **“There ain’t no such thing as a free lunch”** - mọi lựa chọn đều có **cái giá** ở đâu đó.

- Thêm **cache** -> nhanh hơn, nhưng phải xử lý **dữ liệu cũ** (stale) và tăng độ phức tạp.
- Thêm **replica** để sẵn sàng cao -> tốn tiền lưu trữ và đau đầu chuyện **đồng bộ**.
- Chọn **NoSQL** để mở rộng dễ -> mất phần **truy vấn phức tạp** và giao dịch ACID mạnh.
- Chọn **microservices** để scale từng phần -> gánh thêm **độ phức tạp vận hành** và **latency gọi mạng**.

> 💡 Khi ai đó nói “giải pháp X quá tuyệt, chẳng có nhược điểm gì”, hãy cảnh giác. Nếu bạn **chưa thấy cái giá**, khả năng cao là bạn **chưa nhìn đủ kỹ** - chứ không phải nó miễn phí.
# 6. Cách nói về trade-off trong phỏng vấn
Đây là phần **ăn điểm**. Người phỏng vấn không tìm “đáp án đúng duy nhất” - họ muốn thấy bạn **nhận ra đánh đổi** và **giải thích lựa chọn**. Công thức 3 bước:

1. **Nêu tên đánh đổi:** “Ở đây ta phải cân giữa _consistency_ và _latency_…”
2. **Nói cả hai hướng + cái giá:** “Chọn strong consistency thì mỗi lần ghi chờ tất cả replica -> chậm hơn. Chọn eventual consistency -> nhanh nhưng người dùng có thể thấy dữ liệu cũ vài giây.”
3. **Quyết định theo yêu cầu:** “Vì đây là **feed mạng xã hội**, trễ vài giây chấp nhận được, nên mình chọn eventual consistency để ưu tiên tốc độ và availability.”

> 🎯 Câu thần chú ghi điểm: **“It depends on the requirements”** (còn tùy yêu cầu) - nhưng đừng nói khơi khơi. Luôn theo sau bằng “_tùy vào [yêu cầu cụ thể]_, nên mình chọn [X] vì [lý do]”.
# Nhìn sâu hơn
Nhiều bạn tưởng “cứ giảm latency là tốt”. Nhưng có gotcha: **latency trung bình (average) nói dối**. Nếu 99 request chạy 10ms và 1 request chạy 1000ms, trung bình chỉ ~20ms - nghe đẹp. Nhưng cái request 1000ms đó có thể là **khách VIP đang thanh toán** và bỏ đi vì chờ lâu.

Vì thế dân System Design nói bằng **percentile**: **p50** (trung vị), **p95**, **p99** (99% request nhanh hơn mức này). Hệ thống tốt không chỉ **p50 thấp** mà phải **kiểm soát p99** - vì chính cái đuôi chậm (tail latency) mới làm người dùng bực. Khi phỏng vấn, hỏi “tối ưu cho p50 hay p99?” là dấu hiệu của kỹ sư có kinh nghiệm.
# Thực hành
- **[Dễ]** Với mỗi tình huống, cho biết cần ưu tiên **latency** hay **throughput**: (a) game bắn súng online, (b) hệ thống xử lý log ban đêm gom hàng triệu bản ghi, © tìm kiếm Google gõ tới đâu gợi ý tới đó.
- **[Trung bình]** Thiết kế nút “like” cho mạng xã hội 100 triệu user. Bạn chọn **consistency** hay **availability** cho bộ đếm like? Giải thích cái giá phải trả.
- **[Thử thách]** Dùng bảng latency numbers, ước lượng: một request phải (1) đọc RAM 5 lần, (2) đọc SSD 2 lần, (3) gọi 1 service ở DC khác cùng vùng, (4) gọi 1 API ở châu lục khác. Tổng latency xấp xỉ bao nhiêu? Bước nào là “thủ phạm” chính, tối ưu bước nào trước?
# Hiểu lầm thường gặp
- ❌ “Latency và throughput là một.” → ✅ Khác hẳn. Latency = thời gian **1** request; throughput = **số** request/giây. Thêm server thường tăng throughput chứ không giảm latency của một request.
- ❌ “Thêm server thì mọi thứ nhanh hơn.” → ✅ Thêm server tăng **sức chứa** (throughput); muốn **một** request nhanh hơn cần cache, đặt gần user, tối ưu code/query.
- ❌ “Hệ thống tốt là hệ thống nhất quán tuyệt đối.” → ✅ Nhất quán mạnh tốn latency và có thể giảm availability. Nhiều hệ thống **cố ý** chọn eventual consistency để đổi lấy tốc độ.
- ❌ “Nhanh hơn luôn tốt hơn, đáng làm.” → ✅ Nhanh hơn thường **đắt hơn**. Phải cân performance vs cost cho đúng bài toán, không tối ưu vô tội vạ.
- ❌ “Có giải pháp hoàn hảo, không nhược điểm.” → ✅ No free lunch - nếu chưa thấy cái giá, là bạn chưa nhìn kỹ.
# Thực hành tốt nhất / Mẹo ghi nhớ
- Khi thiết kế, luôn tự hỏi: **“Cái này đang đánh đổi gì lấy gì?”** Không lựa chọn nào miễn phí.
- Thuộc **3 mốc latency vàng**: RAM ~100ns, round-trip trong DC ~0.5ms, cross-region ~100ms.
- **Latency** = “một người chờ bao lâu”; **Throughput** = “phục vụ được bao nhiêu người”.
- Trong phỏng vấn: nêu tên trade-off -> nói cả hai hướng + cái giá -> chọn theo **yêu cầu cụ thể**; nói latency bằng **p50/p95/p99**, đừng chỉ nói “trung bình”.
# Tóm tắt trực quan
|Trục đánh đổi|Một bên|Bên kia|Chọn dựa trên|
|---|---|---|---|
|Latency vs Throughput|Mỗi request nhanh|Tổng lượng lớn|Trải nghiệm tức thời hay chịu tải cao|
|Consistency vs Availability|Dữ liệu luôn đúng|Hệ thống luôn trả lời|Ngân hàng vs mạng xã hội (dẫn tới CAP)|
|Latency vs Consistency|Trả lời nhanh (data cũ)|Chờ đồng bộ (data mới)|Chấp nhận trễ đồng bộ hay không|
|Performance vs Cost|Nhanh, mạnh|Rẻ, tiết kiệm|Ngân sách và giá trị kinh doanh|
|Nguyên tắc chung|\| No Free Lunch|mọi lựa chọn có giá \||Luôn hỏi “đánh đổi gì?”|
# Tài liệu tham khảo
- System Design Primer - Latency numbers every programmer should know: [https://github.com/donnemartin/system-design-primer#latency-numbers-every-programmer-should-know](https://github.com/donnemartin/system-design-primer#latency-numbers-every-programmer-should-know)
- ByteByteGo - Which latency numbers should you know: [https://bytebytego.com/guides/which-latency-numbers-should-you-know/](https://bytebytego.com/guides/which-latency-numbers-should-you-know/)
- CAP theorem - Wikipedia: [https://en.wikipedia.org/wiki/CAP_theorem](https://en.wikipedia.org/wiki/CAP_theorem)
- Latency vs Throughput - High Scalability / AWS glossary: [https://aws.amazon.com/what-is/latency/](https://aws.amazon.com/what-is/latency/)
- 🎥 Video: “Latency Numbers Programmer Should Know: Crash Course System Design #1” (Gaurav Sen): [https://www.youtube.com/watch?v=FqR5vESuKe0](https://www.youtube.com/watch?v=FqR5vESuKe0)
- 🎥 Video: “Latency vs Throughput | System Design Tradeoffs”: [https://www.youtube.com/watch?v=60fxsk13g_k](https://www.youtube.com/watch?v=60fxsk13g_k)
# Sum Up
- **Latency** đo thời gian **một** yêu cầu; **throughput** đo **số** yêu cầu/giây - đừng nhầm.
- Thuộc **latency numbers** (cache < RAM < SSD < mạng nội DC < disk < cross-region) để ước lượng nhanh và biết đặt cache/CDN ở đâu.
- **Consistency vs Availability**: khi mạng đứt phải chọn - đúng dữ liệu hay luôn trả lời (dẫn tới CAP ở Tuần 4).
- Còn có **latency vs consistency**, **performance vs cost** - bao trùm tất cả là **no free lunch**: mọi lựa chọn đều có giá.
- Trong phỏng vấn, ghi điểm bằng cách **gọi tên đánh đổi, nói cả hai hướng, rồi chọn theo yêu cầu cụ thể**.

Bạn giờ đã có “la bàn tư duy” để cân nhắc mọi lựa chọn thiết kế. Nhưng để quyết định nhanh trong phòng phỏng vấn, bạn cần thêm **một bộ con số nền tảng thuộc lòng** và **một quy trình trả lời có kỷ luật**. Ở **Bài 5 - “Các con số kỹ sư nên nhớ và quy trình 4 bước trả lời”**, ta sẽ hệ thống hóa các con số then chốt và học khung 4 bước để “gỡ” bất kỳ bài toán System Design nào một cách bài bản. Hẹn gặp lại! 🚀
