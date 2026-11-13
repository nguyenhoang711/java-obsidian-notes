Hãy tưởng tượng bạn đi khám sức khỏe. Bác sĩ giỏi không cần tra sách để biết “nhịp tim 72 là bình thường, 180 là báo động”. Những con số đó nằm sẵn trong đầu họ, nên khi nhìn kết quả xét nghiệm, họ **ước lượng nhanh** được bệnh nhân ổn hay nguy. Kỹ sư System Design cũng vậy: có một bộ “chỉ số sinh tồn” của hệ thống mà bạn cần thuộc lòng - để khi nghe “thiết kế Twitter cho 300 triệu người dùng”, bạn ước lượng được ngay là cần bao nhiêu server, bao nhiêu băng thông, chứ không đứng hình.

Ở **Bài 4**, bạn đã hiểu rằng mọi quyết định thiết kế đều là **trade-off** (đánh đổi): nhanh hơn thì tốn RAM hơn, nhất quán hơn thì chậm hơn… Nhưng làm sao biết đánh đổi bao nhiêu là “đủ”? Câu trả lời nằm ở **con số**. Bài này ta hệ thống hóa hai thứ: (1) bộ **số liệu tham chiếu** mọi kỹ sư nên nhớ (độ trễ, độ sẵn sàng, quy mô), và (2) một **quy trình 4 bước** để trả lời bất kỳ câu hỏi system design nào một cách bài bản, tự tin.

> 🔑 **Một câu để nhớ:** Thuộc lòng vài con số nền tảng + đi theo quy trình 4 bước = bạn biến câu hỏi system design mơ hồ thành một cuộc thảo luận có cấu trúc, thay vì đoán mò.

Đây cũng là bài **chốt Tuần 1**. Sau bài này bạn có đủ “vốn liếng tư duy” để bước vào Tuần 2 học chi tiết các thành phần.
# 1. Bảng độ trễ (Latency Numbers) mọi kỹ sư nên nhớ
Analogy: hãy coi mỗi thao tác trong máy tính như một chuyến đi. Đọc từ **cache CPU** giống với đi từ phòng ngủ ra phòng khách (chớp mắt là tới). Đọc từ **RAM** giống đi ra đầu ngõ. Đọc từ **ổ SSD** giống đi sang quận khác. Gọi qua **mạng giữa hai trung tâm dữ liệu** giống bay xuyên lục địa. Cùng là “lấy dữ liệu”, nhưng chênh nhau hàng triệu lần về thời gian!

Bộ số kinh điển này (phổ biến từ Jeff Dean, Google) không cần thuộc chính xác từng nanosecond - chỉ cần nhớ **thứ hạng độ lớn** (order of magnitude):

|Thao tác|Độ trễ xấp xỉ|Trực giác|
|---|---|---|
|Tham chiếu L1 cache|~1 ns|Cực nhanh, coi như tức thì|
|Tham chiếu L2 cache|~4 ns|Vẫn trong CPU|
|Đọc 1MB tuần tự từ RAM|~100.000 ns (~0.1 ms)|Rất nhanh|
|Đọc 1MB tuần tự từ SSD|~1.000.000 ns (~1 ms)|Nhanh|
|Ổ cứng HDD tìm kiếm (seek)|~10.000.000 ns (~10 ms)|Chậm hơn RAM ~100.000 lần|
|Round trip trong cùng datacenter|~500.000 ns (~0.5 ms)|Nhanh nếu cùng phòng máy|
|Gói tin gửi CA -> Hà Lan -> CA|~150.000.000 ns (~150 ms)|Rất chậm: bị giới hạn tốc độ ánh sáng|

Quy đổi cho dễ nhớ (mẹo “1 ns = 1 giây” - phóng đại thời gian lên tỉ lệ con người):
```
   Nếu 1 ns = 1 giây, thì:
   L1 cache (1 ns)          -> 1 giây          (chớp mắt)
   RAM 1MB (100.000 ns)     -> ~1 ngày
   SSD 1MB (1.000.000 ns)   -> ~11 ngày
   HDD seek (10.000.000 ns) -> ~4 tháng
   Mạng liên lục địa (150ms)-> ~4.75 năm !!!
```
> 💡 Bài học lớn nhất từ bảng này: **truy cập bộ nhớ nhanh gấp cả trăm nghìn lần truy cập đĩa; truy cập trong RAM nhanh hơn gọi mạng liên vùng cả nghìn lần.** Vì vậy chiến lược tối ưu số 1 là _giảm số lần chạm đĩa và số lần đi mạng xa_ - đó chính là lý do **cache** tồn tại.

⚠️ Đừng học vẹt từng số. Cái đáng nhớ là **quan hệ**: RAM > SSD > HDD > mạng xa, và mỗi bậc chậm hơn bậc trên khoảng 10-1000 lần.
# 2. Availability và các "mốc 9"
Analogy: một quán ăn mở cửa “24/7” nghe rất oách, nhưng nếu mỗi tháng nó đóng bất chợt vài tiếng để sửa bếp, khách vẫn bực. **Availability (độ sẵn sàng)** đo chính xác điều này: trong tổng thời gian, hệ thống thực sự **hoạt động được** bao nhiêu phần trăm.

Công thức đơn giản:
Availability=Uptime / Uptime+Downtime​
Người ta hay nói về **“số 9”** (nines): 99% là “hai số 9”, 99.9% là “ba số 9”… Càng nhiều số 9, hệ thống càng ít chết. Nhưng cái “nghe có vẻ nhỏ” ấy khác biệt khổng lồ về downtime:

|Availability|Tên gọi|Downtime / năm|Downtime / tháng|
|---|---|---|---|
|99%|“two nines”|~3.65 ngày|~7.2 giờ|
|99.9%|“three nines”|~8.76 giờ|~43.8 phút|
|99.99%|“four nines”|~52.6 phút|~4.38 phút|
|99.999%|“five nines”|~5.26 phút|~26 giây|
```
   99%     ██████████████████░░  ~3.65 NGÀY chết/năm
   99.9%   ███████████████████░  ~8.76 GIỜ chết/năm
   99.99%  ████████████████████  ~52.6 PHÚT chết/năm
   99.999% ████████████████████  ~5.26 PHÚT chết/năm  (rất khó & rất đắt)
```
Cách tính nhanh: 1 năm có 365×24=8760365×24=8760 giờ. Với 99.9% (mất 0.1%), downtime = 8760×0.001=8.768760×0.001=8.76 giờ. Với 99.99%, downtime = 8760×0.0001=0.8768760×0.0001=0.876 giờ ≈52.6≈52.6 phút.

> 🎯 Mỗi số 9 thêm vào thường **đắt gấp bội** (thêm redundancy, failover, đội trực…). Trong phỏng vấn, hãy hỏi rõ mục tiêu: hệ thống ngân hàng cần “five nines”, nhưng blog cá nhân thì “two nines” là quá đủ. Đừng vẽ vời “five nines” cho một tính năng không cần.

⚠️ Đừng nhầm **availability** (hệ thống có chạy không) với **reliability** (khi chạy thì có đúng không). Một hệ thống có thể “up” 100% thời gian nhưng trả về kết quả sai - up mà vô dụng.
# 3. Các con số quy mô hữu ích để ước lượng
Analogy: đầu bếp giỏi ước lượng “một nồi này nấu được khoảng 40 tô” mà không cần cân từng gram. Kỹ sư cũng cần vài “hằng số bỏ túi” để nhẩm nhanh quy mô hệ thống:

|Con số cần nhớ|Giá trị|Dùng để làm gì|
|---|---|---|
|Số giây trong 1 ngày|~86.400 (làm tròn ~105105)|Đổi “số request/ngày” sang QPS|
|1 triệu request/ngày|~12 QPS|Cảm nhận “1 triệu” thực ra khá nhỏ|
|1 tỉ request/ngày|~11.600 QPS|Quy mô lớn thật sự|
|1 byte ký tự ASCII|1 byte|Ước lượng kích thước text|
|1 dòng tweet/bình luận ngắn|~100-300 byte|Ước lượng storage|
|1 ảnh nén|~200 KB - vài MB|Ước lượng storage media|
|Tỉ lệ đọc/ghi mạng xã hội|thường ~100:1 (đọc nhiều hơn)|Quyết định cache & replica|

Ví dụ nhẩm nhanh: “Thiết kế hệ thống có **86 triệu** request/ngày” -> chia cho ~86400 giây -> **~1000 QPS** trung bình. Nhưng lưu lượng không đều: giờ cao điểm có thể gấp 2-3 lần trung bình -> ước lượng **peak ~2000-3000 QPS**. Chỉ vài phép chia, bạn đã có con số để bàn về số server.
```
   Số request/ngày  ÷ 86.400 (giây/ngày)  = QPS trung bình
   QPS trung bình    × 2~3 (hệ số cao điểm) = QPS đỉnh (peak)
   -> Từ QPS đỉnh mới ước lượng số server, băng thông, storage
```
# 4. Quy trình 4 bước trả lời câu hỏi System Design
Đây là phần “xương sống” của bài. Khi được hỏi “Thiết kế một hệ thống rút gọn URL (như [bit.ly](http://bit.ly/))”, đừng lao vào vẽ database ngay. Hãy đi theo **4 bước** như một kịch bản có sẵn:
```
   ┌──────────────────────────────────────────────────────────────┐
   │  BƯỚC 1: LÀM RÕ YÊU CẦU & SCOPE                                │
   │    - Chức năng (functional): làm được gì?                      │
   │    - Phi chức năng (non-functional): nhanh? sẵn sàng? nhất quán?│
   │    - Ai dùng? Bao nhiêu người? Giới hạn phạm vi (out of scope) │
   └───────────────────────────┬──────────────────────────────────┘
                               ▼
   ┌──────────────────────────────────────────────────────────────┐
   │  BƯỚC 2: ƯỚC LƯỢNG QUY MÔ (Back-of-envelope)                   │
   │    - QPS (đọc/ghi), số user, dữ liệu/ngày                      │
   │    - Storage cần trong 1 năm, băng thông                       │
   │    - Dùng các con số ở mục 1-3 để nhẩm                         │
   └───────────────────────────┬──────────────────────────────────┘
                               ▼
   ┌──────────────────────────────────────────────────────────────┐
   │  BƯỚC 3: THIẾT KẾ HIGH-LEVEL + API + DATA MODEL                │
   │    - Vẽ sơ đồ khối: client -> LB -> service -> DB/cache        │
   │    - Định nghĩa API chính (endpoint, input/output)            │
   │    - Thiết kế bảng dữ liệu / schema cốt lõi                    │
   └───────────────────────────┬──────────────────────────────────┘
                               ▼
   ┌──────────────────────────────────────────────────────────────┐
   │  BƯỚC 4: ĐI SÂU, TÌM BOTTLENECK, BÀN TRADE-OFF & SCALE         │
   │    - Chỗ nào sẽ nghẽn khi tải tăng? (DB? single point?)       │
   │    - Thêm cache, replica, sharding, queue...                  │
   │    - Nêu trade-off cho từng lựa chọn (Bài 4!)                 │
   └──────────────────────────────────────────────────────────────┘
```
Giải thích từng bước:

|Bước|Tên|Việc chính|Sai lầm cần tránh|
|---|---|---|---|
|1|**Làm rõ yêu cầu & scope**|Hỏi lại người phỏng vấn, chốt chức năng + phi chức năng, khoanh vùng|Nhảy vào thiết kế ngay khi chưa hiểu đề|
|2|**Ước lượng quy mô**|Nhẩm QPS, storage, băng thông bằng các con số đã học|Bỏ qua, thiết kế “chay” không biết to nhỏ|
|3|**High-level + API + data model**|Vẽ sơ đồ khối, định nghĩa API, thiết kế schema|Đi vào tiểu tiết quá sớm, quên bức tranh lớn|
|4|**Đi sâu & trade-off**|Tìm bottleneck, thêm cache/replica/shard, bàn đánh đổi|Chỉ mô tả “thêm cache” mà không nói vì sao|

Analogy để nhớ trình tự: giống **xây nhà**. Bước 1 = hỏi chủ nhà muốn nhà mấy phòng, ở mấy người (yêu cầu). Bước 2 = tính diện tích, số vật liệu (ước lượng). Bước 3 = vẽ bản vẽ tổng thể + bố trí phòng (high-level + API). Bước 4 = chọn móng, vật liệu chịu lực cho chỗ yếu nhất (đi sâu, xử lý bottleneck). Không ai xây nhà bằng cách đặt viên gạch đầu tiên rồi mới nghĩ.

> 🎯 Trong phỏng vấn, **luôn nói to quy trình bạn đang đi**: “Đầu tiên em làm rõ yêu cầu… Tiếp theo em ước lượng quy mô…”. Người phỏng vấn đánh giá **cách bạn tư duy có hệ thống** hơn là một đáp án hoàn hảo.

# Nhìn sâu hơn
Nhiều bạn tưởng bước 2 (ước lượng) là để ra con số chính xác. **Sai.** Mục đích thật của “back-of-envelope estimation” là để **định hình quyết định thiết kế**, không phải để đúng số. Ví dụ: nếu ước lượng ra chỉ ~10 QPS, bạn không cần load balancer phức tạp hay sharding - một server + một DB là đủ. Nhưng nếu ra ~100.000 QPS, bạn buộc phải nghĩ tới cache tầng, nhiều replica, sharding. **Con số quyết định kiến trúc.**

Đây cũng là chỗ bước 2 nối liền với Bài 4: mỗi lần thêm một thứ ở bước 4 (cache, replica…) là một **trade-off**. Cache -> nhanh hơn nhưng có nguy cơ dữ liệu cũ (stale). Nhiều replica -> đọc khỏe nhưng khó nhất quán. Người phỏng vấn giỏi sẽ “đào” đúng những chỗ này. Nếu bạn đã quen tư duy trade-off + có con số trong đầu, bạn sẽ trả lời trôi chảy thay vì lúng túng.
# Thực hành
- **[Dễ]** Không nhìn bảng, hãy nhớ và ghi lại: 99.9% availability tương ứng khoảng bao nhiêu giờ downtime/năm? 99.99% thì bao nhiêu phút/năm?
- **[Trung bình]** Một app có **43 triệu** request/ngày. Ước lượng QPS trung bình. Nếu giờ cao điểm gấp 3 lần trung bình, QPS đỉnh khoảng bao nhiêu?
- **[Thử thách]** Áp dụng quy trình 4 bước để phác thảo hệ thống **rút gọn URL** ([bit.ly](http://bit.ly/)): (1) liệt kê 3 yêu cầu chức năng + 2 phi chức năng; (2) ước lượng nếu có 100 triệu URL tạo mới/năm và tỉ lệ đọc/ghi 100:1; (3) vẽ sơ đồ khối + 2 API chính; (4) chỉ ra 1 bottleneck và cách scale kèm trade-off.
# Hiểu lầm thường gặp
- ❌ “Phải nhớ chính xác từng nanosecond trong bảng latency.” → ✅ Chỉ cần nhớ **thứ hạng độ lớn** và quan hệ RAM > SSD > HDD > mạng xa.
- ❌ “99% availability là quá tốt rồi, gần như hoàn hảo.” → ✅ 99% nghĩa là **chết ~3.65 ngày/năm** - rất tệ với dịch vụ nghiêm túc.
- ❌ “1 triệu request/ngày là quy mô khổng lồ.” → ✅ Chỉ ~12 QPS - một server bình thường xử lý thoải mái.
- ❌ “Cứ vẽ database trước cho nhanh.” → ✅ Phải **làm rõ yêu cầu (bước 1)** và **ước lượng quy mô (bước 2)** trước, nếu không dễ thiết kế sai kích cỡ.
- ❌ “Availability và reliability là một.” → ✅ Availability = có chạy không; reliability = chạy có đúng không. Khác nhau.
# Thực hành tốt nhất / Mẹo ghi nhớ
- Nhớ latency bằng mẹo phóng đại **“1 ns = 1 giây”**: RAM ~1 ngày, mạng liên lục địa ~4.75 năm.
- Nhớ availability: mỗi thêm 1 số 9 thì downtime **chia cho 10** (3.65 ngày -> 8.76 giờ -> 52.6 phút -> 5.26 phút).
- Nhớ quy mô: **1 triệu req/ngày ≈ 12 QPS**, và **chia số req/ngày cho ~86400** để ra QPS.
- Nhớ 4 bước bằng câu: **“Hỏi rõ -> Nhẩm số -> Vẽ khung + API -> Đào sâu trade-off.”**
- Trong phỏng vấn: **nói to từng bước bạn đang làm**; hỏi lại khi đề mơ hồ - đó là điểm cộng, không phải điểm trừ.
# Tóm tắt trực quan
|Nhóm|Con số / ý chính cần nhớ|
|---|---|
|Latency|RAM ~0.1 ms · SSD ~1 ms · HDD seek ~10 ms · mạng liên lục địa ~150 ms|
|Availability|99% -> 3.65 ngày · 99.9% -> 8.76 giờ · 99.99% -> 52.6 phút · 99.999% -> 5.26 phút (mỗi số 9 chia 10)|
|Quy mô|1 ngày ~86400 giây · 1 triệu req/ngày ~12 QPS · đọc/ghi MXH ~100:1|
|Quy trình 4 bước|1) Làm rõ yêu cầu & scope · 2) Ước lượng quy mô · 3) High-level + API + data model · 4) Đi sâu, bottleneck, trade-off|
# Tai liệu tham khảo
- Latency Numbers Every Programmer Should Know (bản tương tác) - Colin Scott: [https://colin-scott.github.io/personal_website/research/interactive_latency.html](https://colin-scott.github.io/personal_website/research/interactive_latency.html)
- System Design Primer - Latency numbers & back-of-envelope: [https://github.com/donnemartin/system-design-primer#latency-numbers-every-programmer-should-know](https://github.com/donnemartin/system-design-primer#latency-numbers-every-programmer-should-know)
- Availability (nines) - Wikipedia: [https://en.wikipedia.org/wiki/High_availability#Percentage_calculation](https://en.wikipedia.org/wiki/High_availability#Percentage_calculation)
- Back-of-the-envelope estimation - System Design Primer: [https://github.com/donnemartin/system-design-primer#back-of-the-envelope-calculations](https://github.com/donnemartin/system-design-primer#back-of-the-envelope-calculations)
- 🎥 Video: “System Design Interview - Step By Step Guide” - ByteByteGo: [https://www.youtube.com/watch?v=i7twT3x5yv8](https://www.youtube.com/watch?v=i7twT3x5yv8)
- 🎥 Video: “Latency Numbers Programmer Should Know” - Gaurav Sen (kênh): [https://www.youtube.com/@GauravSensei](https://www.youtube.com/@GauravSensei)
# Sum Up
- Thuộc lòng bộ **con số nền tảng**: latency (RAM/SSD/HDD/mạng), availability (các mốc 9), và quy mô (86400 giây/ngày, 1 triệu req ~12 QPS) giúp bạn **ước lượng nhanh** thay vì đoán mò.
- Mỗi **số 9** thêm vào availability làm downtime **giảm 10 lần** nhưng chi phí tăng vọt - chọn mức phù hợp nhu cầu.
- **Quy trình 4 bước** (làm rõ yêu cầu -> ước lượng -> high-level + API + data model -> đi sâu & trade-off) là kịch bản vạn năng để trả lời mọi câu hỏi system design một cách tự tin, có hệ thống.
- Ước lượng không cần chính xác tuyệt đối - **đúng độ lớn** là đủ để định hình kiến trúc.

**Hết Tuần 1!** Bạn đã có bộ tư duy nền: scalability, latency vs throughput, availability, trade-off, con số tham chiếu và quy trình trả lời. Đây là “bộ đồ nghề” bạn sẽ dùng suốt khóa. **Tuần 2 - “Mạng và giao tiếp”** sẽ mở ra cách các thành phần thực sự “nói chuyện” với nhau. Bài đầu tiên - **“Client-Server và vòng đời một request”** - sẽ mổ xẻ điều gì xảy ra từ lúc bạn gõ một địa chỉ web đến khi trang hiện ra. Hẹn gặp lại ở Tuần 2! 🚀