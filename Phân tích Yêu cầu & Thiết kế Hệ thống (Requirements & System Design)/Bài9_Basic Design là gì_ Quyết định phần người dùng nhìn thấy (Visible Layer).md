Trong thế giới phát triển phần mềm, chúng ta thường nghe đến thuật ngữ “thiết kế” (design). Nhưng “thiết kế” ở đây không chỉ đơn thuần là vẽ giao diện hay chọn màu sắc. Trong bối cảnh kiến trúc phần mềm, **Basic Design** – hay còn gọi là thiết kế cơ bản – đóng vai trò quyết định trong việc **định nghĩa “What”** (cái gì) mà người dùng sẽ nhìn thấy và tương tác. Đây là bước chuyển tiếp từ ý tưởng trừu tượng sang một sản phẩm cụ thể, nơi các yêu cầu nghiệp vụ được dịch thành các thành phần hiển thị trên màn hình.

Tại sao Basic Design lại quan trọng? Hãy tưởng tượng bạn đang xây một ngôi nhà. Nếu không có bản thiết kế chi tiết về cửa sổ, cửa ra vào, và cách bố trí phòng, bạn sẽ không biết bắt đầu từ đâu. Tương tự, trong phát triển phần mềm, Basic Design là bản vẽ chi tiết cho **Visible Layer** – lớp mà người dùng cuối nhìn thấy và tương tác trực tiếp. Nó bao gồm giao diện người dùng (UI), cách bố trí thông tin, và các thành phần tương tác cơ bản. Khi bạn nắm vững Basic Design, bạn có thể đảm bảo rằng sản phẩm cuối cùng không chỉ đẹp mắt mà còn đáp ứng đúng nhu cầu của người dùng.

Trong bài viết này, chúng ta sẽ khám phá bản chất của Basic Design, phạm vi của Visible Layer, và cách áp dụng nó vào thực tế. Bạn sẽ học cách phân biệt giữa “thiết kế để hiển thị” và “thiết kế để hoạt động”, cũng như cách tránh những sai lầm phổ biến khi làm việc với lớp hiển thị. Hãy sẵn sàng để hiểu rõ hơn về cách mà những quyết định thiết kế nhỏ nhất cũng có thể tác động lớn đến trải nghiệm người dùng.
# Basic Design là gì? Bản chất của việc định nghĩa "What"
### Định nghĩa Basic Design trong kiến trúc phần mềm

Basic Design, trong ngữ cảnh kiến trúc phần mềm, là quá trình **định nghĩa các thành phần hiển thị** mà người dùng sẽ tương tác. Nó khác với High-Level Design (thiết kế tổng thể) ở chỗ High-Level Design tập trung vào cấu trúc hệ thống, trong khi Basic Design tập trung vào lớp hiển thị (Visible Layer). Nói cách khác, Basic Design trả lời câu hỏi: **“Người dùng sẽ nhìn thấy và tương tác với cái gì?”**

Bản chất của Basic Design là **định nghĩa “What”** – xác định các thành phần cụ thể như button, form, bảng dữ liệu, và cách chúng được bố trí. Đây là bước chuyển từ yêu cầu nghiệp vụ (Business Requirements) sang các thành phần hiển thị (UI Components). Ví dụ, nếu yêu cầu là “người dùng có thể đăng nhập”, Basic Design sẽ định nghĩa một form đăng nhập với các trường email, mật khẩu, và nút submit.

### Tại sao gọi là “thiết kế” nhưng bản chất là định nghĩa?

Nhiều người nhầm lẫn rằng “thiết kế” chỉ là vẽ giao diện đẹp. Nhưng thực tế, Basic Design là một quá trình **định nghĩa có cấu trúc**. Nó không chỉ là chọn màu sắc hay font chữ, mà là xác định:

- **Các thành phần nào sẽ xuất hiện?** Ví dụ: button, input field, dropdown.
- **Chúng được bố trí như thế nào?** Ví dụ: form đăng nhập nằm ở giữa màn hình.
- **Chúng tương tác với nhau ra sao?** Ví dụ: khi click nút submit, dữ liệu được gửi lên server.

Điều này tương tự như việc kiến trúc sư định nghĩa các phòng trong một ngôi nhà. Họ không chỉ vẽ mà còn xác định chức năng của từng phòng. Tương tự, Basic Design định nghĩa chức năng của từng thành phần hiển thị.

> **Tip:** Khi làm Basic Design, hãy luôn tự hỏi: “Thành phần này giải quyết vấn đề gì cho người dùng?” Điều này giúp bạn tránh thiết kế những thứ không cần thiết.
# Phạm vi của Visible Layer: Người dùng nhìn thấy gì?
### Visible Layer là gì?

Visible Layer là **lớp hiển thị** – phần mà người dùng cuối nhìn thấy và tương tác trực tiếp. Nó bao gồm tất cả các thành phần giao diện như button, text, image, form, và các animation cơ bản. Visible Layer không bao gồm logic backend, database, hay các API – những thứ nằm “phía sau” màn hình.

Phạm vi của Visible Layer trong Basic Design bao gồm:

- **Giao diện người dùng (UI)**: Các thành phần hiển thị như header, footer, sidebar, và content area.
- **Tương tác cơ bản**: Các hành vi như click, hover, scroll, và submit form.
- **Phản hồi trực quan**: Các hiệu ứng như loading spinner, error message, và success notification.

Ví dụ, khi bạn mở một trang web mua sắm, Visible Layer là những gì bạn thấy: danh sách sản phẩm, nút “Thêm vào giỏ”, và form thanh toán. Những thứ như cơ sở dữ liệu sản phẩm hay logic xử lý thanh toán nằm ngoài Visible Layer.

### Các thành phần chính của Visible Layer

Để hiểu rõ hơn, hãy xem xét các thành phần chính của Visible Layer thông qua một ví dụ cụ thể: một form đăng ký người dùng.
```js
// Ví dụ: Định nghĩa các thành phần Visible Layer cho form đăng ký
// Các thành phần này sẽ được render trong HTML

const registrationForm = {
  // 1. Các trường input
  fields: [
    { type: 'text', name: 'username', label: 'Tên đăng nhập', placeholder: 'Nhập tên...' },
    { type: 'email', name: 'email', label: 'Email', placeholder: 'example@email.com' },
    { type: 'password', name: 'password', label: 'Mật khẩu', placeholder: 'Ít nhất 8 ký tự' }
  ],
  // 2. Nút submit
  submitButton: { text: 'Đăng ký', type: 'submit' },
  // 3. Thông báo lỗi
  errorMessages: {
    required: 'Vui lòng điền đầy đủ thông tin',
    invalidEmail: 'Email không hợp lệ',
    weakPassword: 'Mật khẩu phải có ít nhất 8 ký tự'
  }
};
```
> **Note:** Trong ví dụ trên, chúng ta chỉ định nghĩa cấu trúc hiển thị. Logic xử lý (ví dụ: kiểm tra email hợp lệ) sẽ được thực hiện ở lớp khác.

### Phân biệt Visible Layer với các lớp khác

Để tránh nhầm lẫn, hãy so sánh Visible Layer với các lớp khác trong kiến trúc phần mềm:

|Lớp (Layer)|Chức năng|Ví dụ|
|---|---|---|
|**Visible Layer**|Hiển thị và tương tác|Button, form, text|
|**Business Logic Layer**|Xử lý nghiệp vụ|Kiểm tra dữ liệu, tính toán|
|**Data Layer**|Lưu trữ và truy xuất dữ liệu|Database, API|

> **Warning:** Một sai lầm phổ biến là đưa logic nghiệp vụ vào Visible Layer. Ví dụ, kiểm tra mật khẩu trong code JavaScript hiển thị. Điều này vi phạm nguyên tắc Separation of Concerns (SoC).
# Cách xây dựng Basic Design cho Visible Layer
### Bước 1: Xác định yêu cầu nghiệp vụ

Trước khi thiết kế, bạn cần hiểu rõ yêu cầu từ người dùng. Ví dụ, nếu bạn đang xây dựng một ứng dụng quản lý công việc, yêu cầu có thể là:

- Người dùng có thể thêm công việc mới.
- Người dùng có thể đánh dấu công việc đã hoàn thành.
- Người dùng có thể xem danh sách công việc.

Từ đó, Basic Design sẽ định nghĩa các thành phần hiển thị tương ứng.

### Bước 2: Thiết kế các thành phần hiển thị

Dựa trên yêu cầu, bạn xác định các thành phần cụ thể. Ví dụ:
```js
// Thiết kế các thành phần cho ứng dụng quản lý công việc
const taskManagerUI = {
  // Header: tiêu đề và nút thêm công việc
  header: {
    title: 'Quản lý công việc',
    addButton: { text: '+ Thêm công việc', action: 'openAddForm' }
  },
  // Danh sách công việc: mỗi công việc có checkbox và nút xóa
  taskList: {
    items: [
      { id: 1, text: 'Học Basic Design', completed: false },
      { id: 2, text: 'Làm bài tập', completed: true }
    ],
    // Mỗi item sẽ được render với checkbox và nút xóa
    renderItem: (item) => {
      return `
        <div class="task-item">
          <input type="checkbox" ${item.completed ? 'checked' : ''} />
          <span>${item.text}</span>
          <button class="delete-btn">Xóa</button>
        </div>
      `;
    }
  },
  // Form thêm công việc
  addForm: {
    fields: [
      { type: 'text', name: 'taskText', label: 'Tên công việc', placeholder: 'Nhập công việc...' }
    ],
    submitButton: { text: 'Thêm' }
  }
};
```
> **Tip:** Khi thiết kế, hãy sử dụng các component tái sử dụng (reusable components) để giảm thiểu code trùng lặp. Ví dụ, button có thể được định nghĩa một lần và dùng lại nhiều nơi.

### Bước 3: Xác định tương tác cơ bản

Sau khi có các thành phần, bạn cần xác định cách chúng tương tác. Ví dụ:

- Khi click nút “Thêm công việc”, form thêm công việc sẽ hiện ra.
- Khi click checkbox, trạng thái công việc thay đổi.
- Khi click nút “Xóa”, công việc bị xóa khỏi danh sách.
```js
// Định nghĩa các tương tác cơ bản
const interactions = {
  // Khi click nút thêm
  onAddClick: () => {
    // Hiển thị form thêm công việc
    document.getElementById('addForm').style.display = 'block';
  },
  // Khi submit form
  onFormSubmit: (event) => {
    event.preventDefault();
    const taskText = document.getElementById('taskInput').value;
    if (taskText.trim() === '') {
      alert('Vui lòng nhập tên công việc');
      return;
    }
    // Thêm công việc vào danh sách (sẽ được xử lý bởi Business Logic Layer)
    addTask(taskText);
    // Ẩn form
    document.getElementById('addForm').style.display = 'none';
  }
};
```
**Warning:** Trong Basic Design, bạn chỉ nên định nghĩa các tương tác hiển thị (ví dụ: hiện/ẩn form). Logic xử lý dữ liệu (ví dụ: lưu công việc vào database) nên được tách riêng.
# Ví dụ thực tế: Thiết kế form đăng nhập
### Phân tích yêu cầu

Giả sử bạn cần thiết kế một form đăng nhập cho một ứng dụng web. Yêu cầu từ người dùng:

- Người dùng nhập email và mật khẩu.
- Nếu đăng nhập thành công, chuyển đến trang chủ.
- Nếu thất bại, hiển thị thông báo lỗi.
### Thiết kế Basic Design

Dưới đây là cách bạn xây dựng Basic Design cho form đăng nhập:
```js
// Basic Design cho form đăng nhập
const loginFormDesign = {
  // Cấu trúc hiển thị
  structure: {
    // Container chính
    container: {
      type: 'div',
      class: 'login-container',
      children: [
        // Tiêu đề
        { type: 'h2', text: 'Đăng nhập' },
        // Form
        { type: 'form', id: 'loginForm', children: [
          // Trường email
          { type: 'input', name: 'email', type: 'email', placeholder: 'Email', required: true },
          // Trường mật khẩu
          { type: 'input', name: 'password', type: 'password', placeholder: 'Mật khẩu', required: true },
          // Nút submit
          { type: 'button', text: 'Đăng nhập', type: 'submit' }
        ]},
        // Khu vực hiển thị lỗi
        { type: 'div', id: 'errorMessage', class: 'error', text: '' }
      ]
    }
  },
  // Tương tác cơ bản
  interactions: {
    // Khi submit form
    onSubmit: (event) => {
      event.preventDefault();
      const email = document.querySelector('input[name="email"]').value;
      const password = document.querySelector('input[name="password"]').value;
      
      // Kiểm tra dữ liệu đầu vào (chỉ kiểm tra hiển thị)
      if (!email || !password) {
        showError('Vui lòng điền đầy đủ thông tin');
        return;
      }
      // Gọi API đăng nhập (sẽ được xử lý bởi Business Logic Layer)
      loginAPI(email, password);
    },
    // Hiển thị lỗi
    showError: (message) => {
      const errorDiv = document.getElementById('errorMessage');
      errorDiv.textContent = message;
      errorDiv.style.display = 'block';
    }
  }
};
```
### Render thành HTML

Sau khi có Basic Design, bạn có thể render nó thành HTML:
```js
<!-- HTML được sinh ra từ Basic Design -->
<div class="login-container">
  <h2>Đăng nhập</h2>
  <form id="loginForm">
    <input type="email" name="email" placeholder="Email" required />
    <input type="password" name="password" placeholder="Mật khẩu" required />
    <button type="submit">Đăng nhập</button>
  </form>
  <div id="errorMessage" class="error" style="display: none;"></div>
</div>
```
**Tip:** Trong thực tế, bạn có thể sử dụng các framework như React hoặc Vue để render các component từ Basic Design. Điều này giúp code dễ bảo trì hơn.
# Các lỗi thường gặp khi làm Basic Design
### 1. Nhầm lẫn giữa Basic Design và High-Level Design

**Lỗi:** Thiết kế các thành phần hiển thị nhưng lại bao gồm cả kiến trúc hệ thống (ví dụ: thiết kế database).

**Giải pháp:** Luôn nhớ rằng Basic Design chỉ tập trung vào Visible Layer. Các quyết định về kiến trúc nên được để ở High-Level Design.

### 2. Thiếu tính nhất quán trong thiết kế

**Lỗi:** Các button có kiểu dáng khác nhau ở các trang khác nhau (ví dụ: button “Đăng nhập” màu xanh ở trang A, màu đỏ ở trang B).

**Giải pháp:** Xây dựng một Design System (hệ thống thiết kế) với các component chuẩn hóa. Ví dụ, sử dụng một thư viện như Material-UI hoặc Ant Design.

### 3. Đưa logic nghiệp vụ vào Visible Layer

**Lỗi:** Kiểm tra dữ liệu hoặc xử lý tính toán trong code hiển thị.

**Giải pháp:** Tách biệt rõ ràng giữa Visible Layer và Business Logic Layer. Sử dụng các design pattern như MVC (Model-View-Controller) để phân chia trách nhiệm.

### 4. Bỏ qua trải nghiệm người dùng (UX)

**Lỗi:** Thiết kế đẹp nhưng khó sử dụng. Ví dụ: button quá nhỏ, màu sắc khó đọc.

**Giải pháp:** Luôn kiểm tra với người dùng thực tế. Sử dụng các nguyên tắc UX như Fitts’s Law (định luật Fitts) để thiết kế các thành phần dễ tương tác.

### 5. Không tối ưu cho các thiết bị khác nhau

**Lỗi:** Thiết kế chỉ hoạt động tốt trên desktop, nhưng xấu trên mobile.

**Giải pháp:** Sử dụng responsive design từ đầu. Ví dụ, sử dụng CSS Grid và Flexbox để tạo layout linh hoạt.
# Các thực hành tốt nhất (Best Practices)
### 1. Xây dựng Design System từ đầu

Tạo một thư viện các component chuẩn hóa (button, input, form) để đảm bảo tính nhất quán. Ví dụ, sử dụng Figma để thiết kế các component và sau đó chuyển chúng thành code.

### 2. Sử dụng Atomic Design Pattern

Atomic Design chia nhỏ các thành phần thành 5 cấp độ: Atoms (nguyên tử), Molecules (phân tử), Organisms (sinh vật), Templates (mẫu), và Pages (trang). Điều này giúp bạn dễ dàng quản lý và tái sử dụng các component.

### 3. Tối ưu hóa cho accessibility (a11y)

Đảm bảo rằng tất cả các thành phần hiển thị đều có thể truy cập được bằng bàn phím và screen reader. Ví dụ, thêm `aria-label` cho các button không có text.
```js
// Ví dụ: Thêm accessibility cho button
const button = {
  type: 'button',
  text: 'Đăng nhập',
  ariaLabel: 'Nút đăng nhập để vào hệ thống'
};
```
### 4. Sử dụng CSS Variables cho theme

Định nghĩa các biến CSS cho màu sắc, font chữ, và spacing để dễ dàng thay đổi theme sau này.
```css
/* Định nghĩa CSS Variables */
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --font-size-base: 16px;
  --spacing-unit: 8px;
}

.button {
  background-color: var(--primary-color);
  font-size: var(--font-size-base);
  padding: var(--spacing-unit);
}
```
### 5. Kiểm thử trên nhiều thiết bị

Luôn kiểm tra giao diện trên desktop, tablet, và mobile. Sử dụng các công cụ như BrowserStack hoặc Chrome DevTools để mô phỏng các thiết bị khác nhau.

### 6. Tách biệt rõ ràng giữa hiển thị và logic

Sử dụng các framework như React với state management (ví dụ: Redux) để tách biệt UI khỏi logic nghiệp vụ.

### 7. Ghi chú và tài liệu hóa thiết kế

Luôn ghi lại các quyết định thiết kế trong tài liệu. Ví dụ, sử dụng Notion hoặc Confluence để lưu trữ các quyết định về Basic Design.
# Tài liệu tham khảo
- [Figma Design System Documentation](https://www.figma.com/best-practices/design-systems/) - Hướng dẫn xây dựng Design System với Figma.
- [React Official Documentation](https://react.dev/) - Tài liệu chính thức về React, framework phổ biến để xây dựng UI.
- [Material-UI (MUI) Documentation](https://mui.com/) - Thư viện component UI phổ biến với thiết kế Material Design.
- [Atomic Design by Brad Frost](https://atomicdesign.bradfrost.com/) - Sách về Atomic Design Pattern, rất hữu ích cho Basic Design.
- [Stack Overflow: Basic Design vs High-Level Design](https://stackoverflow.com/questions/tagged/software-design) - Thảo luận về sự khác biệt giữa các cấp độ thiết kế.
# Tóm tắt
- **Basic Design là quá trình định nghĩa “What”** – xác định các thành phần hiển thị mà người dùng sẽ nhìn thấy và tương tác.
- **Visible Layer bao gồm UI, tương tác cơ bản, và phản hồi trực quan** – không bao gồm logic backend hay database.
- **Bản chất của Basic Design là định nghĩa có cấu trúc** – không chỉ là vẽ giao diện đẹp.
- **Các lỗi thường gặp** bao gồm nhầm lẫn giữa Basic Design và High-Level Design, thiếu nhất quán, và đưa logic nghiệp vụ vào Visible Layer.
- **Các thực hành tốt nhất** bao gồm xây dựng Design System, sử dụng Atomic Design, tối ưu accessibility, và kiểm thử trên nhiều thiết bị.

> **Lời khuyên cuối cùng:** Hãy luôn nhớ rằng Basic Design là cầu nối giữa ý tưởng và sản phẩm. Một Basic Design tốt sẽ giúp đội ngũ phát triển dễ dàng triển khai và bảo trì sản phẩm. Hãy bắt đầu bằng cách thực hành với một dự án nhỏ, như thiết kế form đăng nhập, và dần dần mở rộng ra các ứng dụng phức tạp hơn.
