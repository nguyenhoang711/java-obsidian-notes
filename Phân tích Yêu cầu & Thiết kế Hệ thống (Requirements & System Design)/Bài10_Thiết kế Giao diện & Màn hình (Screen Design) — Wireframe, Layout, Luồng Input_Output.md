Trong bài học này, chúng ta sẽ khám phá một trong những khía cạnh quan trọng nhất của thiết kế sản phẩm: **thiết kế giao diện và màn hình (Screen Design)**. Đây là bước chuyển từ ý tưởng trừu tượng sang một sản phẩm cụ thể, nơi người dùng có thể tương tác trực tiếp. Bạn sẽ học cách tạo wireframe, xây dựng layout, và xác định luồng dữ liệu vào/ra (Input/Output) mà người dùng trải nghiệm hàng ngày. Kỹ năng này không chỉ dành cho nhà thiết kế UI/UX mà còn cho bất kỳ ai tham gia vào quá trình phát triển sản phẩm, từ lập trình viên đến quản lý dự án.

Tại sao điều này quan trọng? Một giao diện được thiết kế tốt sẽ giúp người dùng hoàn thành nhiệm vụ một cách nhanh chóng và dễ dàng, giảm thiểu lỗi và tăng sự hài lòng. Ngược lại, một thiết kế kém có thể dẫn đến trải nghiệm tồi tệ, làm giảm tỷ lệ chuyển đổi hoặc thậm chí khiến người dùng rời bỏ sản phẩm. Theo một nghiên cứu của Forrester năm 2025, mỗi đô la đầu tư vào UX có thể mang lại lợi nhuận lên đến 100 đô la. Với sự phát triển của các công cụ như Figma, Sketch, và Adobe XD (tính đến năm 2026), việc thiết kế giao diện trở nên dễ dàng hơn bao giờ hết, nhưng vẫn đòi hỏi kiến thức nền tảng vững chắc.

Trong bài học này, bạn sẽ được hướng dẫn từng bước từ cơ bản đến nâng cao: bắt đầu với wireframe thô sơ, sau đó là thiết kế layout chi tiết, và cuối cùng là xác định luồng dữ liệu vào/ra. Chúng tôi sẽ sử dụng các ví dụ thực tế từ các ứng dụng phổ biến như Google Maps, Spotify, và một ứng dụng đặt đồ ăn giả định để minh họa. Hãy sẵn sàng để biến những ý tưởng trừu tượng thành những màn hình tương tác thực sự!
# Wireframe: Khung xương của giao diện
### Wireframe là gì?

Wireframe là bản phác thảo đơn giản, thường là đen trắng, thể hiện cấu trúc và bố cục của một màn hình. Nó tập trung vào **chức năng** hơn là **thẩm mỹ** — không có màu sắc, font chữ, hay hình ảnh chi tiết. Wireframe giống như bản vẽ kiến trúc của một ngôi nhà: nó cho thấy vị trí của các phòng, cửa ra vào, và cửa sổ, nhưng không quyết định màu sơn hay loại gạch lát nền.

Tại sao cần wireframe? Đây là bước đầu tiên để biến ý tưởng thành hiện thực. Nó giúp bạn:

- **Kiểm tra tính khả thi**: Liệu bố cục có hợp lý không? Các thành phần có đủ không gian không?
- **Giao tiếp ý tưởng**: Cho phép các bên liên quan (stakeholders, developers) hiểu được cấu trúc trước khi đầu tư thời gian vào thiết kế chi tiết.
- **Phát hiện lỗi sớm**: Một wireframe xấu có thể tiết lộ vấn đề về luồng người dùng trước khi code được viết.

### Các loại wireframe

Có ba mức độ chi tiết của wireframe:

1. **Low-fidelity wireframe**: Bản vẽ tay hoặc hình khối đơn giản, không có chi tiết. Thường được dùng trong brainstorming.
2. **Mid-fidelity wireframe**: Sử dụng các shape cơ bản (hình chữ nhật, hình tròn) và text placeholder. Đây là loại phổ biến nhất.
3. **High-fidelity wireframe**: Gần giống với thiết kế cuối cùng, có thể bao gồm màu sắc và font chữ, nhưng vẫn tập trung vào cấu trúc.

Dưới đây là một ví dụ wireframe low-fidelity cho màn hình đăng nhập:
```markdown
+----------------------------------+
|  [Logo]                          |
|                                  |
|  Tên đăng nhập: [______________] |
|  Mật khẩu:      [______________] |
|                                  |
|  [Đăng nhập]                     |
|                                  |
|  Quên mật khẩu?                  |
+----------------------------------+
```
### Ví dụ code: Tạo wireframe bằng HTML/CSS

Bạn có thể tạo wireframe nhanh bằng HTML/CSS để kiểm tra layout. Dưới đây là một ví dụ đơn giản:
```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wireframe Đăng nhập</title>
    <style>
        /* CSS cho wireframe - chỉ dùng border và layout */
        .container {
            width: 300px;
            margin: 50px auto;
            border: 2px solid #333;
            padding: 20px;
            font-family: Arial, sans-serif;
        }
        .logo {
            text-align: center;
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 20px;
            border: 1px dashed #999;
            padding: 10px;
        }
        .input-group {
            margin-bottom: 15px;
        }
        .input-group label {
            display: block;
            margin-bottom: 5px;
        }
        .input-group input {
            width: 100%;
            padding: 8px;
            border: 1px solid #333;
            box-sizing: border-box;
        }
        .btn {
            width: 100%;
            padding: 10px;
            background: #eee;
            border: 1px solid #333;
            cursor: pointer;
            text-align: center;
        }
        .link {
            text-align: center;
            margin-top: 10px;
            color: #333;
            text-decoration: underline;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="logo">Logo</div>
        <div class="input-group">
            <label for="username">Tên đăng nhập</label>
            <input type="text" id="username" placeholder="Nhập tên đăng nhập">
        </div>
        <div class="input-group">
            <label for="password">Mật khẩu</label>
            <input type="password" id="password" placeholder="Nhập mật khẩu">
        </div>
        <div class="btn">Đăng nhập</div>
        <div class="link">Quên mật khẩu?</div>
    </div>
</body>
</html>
```
**Mẹo:** Khi tạo wireframe, hãy sử dụng placeholder text như “Lorem ipsum” hoặc “Nội dung ở đây” để tránh bị phân tâm bởi nội dung thực tế.
# Layout: Bố cục và tổ chức không gian
### Nguyên tắc cơ bản của layout

Layout là cách bạn sắp xếp các thành phần trên màn hình để tạo ra một trải nghiệm trực quan và chức năng. Một layout tốt phải tuân theo các nguyên tắc sau:

- **Hierarchy (Thứ bậc)**: Các yếu tố quan trọng nhất (như tiêu đề, nút hành động chính) phải nổi bật nhất. Sử dụng kích thước, màu sắc, hoặc vị trí để tạo hierarchy.
- **Alignment (Căn chỉnh)**: Mọi thứ nên được căn chỉnh theo một lưới (grid) để tạo sự gọn gàng. Sử dụng hệ thống lưới 8px hoặc 4px là phổ biến.
- **White space (Khoảng trắng)**: Đừng sợ khoảng trống! Nó giúp mắt người dùng nghỉ ngơi và tập trung vào nội dung chính.
- **Consistency (Nhất quán)**: Các thành phần tương tự (nút, input, icon) nên có cùng kích thước và kiểu dáng trên toàn bộ ứng dụng.

### Các loại layout phổ biến

Dưới đây là một số layout thường gặp trong thiết kế màn hình:

|Loại layout|Mô tả|Ví dụ|
|---|---|---|
|**Single column**|Một cột duy nhất, dễ đọc trên di động|Trang chủ Instagram|
|**Two column**|Hai cột, thường dùng cho dashboard|Gmail (danh sách email + nội dung)|
|**Grid**|Lưới các ô đều nhau, thích hợp cho gallery|Pinterest|
|**F-shaped**|Người dùng đọc theo hình chữ F, phù hợp cho nội dung văn bản|Blog, bài báo|
|**Z-shaped**|Người dùng quét theo hình chữ Z, phù hợp cho landing page|Trang giới thiệu sản phẩm|

### Ví dụ code: Layout hai cột với CSS Grid

CSS Grid là công cụ mạnh mẽ để tạo layout linh hoạt. Dưới đây là một ví dụ layout hai cột cho dashboard:
```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Layout</title>
    <style>
        /* Sử dụng CSS Grid để tạo layout hai cột */
        .dashboard {
            display: grid;
            grid-template-columns: 250px 1fr; /* Cột trái cố định 250px, cột phải chiếm phần còn lại */
            gap: 20px;
            height: 100vh;
            padding: 20px;
            background: #f5f5f5;
        }
        .sidebar {
            background: #fff;
            border: 1px solid #ddd;
            padding: 20px;
            border-radius: 8px;
        }
        .sidebar h2 {
            margin-top: 0;
        }
        .sidebar ul {
            list-style: none;
            padding: 0;
        }
        .sidebar li {
            padding: 10px;
            margin-bottom: 5px;
            background: #f0f0f0;
            border-radius: 4px;
            cursor: pointer;
        }
        .sidebar li:hover {
            background: #e0e0e0;
        }
        .main-content {
            background: #fff;
            border: 1px solid #ddd;
            padding: 20px;
            border-radius: 8px;
        }
        .main-content h1 {
            margin-top: 0;
        }
        .card {
            background: #f9f9f9;
            border: 1px solid #eee;
            padding: 15px;
            margin-bottom: 15px;
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <div class="dashboard">
        <!-- Sidebar: menu điều hướng -->
        <div class="sidebar">
            <h2>Menu</h2>
            <ul>
                <li>Tổng quan</li>
                <li>Người dùng</li>
                <li>Báo cáo</li>
                <li>Cài đặt</li>
            </ul>
        </div>
        <!-- Main content: nội dung chính -->
        <div class="main-content">
            <h1>Bảng điều khiển</h1>
            <div class="card">
                <h3>Thống kê ngày</h3>
                <p>Số người dùng mới: 150</p>
                <p>Doanh thu: 10.000.000 VND</p>
            </div>
            <div class="card">
                <h3>Hoạt động gần đây</h3>
                <p>Nguyễn Văn A đã đăng nhập lúc 10:30</p>
                <p>Trần Thị B đã đặt hàng #1234</p>
            </div>
        </div>
    </div>
</body>
</html>
```
**Mẹo:** Khi thiết kế layout, hãy luôn xem xét responsive design. Sử dụng media queries để điều chỉnh layout cho các kích thước màn hình khác nhau.
# Luồng Input/Output: Tương tác của người dùng
### Input là gì? Output là gì?

Trong thiết kế giao diện, **Input** là bất kỳ hành động nào người dùng thực hiện để tương tác với hệ thống, bao gồm:

- Nhập văn bản (text input)
- Nhấp chuột hoặc chạm (click/tap)
- Kéo thả (drag and drop)
- Cuộn (scroll)
- Giọng nói (voice input) — ngày càng phổ biến với sự phát triển của AI

**Output** là phản hồi từ hệ thống đến người dùng, bao gồm:

- Hiển thị dữ liệu (data display)
- Thông báo (notifications)
- Hiệu ứng chuyển động (animations)
- Âm thanh (sound)
- Phản hồi xúc giác (haptic feedback) trên thiết bị di động

### Xác định luồng Input/Output

Khi thiết kế một màn hình, bạn cần xác định rõ:

1. **Người dùng sẽ nhập gì?** (Ví dụ: tên đăng nhập, mật khẩu)
2. **Hệ thống sẽ xuất gì?** (Ví dụ: thông báo lỗi, chuyển hướng đến trang chủ)
3. **Luồng tương tác diễn ra như thế nào?** (Ví dụ: nhấn nút -> kiểm tra dữ liệu -> hiển thị kết quả)

Dưới đây là một sơ đồ ASCII mô tả luồng Input/Output cho màn hình đăng nhập:
```markdown
[Input]                    [Xử lý]                    [Output]
   |                          |                          |
   v                          v                          v
Nhập tên đăng nhập  ->   Kiểm tra dữ liệu       ->   Hiển thị lỗi (nếu sai)
Nhập mật khẩu        ->   Xác thực với server    ->   Chuyển hướng (nếu đúng)
Nhấn nút Đăng nhập   ->   Gửi request API       ->   Hiển thị loading
```
### Ví dụ code: Xử lý Input/Output với JavaScript

Dưới đây là một ví dụ hoàn chỉnh về xử lý đăng nhập, bao gồm validation và phản hồi:
```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Xử lý Đăng nhập</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background: #f0f0f0;
        }
        .login-form {
            background: #fff;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            width: 300px;
        }
        .input-group {
            margin-bottom: 15px;
        }
        .input-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        .input-group input {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        .input-group input.error {
            border-color: #ff0000;
        }
        .btn {
            width: 100%;
            padding: 10px;
            background: #007bff;
            color: #fff;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        .btn:hover {
            background: #0056b3;
        }
        #message {
            margin-top: 15px;
            padding: 10px;
            border-radius: 4px;
            display: none;
        }
        #message.success {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
            display: block;
        }
        #message.error {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
            display: block;
        }
        .loading {
            text-align: center;
            margin-top: 15px;
            display: none;
        }
    </style>
</head>
<body>
    <div class="login-form">
        <h2>Đăng nhập</h2>
        <!-- Input: form đăng nhập -->
        <form id="loginForm">
            <div class="input-group">
                <label for="username">Tên đăng nhập</label>
                <input type="text" id="username" placeholder="Nhập tên đăng nhập" required>
            </div>
            <div class="input-group">
                <label for="password">Mật khẩu</label>
                <input type="password" id="password" placeholder="Nhập mật khẩu" required>
            </div>
            <button type="submit" class="btn">Đăng nhập</button>
        </form>
        <!-- Output: thông báo kết quả -->
        <div id="message"></div>
        <div class="loading" id="loading">Đang xử lý...</div>
    </div>

    <script>
        // Lấy các phần tử DOM
        const form = document.getElementById('loginForm');
        const usernameInput = document.getElementById('username');
        const passwordInput = document.getElementById('password');
        const messageDiv = document.getElementById('message');
        const loadingDiv = document.getElementById('loading');

        // Xử lý sự kiện submit form
        form.addEventListener('submit', function(event) {
            event.preventDefault(); // Ngăn chặn reload trang

            // Input: lấy dữ liệu từ người dùng
            const username = usernameInput.value.trim();
            const password = passwordInput.value.trim();

            // Validation: kiểm tra dữ liệu đầu vào
            let hasError = false;
            if (username === '') {
                usernameInput.classList.add('error');
                hasError = true;
            } else {
                usernameInput.classList.remove('error');
            }
            if (password === '') {
                passwordInput.classList.add('error');
                hasError = true;
            } else {
                passwordInput.classList.remove('error');
            }

            if (hasError) {
                // Output: hiển thị lỗi
                messageDiv.className = 'error';
                messageDiv.textContent = 'Vui lòng điền đầy đủ thông tin!';
                return;
            }

            // Output: hiển thị loading
            loadingDiv.style.display = 'block';
            messageDiv.style.display = 'none';

            // Giả lập xử lý API (setTimeout thay cho request thực tế)
            setTimeout(function() {
                loadingDiv.style.display = 'none';
                
                // Giả lập kiểm tra đăng nhập
                if (username === 'admin' && password === '123456') {
                    // Output: thành công
                    messageDiv.className = 'success';
                    messageDiv.textContent = 'Đăng nhập thành công! Chào mừng bạn!';
                    // Trong thực tế, bạn sẽ chuyển hướng đến trang chủ
                    // window.location.href = '/dashboard';
                } else {
                    // Output: thất bại
                    messageDiv.className = 'error';
                    messageDiv.textContent = 'Tên đăng nhập hoặc mật khẩu không đúng!';
                }
            }, 2000); // Giả lập thời gian xử lý 2 giây
        });
    </script>
</body>
</html>
```
**Cảnh báo:** Trong ví dụ trên, chúng ta sử dụng dữ liệu giả lập. Trong thực tế, việc xác thực phải được thực hiện trên server, và bạn không bao giờ được lưu mật khẩu dưới dạng plain text. Luôn sử dụng HTTPS và các biện pháp bảo mật như hashing (bcrypt) và CSRF tokens.
# Thiết kế cho các trạng thái khác nhau
### Các trạng thái của một màn hình

Một màn hình không chỉ tồn tại ở một trạng thái duy nhất. Khi thiết kế, bạn cần xem xét các trạng thái sau:

1. **Default state (Trạng thái mặc định)**: Màn hình hiển thị khi không có tương tác.
2. **Loading state (Trạng thái tải)**: Hiển thị khi dữ liệu đang được lấy từ server.
3. **Empty state (Trạng thái rỗng)**: Hiển thị khi không có dữ liệu (ví dụ: giỏ hàng trống).
4. **Error state (Trạng thái lỗi)**: Hiển thị khi có lỗi xảy ra (ví dụ: mất kết nối).
5. **Success state (Trạng thái thành công)**: Hiển thị khi hành động hoàn tất thành công.
6. **Edge case state (Trạng thái ngoại lệ)**: Các trường hợp đặc biệt (ví dụ: quá nhiều dữ liệu, ký tự đặc biệt).

### Ví dụ: Thiết kế trạng thái rỗng cho danh sách

Dưới đây là một ví dụ về thiết kế trạng thái rỗng cho danh sách sản phẩm:
```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Trạng thái rỗng</title>
    <style>
        .empty-state {
            text-align: center;
            padding: 50px;
            background: #fff;
            border: 1px solid #ddd;
            border-radius: 8px;
            max-width: 400px;
            margin: 50px auto;
        }
        .empty-state .icon {
            font-size: 64px;
            color: #ccc;
            margin-bottom: 20px;
        }
        .empty-state h2 {
            color: #333;
            margin-bottom: 10px;
        }
        .empty-state p {
            color: #666;
            margin-bottom: 20px;
        }
        .empty-state .btn {
            padding: 10px 20px;
            background: #007bff;
            color: #fff;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="empty-state">
        <div class="icon">📦</div>
        <h2>Chưa có sản phẩm nào</h2>
        <p>Danh sách sản phẩm của bạn đang trống. Hãy thêm sản phẩm đầu tiên!</p>
        <button class="btn">Thêm sản phẩm</button>
    </div>
</body>
</html>
```
# Các lỗi thường gặp
Dưới đây là những lỗi phổ biến khi thiết kế giao diện và cách khắc phục:

1. **Bỏ qua trạng thái loading và error**: Nhiều nhà thiết kế chỉ tập trung vào trạng thái mặc định, dẫn đến trải nghiệm tồi tệ khi dữ liệu chậm hoặc lỗi xảy ra.
    
    - **Giải pháp**: Luôn thiết kế ít nhất ba trạng thái: default, loading, và error. Sử dụng skeleton screens thay vì spinner để cải thiện cảm nhận tốc độ.
2. **Quá tải thông tin trên một màn hình**: Cố gắng nhồi nhét quá nhiều chức năng vào một màn hình làm người dùng bối rối.
    
    - **Giải pháp**: Áp dụng nguyên tắc “one task per screen” — mỗi màn hình chỉ nên có một mục tiêu chính. Sử dụng progressive disclosure để hiển thị thông tin dần dần.
3. **Thiếu feedback cho hành động người dùng**: Khi người dùng nhấn nút mà không có phản hồi (không loading, không thông báo), họ sẽ không biết hệ thống đang hoạt động.
    
    - **Giải pháp**: Luôn cung cấp phản hồi ngay lập tức, dù chỉ là hiệu ứng nhấn (button press) hoặc thay đổi màu sắc.
4. **Không tối ưu cho touch target**: Trên thiết bị di động, các nút quá nhỏ hoặc quá gần nhau gây khó khăn khi chạm.
    
    - **Giải pháp**: Đảm bảo kích thước tối thiểu 44x44px cho touch target (theo khuyến nghị của Apple và Google).
5. **Bỏ qua accessibility**: Thiết kế không tính đến người dùng khiếm thị, khiếm thính, hoặc các hạn chế khác.
    
    - **Giải pháp**: Sử dụng contrast ratio tối thiểu 4.5:1 cho text, cung cấp alt text cho hình ảnh, và hỗ trợ keyboard navigation.
# Best practices
1. **Bắt đầu bằng wireframe, kết thúc bằng prototype**: Luôn bắt đầu với wireframe low-fidelity để kiểm tra ý tưởng, sau đó dần dần tăng độ chi tiết. Sử dụng Figma hoặc Sketch để tạo prototype có thể tương tác.
    
2. **Sử dụng hệ thống lưới (grid system)**: Áp dụng lưới 8px hoặc 4px để đảm bảo mọi thứ được căn chỉnh chính xác. Điều này giúp giao diện trông chuyên nghiệp và nhất quán.
    
3. **Thiết kế cho nhiều kích thước màn hình**: Sử dụng responsive design với breakpoints phổ biến: 320px (mobile nhỏ), 768px (tablet), 1024px (desktop nhỏ), 1440px (desktop lớn).
    
4. **Sử dụng design system**: Xây dựng hoặc sử dụng một design system có sẵn (như Material Design 3 hoặc Ant Design) để đảm bảo consistency và tăng tốc độ phát triển.
    
5. **Kiểm tra với người dùng thực tế**: Đừng chỉ dựa vào ý kiến của team. Thực hiện usability testing với 5-10 người dùng để phát hiện vấn đề sớm.
    
6. **Tối ưu hóa performance**: Thiết kế giao diện nhẹ, tránh sử dụng quá nhiều hình ảnh lớn hoặc animation phức tạp. Sử dụng lazy loading cho nội dung dài.
    
7. **Luôn cập nhật xu hướng**: Năm 2026, các xu hướng nổi bật bao gồm: glassmorphism, micro-interactions, và AI-driven UI (giao diện tự động điều chỉnh dựa trên hành vi người dùng).
# Tài liệu tham khảo
- [Figma Official Documentation](https://help.figma.com/hc/en-us) - Tài liệu chính thức về Figma, công cụ thiết kế phổ biến nhất năm 2026.
- [Material Design 3 Guidelines](https://m3.material.io/) - Hệ thống thiết kế của Google với các nguyên tắc chi tiết về layout, color, và typography.
- [Stack Overflow: UI/UX Design](https://stackoverflow.com/questions/tagged/ui-design) - Cộng đồng hỏi đáp về thiết kế giao diện, nơi bạn có thể tìm giải pháp cho các vấn đề cụ thể.
- [Nielsen Norman Group: UX Articles](https://www.nngroup.com/articles/) - Các bài viết chuyên sâu về UX research và usability từ nhóm nghiên cứu hàng đầu thế giới.
# Tổng kết
Trong bài học này, bạn đã học được:

- **Wireframe** là bước đầu tiên để phác thảo cấu trúc màn hình, với ba mức độ chi tiết: low, mid, và high-fidelity.
- **Layout** là cách sắp xếp các thành phần trên màn hình, tuân theo các nguyên tắc hierarchy, alignment, white space, và consistency.
- **Luồng Input/Output** xác định cách người dùng tương tác với hệ thống và cách hệ thống phản hồi lại.
- **Các trạng thái màn hình** bao gồm default, loading, empty, error, và success — tất cả đều cần được thiết kế cẩn thận.
- **Các lỗi thường gặp** như bỏ qua trạng thái, quá tải thông tin, thiếu feedback, và không tối ưu touch target có thể tránh được bằng cách tuân theo best practices.
- **Best practices** như sử dụng grid system, responsive design, design system, và kiểm tra với người dùng thực tế sẽ giúp bạn tạo ra những giao diện chất lượng cao.

Hãy nhớ rằng, thiết kế giao diện không chỉ là về cái đẹp — nó là về sự hữu dụng và trải nghiệm người dùng. Một giao diện tốt là giao diện mà người dùng không cần phải suy nghĩ để sử dụng. Hãy thực hành bằng cách thiết kế lại một ứng dụng bạn yêu thích, bắt đầu từ wireframe và kết thúc bằng prototype có thể tương tác. Chúc bạn thành công!
