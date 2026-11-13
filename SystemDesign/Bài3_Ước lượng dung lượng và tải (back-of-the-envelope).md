Tưởng tượng bạn sắp mở một quán trà sữa. Trước khi thuê mặt bằng, bạn không cần một bản kế hoạch tài chính 50 trang - bạn chỉ cần lấy tờ giấy nháp và tính nhanh: “Mỗi ngày bán chừng 200 ly, mỗi ly lời 10 nghìn, vậy một tháng lời khoảng 60 triệu, đủ trả mặt bằng 20 triệu và nhân viên 25 triệu”. Vài phép tính trên góc tờ giấy đủ để bạn biết ý tưởng này “có cửa” hay không. Dân thiết kế hệ thống làm y hệt vậy, chỉ khác là họ tính **tải** và **dung lượng** thay vì tiền lời.

Ở **Bài 2**, chúng ta đã học cách làm rõ **yêu cầu** (requirements): hỏi đủ về chức năng, người dùng, ràng buộc. Nhưng “làm rõ yêu cầu” mới trả lời câu _“hệ thống làm gì”_. Bước tiếp theo, ngay sau khi biết rõ nghiệp vụ, là trả lời _“hệ thống to cỡ nào”_: bao nhiêu request mỗi giây, cần bao nhiêu ổ cứng, đường truyền bao rộng, RAM cho cache bao nhiêu. Đó chính là **ước lượng back-of-the-envelope** - kỹ năng bài này.

> 🔑 **Một câu để nhớ:** Back-of-the-envelope estimation là dùng vài phép nhân đơn giản để biết hệ thống của bạn cần bao nhiêu QPS, storage, bandwidth và RAM - đủ để chọn đúng kiến trúc trước khi viết một dòng code
# 1. Back-of-the-envelope estimation là gì và vì sao cần?
Cụm từ này dịch nôm na là “phép tính trên mặt sau phong bì” - ý chỉ những tính toán nhanh, thô, làm bằng đầu hoặc trên mẩu giấy, **không cần chính xác tuyệt đối**, chỉ cần đúng về **bậc độ lớn** (order of magnitude). Đích đến không phải con số đẹp đến từng chữ số, mà là câu trả lời kiểu: “Cần vài chục server hay vài nghìn server? Vài GB hay vài PB?”.

Vì sao dân thiết kế hệ thống cần nó?

- **Chọn kiến trúc đúng ngay từ đầu.** Một hệ thống 100 QPS chạy ngon trên một con server; một hệ thống 1 triệu QPS bắt buộc phải phân tán, sharding, cache.
- **Phát hiện điểm nghẽn (bottleneck) sớm.** Nếu tính ra storage cần 10 PB, bạn biết ngay không thể nhét vào một database - phải nghĩ tới phân mảnh.
- **Ghi điểm khi phỏng vấn.** Người phỏng vấn muốn thấy bạn _tư duy có định lượng_, không “chém gió” chung chung.

> 💡 Tinh thần cốt lõi: **quá trình quan trọng hơn kết quả**. Người phỏng vấn quan tâm cách bạn lập luận và giả định, chứ không bắt bẻ bạn tính ra 11.500 hay 12.000 QPS. Cứ nói to giả định của mình (“mình giả sử mỗi user đăng 2 bài/ngày”).
# 2. Đơn vị dữ liệu và powers of two
Trước khi tính, phải thuộc “bảng cửu chương” của dữ liệu. Máy tính đếm theo lũy thừa của 2, nên các đơn vị nhảy theo bội số 210=1024≈1000210=1024≈1000. Trong ước lượng nhanh, ta **làm tròn 10241024 thành 10001000** cho dễ nhân chia.

|Lũy thừa|Giá trị xấp xỉ|Tên gần đúng|Đơn vị dữ liệu|
|---|---|---|---|
|210210|~1 nghìn (Thousand)|1 K|1 KB (Kilobyte)|
|220220|~1 triệu (Million)|1 M|1 MB (Megabyte)|
|230230|~1 tỉ (Billion)|1 G|1 GB (Gigabyte)|
|240240|~1 nghìn tỉ (Trillion)|1 T|1 TB (Terabyte)|
|250250|~1 triệu tỉ (Quadrillion)|1 P|1 PB (Petabyte)|

Nói cách khác: 210=1K210=1K, 220=1M220=1M, 230=1G230=1G, 240=1T240=1T, 250=1P250=1P. Cứ mỗi bậc là nhân thêm ~1000.

Vài mốc kích thước để “cảm nhận” dữ liệu:
```
1 ký tự ASCII        = 1 byte
1 tweet ngắn (~140)  ~ 140-300 bytes
1 bản ghi user       ~ 1 KB (id, tên, email, avatar url...)
1 ảnh nén (web)      ~ 200 KB - 2 MB
1 phút video 1080p   ~ 30-50 MB
```
⚠️ Đừng nhầm **bit (b)** với **byte (B)**: 1 byte=8 bit1 byte=8 bit. Đường truyền mạng thường đo bằng **bit/giây** (Mbps, Gbps), còn dung lượng lưu trữ đo bằng **byte** (MB, GB). Khi tính bandwidth phải nhớ nhân/chia cho 8.
# 3. Ước lượng QPS (queries per second)
**QPS (queries per second)** là số truy vấn hệ thống nhận mỗi giây - thước đo “tải” quan trọng nhất. Analogy: QPS giống như “số khách bước vào quán mỗi giây” - biết nó bạn mới xếp đủ nhân viên (server).

Công thức nền tảng bắt đầu từ số **DAU (Daily Active Users)** - số người dùng hoạt động mỗi ngày. Một ngày có 8640086400 giây, ta thường **làm tròn thành ≈100,000≈100,000 giây (10^5)** cho dễ tính.

QPS trung bıˋnh=soˆˊ haˋnh động moˆ˜i ngaˋy/ 86400 (soˆˊ giaˆy/ngaˋy)soˆˊ haˋnh động moˆ˜i ngaˋy

**Bước tính QPS ghi (write):**

1. Số hành động ghi/ngày == DAU ×× (số lần ghi mỗi user/ngày).
2. Write QPS == (số hành động ghi/ngày) ÷86400÷86400.

**Bước tính QPS đọc (read):** hầu hết hệ thống mạng xã hội **đọc nhiều hơn ghi rất nhiều**. Ta dùng **tỉ lệ đọc/ghi (read:write ratio)**, ví dụ 100:1100:1 nghĩa là cứ 1 lượt ghi thì có 100 lượt đọc.

Read QPS=Write QPS×(tỉ lệ read:write)Read QPS=Write QPS×(tỉ lệ read:write)

**Đừng quên Peak QPS (giờ cao điểm).** QPS trung bình chỉ là mức nền; lúc đông người, tải vọt lên. Quy tắc ngón tay cái: **Peak QPS ≈2×≈2× đến 3×3× QPS trung bình**. Server phải chịu được peak, không phải mức trung bình.
```
DAU ──(x hành động/ngày)──► Tổng hành động/ngày
                                   │  ÷ 86400
                                   ▼
                            Write QPS (trung bình)
                    ┌──────────────┴──────────────┐
              x (read:write)                  x 2~3 (peak)
                    ▼                              ▼
               Read QPS                      Peak QPS
```
🎯 Ba con số cần nhớ khi được hỏi về tải: **Write QPS**, **Read QPS**, **Peak QPS**. Luôn nêu giả định read:write ratio - nó quyết định độ lệch giữa đọc và ghi.
# 4. Ước lượng storage (dung lượng lưu trữ)
Analogy: tính storage giống tính “cần bao nhiêu kho chứa hàng”. Mỗi món hàng chiếm một chỗ, mỗi ngày nhập thêm hàng, và bạn muốn giữ hàng trong bao lâu.

Công thức tổng quát:

Storage=(soˆˊ record moˆ˜i ngaˋy)×(kıˊch thước 1 record)×(soˆˊ ngaˋy lưu)

Các bước cụ thể:

1. **Số record mới/ngày** == DAU ×× (số record mỗi user tạo/ngày). Đây chính là “số hành động ghi/ngày” ở mục QPS.
2. **Kích thước mỗi record**: cộng các trường (text, metadata, id…). Nếu có ảnh/video thì cộng thêm - phần media thường **áp đảo** phần text.
3. **Thời gian lưu**: đề bài thường yêu cầu giữ dữ liệu vài năm. Nhân với 365365 ngày/năm.

|Bước|Cách tính|
|---|---|
|Dung lượng/ngày|(record/ngày) x (kích thước record)|
|Dung lượng/năm|(dung lượng/ngày) x 365|
|Dung lượng N năm|(dung lượng/năm) x N|

> 💡 Mẹo: tách riêng **text/metadata** và **media (ảnh/video)**. Text thường vài trăm byte - vài KB; media thường vài trăm KB - vài chục MB. Với hệ thống có media, chính media quyết định storage, đừng bỏ sót nó.
# 5. Ước lượng bandwidth (băng thông)
**Bandwidth** là lượng dữ liệu chảy qua mạng mỗi giây (đo bằng byte/s hoặc bit/s). Analogy: bandwidth là “độ rộng của ống nước” - QPS là số lần mở vòi, bandwidth là tổng nước chảy qua ống mỗi giây.

Công thức đơn giản:

Bandwidth=QPS×(kıˊch thước moˆ˜i request/response)

- **Bandwidth ghi (ingress/vào)** == Write QPS ×× (kích thước dữ liệu mỗi lần ghi).
- **Bandwidth đọc (egress/ra)** == Read QPS ×× (kích thước dữ liệu mỗi lần đọc).

Với hệ thống nhiều media (đọc ảnh/video), **egress thường lớn hơn ingress rất nhiều** vì đọc nhiều hơn ghi và mỗi lần đọc kéo cả file media.

> ⚠️ Nhớ đơn vị: nếu nhà cung cấp mạng báo “10 Gbps” (gigabit/s) mà bạn tính storage ra “byte/s”, phải quy đổi: chia byte cho… à nhầm, **nhân byte với 8 để ra bit**. 1 MB/s=8 Mbps1 MB/s=8 Mbps.
# 6. Ước lượng RAM cho cache (quy tắc 80/20)
Cache là bộ nhớ nhanh (RAM) giữ sẵn dữ liệu hay dùng để khỏi phải đọc lại từ database chậm. Nhưng RAM đắt, không thể cache tất cả. Đây là lúc **quy tắc 80/20 (nguyên lý Pareto)** vào cuộc: **khoảng 80% lượng truy cập chỉ nhắm vào ~20% dữ liệu “hot”**. Vậy chỉ cần cache 20% nóng đó là đã phục vụ được phần lớn request.

Analogy: trong thư viện, 80% lượt mượn dồn vào 20% đầu sách bán chạy. Bạn để 20% đó ngay quầy trước (cache RAM), số còn lại cất trong kho (database).

Công thức ước lượng RAM cache:

RAM cache≈20%×(tổng dữ liệu hoặc soˆˊ request/ngaˋy x kıˊch thước item)

Ví dụ: nếu một ngày phục vụ dữ liệu tổng cộng 100 GB, bạn cache ~20% “nóng” là ~20 GB RAM. Rồi làm tròn lên bội số server (mỗi server ~64-256 GB RAM) để biết cần mấy con máy cache.

> 🎯 Quy tắc 80/20 không phải luật vật lý chính xác - nó là kinh nghiệm để **ước lượng nhanh** ngân sách cache. Tỉ lệ thật tùy hệ thống, nhưng 80/20 là điểm khởi đầu hợp lý ai cũng chấp nhận.
# 7. Ví dụ đầy đủ: ước lượng cho hệ thống kiểu Twitter
Giờ ráp tất cả lại. Đề bài: thiết kế một hệ thống tweet kiểu Twitter với các **giả định** sau (luôn nói to giả định!):

- **DAU:** 200200 triệu (200M=2×108200M=2×108).
- Mỗi user đăng **2 tweet/ngày**.
- **Tỉ lệ đọc/ghi:** 100:1100:1 (đọc timeline nhiều hơn đăng bài rất nhiều).
- Mỗi tweet trung bình **300 bytes** (text + metadata; giả sử không tính media để đơn giản).
- **Lưu 5 năm.**

**Bước 1 - Write QPS (số tweet mới mỗi giây):**

Tweet/ngaˋy=200M×2=400M=4×108Tweet/ngaˋy=200M×2=400M=4×108

Write QPS=4×10886400≈4×108105≈4000 QPSWrite QPS=864004×108​≈1054×108​≈4000 QPS

**Bước 2 - Read QPS:**

Read QPS=4000×100=400,000 QPSRead QPS=4000×100=400,000 QPS

**Peak Read QPS** (nhân 2): ≈800,000≈800,000 QPS. Con số này ngay lập tức cho biết: **bắt buộc phải cache mạnh + phân tán**, một database đơn không kham nổi.

**Bước 3 - Storage cho 5 năm:**

Bytes/ngaˋy=400M tweet×300 bytes=1.2×1011 bytes=120 GB/ngaˋyBytes/ngaˋy=400M tweet×300 bytes=1.2×1011 bytes=120 GB/ngaˋy

Storage 5 na˘m=120 GB×365×5≈219,000 GB≈219 TBStorage 5 na˘m=120 GB×365×5≈219,000 GB≈219 TB

Làm tròn: **~220 TB cho 5 năm** (chỉ riêng text tweet). Nếu tính thêm ảnh/video thì con số vọt lên hàng chục PB.

**Bước 4 - Bandwidth:**

Ingress (ghi)=4000 QPS×300 bytes=1.2 MB/sIngress (ghi)=4000 QPS×300 bytes=1.2 MB/s

Egress (đọc)=400,000 QPS×300 bytes=120 MB/s≈960 MbpsEgress (đọc)=400,000 QPS×300 bytes=120 MB/s≈960 Mbps

**Bước 5 - RAM cache (80/20):** áp quy tắc 80/20, chỉ cache ~20% tweet “hot” được đọc nhiều nhất. Nếu tập “hot” cỡ ~vài trăm GB thì cache ~20% ≈≈ vài chục GB RAM →→ cần một cụm vài server cache.

**Bảng tổng hợp kết quả:**

|Chỉ số|Kết quả ước lượng|
|---|---|
|Write QPS|~4.000|
|Read QPS|~400.000 (peak ~800.000)|
|Storage/ngày|~120 GB|
|Storage 5 năm|~220 TB (chưa tính media)|
|Bandwidth đọc (egress)|~120 MB/s (~960 Mbps)|
|RAM cache (80/20)|~vài chục GB (cụm cache)|

> 💡 Nhìn bảng này, bạn “đọc” ra ngay thiết kế: read:write lệch 100:1100:1 →→ cần **cache + read replica**; 220 TB →→ cần **sharding**; 800K peak QPS →→ cần **load balancer + nhiều server**. Đó là sức mạnh của back-of-the-envelope: vài phép nhân dẫn thẳng tới quyết định kiến trúc.
# Nhìn sâu hơn
Một cái bẫy tinh vi: **làm tròn quá nhiều dồn lại có thể sai một bậc độ lớn**. Làm tròn 86400 giây thành 100,000 khiến QPS nhỏ hơn thực tế ~15%; làm tròn 1024 thành 1000 khiến dung lượng nhỏ đi vài phần trăm mỗi bậc. Từng cái nhỏ, nhưng cộng dồn thì lệch đáng kể. Mẹo an toàn: **làm tròn theo hướng “over-estimate” (ước lượng dư)** khi tính công suất - thà chuẩn bị dư server còn hơn thiếu.

Điểm sâu thứ hai: đừng chỉ tính con số trung bình rồi dừng. Hệ thống thật sống chết ở **peak** và ở **phân bố lệch (skew)**: một tweet của người nổi tiếng (hot key) có thể tạo hàng triệu lượt đọc trong vài phút - vượt xa mọi con số trung bình. Ước lượng cho bạn mức nền; kiến trúc thật phải xử lý cả những đỉnh nhọn đó (chủ đề các bài sau về cache, replica, rate limiting).
# Thực hành
- **[Dễ]** Một app chat có 1010 triệu DAU, mỗi user gửi 4040 tin/ngày. Tính **Write QPS** trung bình (dùng 86400≈10586400≈105).
- **[Trung bình]** Cũng app chat trên, tỉ lệ đọc/ghi là 10:110:1, mỗi tin nhắn ~100 bytes, lưu 3 năm. Tính **Read QPS**, **storage 3 năm** và **bandwidth đọc**.
- **[Thử thách]** Thiết kế ước lượng cho một hệ thống chia sẻ ảnh kiểu Instagram: 500500 triệu DAU, mỗi user đăng 11 ảnh/ngày (~2 MB/ảnh sau nén), xem 5050 ảnh/ngày, lưu 5 năm. Tính write/read QPS, storage 5 năm (chú ý media áp đảo!), và egress bandwidth. So sánh storage với ví dụ Twitter và giải thích vì sao chênh lệch lớn.
# Hiểu lầm thường gặp
- ❌ “Ước lượng phải chính xác đến từng con số.” → ✅ Sai. Chỉ cần đúng **bậc độ lớn**; quá trình lập luận quan trọng hơn kết quả.
- ❌ “QPS trung bình là đủ để thiết kế.” → ✅ Không. Phải tính **Peak QPS** (22-3×3× trung bình) vì server phải chịu được giờ cao điểm.
- ❌ “Read QPS xấp xỉ Write QPS.” → ✅ Hầu hết hệ thống xã hội **đọc gấp hàng chục đến hàng trăm lần ghi**; luôn hỏi tỉ lệ read:write.
- ❌ “1 KB = 1000 byte chính xác.” → ✅ Thực ra 1 KB=10241 KB=1024 byte (210210); ta chỉ **làm tròn** thành 1000 cho dễ tính nhanh.
- ❌ “Cache phải giữ 100% dữ liệu.” → ✅ Không. Theo 80/20, chỉ cache ~20% dữ liệu “hot” là phục vụ được ~80% truy cập.
- ❌ “Bandwidth và storage cùng đơn vị.” → ✅ Cẩn thận bit vs byte: mạng đo bằng **bit/s**, lưu trữ đo bằng **byte**; 1 byte=8 bit1 byte=8 bit.
# Thực hành tốt nhất / Mẹo ghi nhớ
- **Luôn nói to giả định** (DAU, số hành động/ngày, read:write, kích thước record, thời gian lưu) trước khi nhân chia.
- Nhớ hằng số vàng: **86400≈10586400≈105 giây/ngày**, **11 năm ≈365≈365 ngày**, **peak ≈2≈2-3×3× trung bình**.
- Thuộc powers of two: 2^10=1K,2^20=1M,2^30=1G,2^40=1T,2^50=1P
- Trình tự 4 bước: **QPS → Storage → Bandwidth → RAM cache**. Cứ theo thứ tự này là không sót.
- **Mẹo nhớ:** _“DAU chia giây ra QPS, record nhân ngày ra storage, QPS nhân size ra bandwidth, 80/20 ra cache.”_
# Tóm tắt trực quan
|Đại lượng|Công thức một dòng|
|---|---|
|Write QPS|(DAU x ghi/user/ngày) ÷÷ 86400|
|Read QPS|Write QPS x (tỉ lệ read:write)|
|Peak QPS|QPS trung bình x 2 đến 3|
|Storage|(record/ngày) x (size record) x (số ngày lưu)|
|Bandwidth|QPS x (size mỗi request/response)|
|RAM cache|~20% dữ liệu “hot” (quy tắc 80/20)|
|Powers of two|$2^{10}$K, $2^{20}$M, $2^{30}$G, $2^{40}$T, $2^{50}$P|
# Tài liệu tham khảo
- Back-of-the-envelope Estimation - ByteByteGo (System Design Interview): [https://bytebytego.com/courses/system-design-interview/back-of-the-envelope-estimation](https://bytebytego.com/courses/system-design-interview/back-of-the-envelope-estimation)
- System Design Primer - “Powers of two” và “Latency numbers every programmer should know” (GitHub): [https://github.com/donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer)
- Numbers Everyone Should Know - Jeff Dean (Google): [https://static.googleusercontent.com/media/research.google.com/en//people/jeff/stanford-295-talk.pdf](https://static.googleusercontent.com/media/research.google.com/en//people/jeff/stanford-295-talk.pdf)
- Back-of-the-envelope calculation - Wikipedia: [https://en.wikipedia.org/wiki/Back-of-the-envelope_calculation](https://en.wikipedia.org/wiki/Back-of-the-envelope_calculation)
- 🎥 Video (tiếng Anh): “Back Of The Envelope Estimation / Capacity Planning” - Gaurav Sen: [https://www.youtube.com/watch?v=UC5xf8FbdJc](https://www.youtube.com/watch?v=UC5xf8FbdJc)
- 🎥 Video (tiếng Anh): “Back of the Envelope | System Design Essentials”: [https://www.youtube.com/watch?v=TWFPGGqScpg](https://www.youtube.com/watch?v=TWFPGGqScpg)
# Sum Up
- **Back-of-the-envelope estimation** là vài phép nhân/chia đơn giản để biết hệ thống cần bao nhiêu **QPS, storage, bandwidth, RAM cache** - đủ để chọn đúng kiến trúc.
- Thuộc **powers of two** (210=1K210=1K … 240=1T240=1T) và hằng số vàng 86400≈10586400≈105 giây/ngày.
- Trình tự 4 bước: **QPS** (từ DAU + read:write + peak) → **Storage** (record x size x số ngày) → **Bandwidth** (QPS x size) → **RAM cache** (80/20).
- Ví dụ Twitter ($200$M DAU) cho ra ~4K write QPS, ~400K read QPS, ~220 TB/5 năm - từ đó “đọc” thẳng ra nhu cầu cache, sharding, load balancer.

Chúng ta đã biết hệ thống **to cỡ nào**. Nhưng khi bắt tay thiết kế, bạn sẽ liên tục phải chọn: nhanh hơn nhưng tốn hơn? nhất quán hơn nhưng chậm hơn? Ở **Bài 4 - “Các trục đánh đổi: độ trễ, thông lượng, nhất quán”**, ta sẽ học ba trục đánh đổi (trade-off) cốt lõi mà mọi quyết định thiết kế hệ thống đều xoay quanh. Hẹn gặp lại!
