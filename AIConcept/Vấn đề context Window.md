Các model hiện tại đã đủ mạnh, tuy nhiên khi hỏi đôi khi kết quả vẫn ra sai --> bottleneck thật sự là CONTEXT

![[Pasted image 20260509161716.png]]
Context: ngay từ đầu đã load từng này token --> context càng đầy
![[Pasted image 20260509161928.png]]
Lãng phí ở CLAUDE.md quá dài --> lãng phí
Nhìn nhận AI dưới góc độ bạn tuyển 1 nhân sự senior vào dự án --> không cần đưa ra những chỉ dẫn thừa thãi như trên hình

# Chỉ 5% thông tin đưa vào CLAUDE.MD là cần thiết
3 thứ đáng dữ lại:
- Quy trình đặc thù của công ty bạn
- Thuật ngữ nội bộ - Định nghĩa riêng cho công ty bạn
- Phong cách giao tiếp cụ thể muốn AI giữ nhất quán

# Cách skills hoạt động - khác CLAUDE.md
## 2.1 Cách skills hoạt động

| CLAUDE.md                     | skills                                                                            |
| ----------------------------- | --------------------------------------------------------------------------------- |
| Chạy mỗi lần mở hội thoại     | Mỗi lần mở hội thoại                                                              |
| Load TOÀN BỘ nội dung         | Chỉ load tên + Mô tả                                                              |
| Dù cần hay không cần vẫn LOAD | Khi bạn gọi tên skill<br><br>Mới load toàn bộ nội dung --> Progressive Disclosure |
| 10000 token                   | 50 token                                                                          |
## 2.2 Skills = SOP cho AI Agent
![[Pasted image 20260509163916.png]]
Dùng skills giống như đóng gói quy trình chuẩn thao tác (SOP) cho AI làm
Có từng SOP riêng cho từng loại công việc:
- SOP riêng cho khiếu nại
- SOP Onboarding
- SOP Analystics
# 3. Viết skills
## 3.1 Sai lầm khi tạo skills
![[Pasted image 20260509164158.png]]
Ra lệnh cho AI viết skills theo workflow này ... --> không hiệu quả
Vì AI chưa từng chạy workflow này bao giờ, chưa biết các bước cụ thể, các vấn đề có thể xảy ra với từng bước
## 3.2 Cách làm đúng
![[Pasted image 20260509164509.png]]
VD: đưa Agent đánh giá Brand Email --> cho vào skills đánh giá
Kết quả: Luôn nhận về đánh giá TỐT.

Nguyên nhân:
- Do AI chưa có tiêu chí đánh giá nên 
- chỉ làm theo nghĩa đen của câu lệnh

CÁCH LÀM ĐÚNG
![[Pasted image 20260509164721.png]]
- Tạo ra một quy trình thực sự (rõ các edge cases, criteria)
- Tiêu chí đánh giá 
- AI gửi đề xuất --> sửa lại
- AI điều chỉnh
Lặp đi lặp lại đến khi ra quy trình thật.
![[Pasted image 20260509164957.png]]
Khi đó sẽ ra lệnh cho CLAUDE code: "Hãy đóng gói tất cả những gì ta vừa làm vào 1 skills, được viết từ context thật
## 3.3 Skill FAIL = Data QUÝ để cải thiện
Sẽ có trường hợp SKILLS gặp vào unknown case, mình chưa cover được --> FAIL
Trong workflow thực tế, sẽ có những case như vậy
1. Skill chạy trong workflow thật
2. Fail --> lỗi xảy ra
3. Hỏi AI "Tại sao lại fail ở bước này"
4. AI giải thích --> bạn fix skills
5. Cập nhật skills --> chạy lại --> lặp tiếp
6. Lặp 3-> 5 vòng skills gần như không bao giờ lỗi
## 3.4 Đừng dùng skill người khác ngay -> tại sao
- Thiếu bước đặc thù trong workflow của bạn
- Thiếu error handling cho hệ thống, xử lý edge cases
- Không hiểu rõ hoạt độg như nào
- CHạy tốt với người ta, chưa chắc tốt với bạn

# 4. Sai lầm khi dùng nhiều Agents
Hãy bắt đầu với 1 Agent duy nhất: làm việc với tác vụ nào lặp lại nhiều nhất trong ngày của bạn
*Khi đã ổn định và hiểu rõ giới hạn*
Thực sự cần thiết --> Tách sub-agent cho nhóm công việc riêng
Mở rộng khi thực sự cần

![[Pasted image 20260509170858.png]]



