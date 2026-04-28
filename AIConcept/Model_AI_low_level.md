# Token là gì?
## Khái niệm
Là đơn vị nhỏ nhất mà một mô hình (model) AI xử lý
Khác nhau trên từng loại model
## Mục đích
- **Tính chi phí**
Chi phí sử dụng dựa trên token:
+ Số token input
+ Số token trả ra (kết quả)
- Tính giới hạn bộ nhớ
AI agent không có "trí nhớ vô hạn": chỉ xử lý được trong **context window (giới hạn token)**
- Xác định hiệu năng
Token càng nhiều, xử lý càng chậm
Agent càng tốn tài nguyên
## Vì sao chia khác nhau trên từng model?
Các yếu tố tác động tới cách phân bố token
- Kiến trúc model
- Dữ liệu train
- Ngôn ngữ tối ưu
Đối tượng model nhắm đến cũng ảnh hưởng tới các allocate token
- Model tối ưu tiếng anh (token ít với english, nhiều với tiếng việt)
- Model đa ngôn ngữ (cân bằng)
- Model cho lập trình (tối ưu cho dấu {} và syntax)
## Cách tối ưu Token sử dụng?
- Không viết câu lịch sự
- Intent (Ý chính) và Context (nếu cần)
Các phần có thể bỏ:

- “tôi muốn…”
- “bạn hãy…”
- “giúp tôi…”
Bạn có thể dùng format:

> **[Chủ đề] + [Yêu cầu cụ thể] + (context nếu cần)**

# Context Window
## Khái niệm
Số lượng token tối đa mà AI có thể "nhìn thấy" trong 1 lần xử lý
Gồm:
- Input (prompt bạn gửi)
- Output (Response)

Hiểu đơn giản context window là trí nhớ ngắn hạn của AI
- AI không nhớ toàn bộ cuộc hội thoại
- Chỉ nhìn được phần nằm trong context
## Thành phần
[System prompt]
+ [Tool definitions]
+ [Chat history]
+ [User input]
+ [Retrieved data (RAG)]
## Các chiến lược xử lý context
### Trimming
Xóa message cũ
### Summarization
Tóm tắt history thành đoạn ngắn
### RAG (best practice)
Không nhét tất cả data
Chỉ lấy data liên quan
### Sliding window
Giữ N message gần nhất
### Memory design (Nâng cao)
Short-term memory --> trong context
Long-term memory --> database
