1. Vấn đề tồn đọc cái model AI hiện tại
- cutoff date: ngày cuối cùng mà 1 model được training dữ liệu
![[Pasted image 20260209093939.png]]
VD: tháng 10 - 2023 là ngày cuối cùng mà o1-mini được training data --> nó sẽ không biết về các sự kiện xảy ra sau tháng 10-2023
- không thể truy cập URL và các web page trực tiếp: vd tóm tắt nội dung bài báo, video Youtube
- không thể truy cập file lưu trữ máy, lưu trữ cloud
- CRM (Customer Relationship Management), Notion: không thể truy cập trực tiếp qua các model
2. Giải pháp hiện có
- Vấn đề cutoff date: kết nối model với mô hình ngôn ngữ lớn
- Không truy cập URL và web page: nhiều công cụ hỗ trợ, chỉ cần kết nối model với cộng cụ
- Truy cập file trực tiếp máy: tự code 1 mini project và kết nối với model
- Truy cập CSDL, API doanh nghiệp: cung cấp config kết nối
# 1. Model Context Protocol là gì ? (MCP)
Giao thức ngữ cảnh dành cho mô hình AI
So sánh với web, muốn duyệt web nằm trên máy chủ thì cần dùng qua tool là browser và giao tiếp qua giao thức http
Tương tự: AI agent cần công cụ (search internet, đọc file, lấy dữ liệu từ DB) để nạp thêm thông tin vào context --> cần tools và gọi qua giao thức MCP
Đơn giản là MCP server là nơi chứa công cụ, AI muốn kết nối tools nào thì nối trên MCP server mà dùng
![[Pasted image 20260504143316.png]]
# 2. Kiến trúc của MCP
![[Pasted image 20260209104510.png]]
- MCP Host (Claude, IDEs, tools): là mô hình ngôn ngữ lớn, hoặc IDEs, muốn truy cập, kết nối tới tools bên ngoài
- MCP Server A, B, C: là các tools bên ngoài muốn kết nối tới MCP host, phơi bày API đúng chuẩn MCP, để có thể lấy được tài liệu, công cụ ở bên ngoài
- MCP client: duy trì kết nối 1-1 với MCP host với từng MCP server ()
![[Pasted image 20260209105450.png]]
	- Giống như đoạn script để kết nối tới ứng dụng bên ngoài
- Local Resource: là các CSDL, các dịch vụ mà ta muốn MCP host truy cập và sử dụng thông qua MCP server. (giống như ta lưu trữ dữ  liệu vào ổ cứng rời, hoặc ở máy tính khác)
- Remote Resource: tài nguyên mà chúng ta có thể truy cập qua internet như slack, gmail, calendar.
Tóm lại: khi ta muốn model trong máy chúng ta kết nối tới các dịch vụ bên ngoài thì cần tạo ra kết nối MCP server tới các dịch vụ này. Ngược lại các công cụ muốn cho các model sử dụng thì phải tạo MCP server riêng cho công cụ ta muốn phát triển.
# 3. Các loại kết nối chính của MCP
- stdio: dành cho MCP server trên máy người dùng, các ứng dụng AI chat dùng transport này. Cài đặt MCP server cần kéo nó về máy rồi AI chat mới nói chuyện với nó đc
- **Streamable  HTTP:** kết nối MCP server chạy trên server, tức khi tạo MCP và muốn deploy lên server thì các công cụ AI chat connect tới MCP server bằng "Streamable HTTP"
# 4. Có cần thiết của MCP ?
MCP không khác gì **API wrapper**
# 5. Cơ hội cho DN sử dụng các micro SaaS

# 6. Tiềm năng phát triển
Cấu hình MCP bằng JSON và environment variables, bearer authorization --> khó cho người phổ thông
# 7. Chợ MCP
Giống với ThemeForest đang làm với N8N market bán workflow
MCP registry
