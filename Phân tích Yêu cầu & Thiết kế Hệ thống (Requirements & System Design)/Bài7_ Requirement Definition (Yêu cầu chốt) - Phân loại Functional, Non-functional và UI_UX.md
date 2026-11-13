Trong vòng đời phát triển phần mềm, giai đoạn xác định yêu cầu (Requirement Definition) là bước ngoặt quan trọng nhất, quyết định sự thành bại của toàn bộ dự án. Đây là lúc các bên liên quan – từ chủ đầu tư, đội ngũ sản phẩm đến vendor (nhà cung cấp dịch vụ phát triển) – phải ngồi lại để chốt một cách rõ ràng, chi tiết những gì sẽ được xây dựng. Nếu bước này làm không tốt, dự án sẽ rơi vào vòng xoáy thay đổi yêu cầu, vượt ngân sách và trễ hạn. Bài học hôm nay sẽ tập trung vào ba loại yêu cầu cốt lõi: **Functional Requirements** (yêu cầu chức năng), **Non-functional Requirements** (yêu cầu phi chức năng) và **UI/UX Requirements** (yêu cầu giao diện và trải nghiệm người dùng). Việc phân loại này không chỉ giúp tài liệu yêu cầu trở nên có cấu trúc mà còn là cơ sở để phân định trách nhiệm rõ ràng giữa các bên, đặc biệt là với vendor.

Tại sao điều này lại quan trọng? Hãy tưởng tượng bạn đặt hàng một chiếc bánh sinh nhật. Nếu chỉ nói “làm một cái bánh đẹp”, người thợ làm bánh có thể tạo ra một tác phẩm nghệ thuật nhưng không có chỗ để cắm nến, hoặc bánh quá ngọt không ai ăn được. Tương tự, trong phát triển phần mềm, yêu cầu mơ hồ dẫn đến sản phẩm không đáp ứng nhu cầu thực tế. Requirement Definition chính là bản vẽ chi tiết, nơi mọi chức năng, hiệu năng, bảo mật và giao diện đều được mô tả cụ thể. Đến năm 2026, với sự phổ biến của Agile và DevOps, quy trình này càng trở nên tinh gọn nhưng vẫn đòi hỏi sự chính xác cao.

Bài viết này sẽ hướng dẫn bạn cách phân loại và viết yêu cầu một cách chuyên nghiệp, kèm theo các ví dụ thực tế và mã nguồn minh họa. Bạn sẽ hiểu được sự khác biệt giữa ba loại yêu cầu, cách chúng tương tác với nhau, và quan trọng nhất – làm thế nào để đạt được sự đồng thuận với vendor trước khi bắt đầu phát triển.
## Functional Requirements – Yêu cầu chức năng
### Định nghĩa và vai trò

**Functional Requirements** mô tả chính xác hệ thống phải _làm gì_. Đây là những hành vi, chức năng cụ thể mà người dùng có thể tương tác trực tiếp. Ví dụ: “Người dùng có thể đăng nhập bằng email và mật khẩu”, “Hệ thống gửi email xác nhận sau khi đặt hàng”. Mỗi yêu cầu chức năng thường được viết dưới dạng “Hệ thống phải [hành động] khi [điều kiện]”.

Trong thực tế, yêu cầu chức năng chiếm phần lớn nội dung của SRS. Chúng là cơ sở để lập kế hoạch phát triển, kiểm thử và bàn giao. Khi làm việc với vendor, bạn cần liệt kê tất cả các chức năng một cách chi tiết, tránh để sót bất kỳ trường hợp nào. Một mẹo nhỏ: hãy sử dụng Use Case Diagram hoặc User Story Map để hình dung luồng công việc trước khi viết.

### Cách viết yêu cầu chức năng hiệu quả

Một yêu cầu chức năng tốt cần tuân theo nguyên tắc **INVEST** (Independent, Negotiable, Valuable, Estimable, Small, Testable). Dưới đây là ví dụ về một yêu cầu chức năng cho tính năng “Đặt hàng” trong ứng dụng thương mại điện tử:
```javascipt
// Yêu cầu chức năng: FR-001 - Thêm sản phẩm vào giỏ hàng
// Mô tả: Người dùng có thể thêm sản phẩm vào giỏ hàng từ trang chi tiết sản phẩm.
// Điều kiện tiên quyết: Người dùng đã đăng nhập và sản phẩm còn hàng.

// Ví dụ code minh họa backend (Node.js + Express)
app.post('/api/cart/add', async (req, res) => {
  const { userId, productId, quantity } = req.body;

  // Kiểm tra đầu vào
  if (!userId || !productId || !quantity) {
    return res.status(400).json({ error: 'Thiếu thông tin bắt buộc' });
  }

  try {
    // Kiểm tra sản phẩm tồn tại và còn hàng
    const product = await Product.findById(productId);
    if (!product || product.stock < quantity) {
      return res.status(404).json({ error: 'Sản phẩm không khả dụng' });
    }

    // Thêm vào giỏ hàng (giả sử có model Cart)
    let cart = await Cart.findOne({ userId });
    if (!cart) {
      cart = new Cart({ userId, items: [] });
    }

    // Kiểm tra sản phẩm đã có trong giỏ chưa
    const existingItem = cart.items.find(item => item.productId === productId);
    if (existingItem) {
      existingItem.quantity += quantity;
    } else {
      cart.items.push({ productId, quantity });
    }

    await cart.save();
    res.status(200).json({ message: 'Đã thêm vào giỏ hàng', cart });
  } catch (error) {
    res.status(500).json({ error: 'Lỗi máy chủ' });
  }
});
```
**Mẹo:** Khi viết yêu cầu chức năng cho vendor, hãy kèm theo cả các trường hợp ngoại lệ (exception cases) như: sản phẩm hết hàng, người dùng chưa đăng nhập, số lượng vượt quá giới hạn. Điều này giúp tránh tranh cãi sau này.
# Non-functional Requirements – Yêu cầu phi chức năng
### Định nghĩa và tầm quan trọng

**Non-functional Requirements** (NFR) mô tả hệ thống phải _tốt như thế nào_ khi thực hiện các chức năng. Chúng bao gồm hiệu năng (performance), bảo mật (security), khả năng mở rộng (scalability), độ tin cậy (reliability), khả năng bảo trì (maintainability) và nhiều khía cạnh khác. NFR thường bị bỏ qua trong giai đoạn đầu, nhưng hậu quả có thể rất nghiêm trọng: một ứng dụng có đầy đủ chức năng nhưng load mất 10 giây sẽ không ai dùng.

Trong hợp đồng với vendor, NFR thường là điểm gây tranh cãi nhất vì chúng khó đo lường và kiểm chứng. Ví dụ: “Hệ thống phải nhanh” là một yêu cầu mơ hồ. Thay vào đó, hãy viết: “API trả về kết quả trong vòng 200ms dưới tải 1000 request/giây”. Các chỉ số này được gọi là **SLI** (Service Level Indicator) và **SLO** (Service Level Objective).

### Các loại NFR phổ biến

Dưới đây là bảng so sánh các loại NFR thường gặp và cách đo lường chúng:

|Loại NFR|Mô tả|Ví dụ chỉ số đo lường|
|---|---|---|
|Hiệu năng|Tốc độ xử lý và thời gian phản hồi|Thời gian phản hồi API < 200ms|
|Bảo mật|Bảo vệ dữ liệu và chống tấn công|Mật khẩu phải được mã hóa bằng bcrypt|
|Khả năng mở rộng|Hệ thống có thể xử lý tăng tải|Hỗ trợ 10.000 người dùng đồng thời|
|Độ tin cậy|Hệ thống hoạt động ổn định|Uptime 99.9% (tương đương ~8.76 giờ downtime/năm)|
|Khả năng bảo trì|Dễ dàng sửa lỗi và nâng cấp|Code coverage > 80%|

Ví dụ về yêu cầu phi chức năng cho hệ thống thanh toán:
```javascript
// Non-functional Requirement: NFR-001 - Hiệu năng API thanh toán
// Mô tả: API xử lý thanh toán phải hoàn thành trong vòng 500ms
// dưới tải 500 request/giây, với độ chính xác 99.99%.

// Code minh họa kiểm tra hiệu năng bằng k6 (công cụ load testing)
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 500 }, // Tăng dần lên 500 users
    { duration: '1m', target: 500 },  // Giữ ổn định
    { duration: '30s', target: 0 },   // Giảm dần
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'], // 95% request phải dưới 500ms
    http_req_failed: ['rate<0.01'],   // Tỷ lệ lỗi dưới 1%
  },
};

export default function () {
  const payload = JSON.stringify({
    userId: 'test_user',
    amount: 100000,
    currency: 'VND',
  });

  const params = {
    headers: { 'Content-Type': 'application/json' },
  };

  const res = http.post('https://api.example.com/payment', payload, params);

  check(res, {
    'status là 200': (r) => r.status === 200,
    'thời gian phản hồi < 500ms': (r) => r.timings.duration < 500,
  });

  sleep(1);
}
```
**Cảnh báo:** NFR thường yêu cầu đầu tư lớn về hạ tầng và kiến trúc. Đừng đặt ra các chỉ số không thực tế (ví dụ: uptime 99.999% cho một ứng dụng nhỏ) vì chi phí sẽ rất cao. Hãy thương lượng với vendor dựa trên ngân sách và mức độ quan trọng của hệ thống.
# UI/UX Requirements – Yêu cầu giao diện và trải nghiệm người dùng
### Sự khác biệt giữa UI và UX

**UI (User Interface)** là phần giao diện trực quan: màu sắc, bố cục, font chữ, nút bấm. **UX (User Experience)** là trải nghiệm tổng thể khi người dùng tương tác với hệ thống: dễ sử dụng, hiệu quả, thỏa mãn. Trong Requirement Definition, UI/UX requirements thường được mô tả qua wireframe, prototype và style guide.

Ví dụ: UI requirement có thể là “Nút ‘Đặt hàng’ có màu xanh lá, kích thước 48x48px”. UX requirement có thể là “Quy trình đặt hàng không quá 3 bước, người dùng có thể hoàn tất trong vòng 2 phút”. Cả hai đều quan trọng, nhưng UX thường khó đo lường hơn và cần được kiểm chứng qua user testing.

### Cách viết UI/UX requirements

Khi làm việc với vendor, bạn cần cung cấp tài liệu UI/UX đủ chi tiết để họ có thể phát triển mà không cần hỏi lại. Dưới đây là ví dụ về yêu cầu UI/UX cho form đăng ký:
```javascript
// UI/UX Requirement: UIUX-001 - Form đăng ký người dùng
// Mô tả: Form đăng ký phải có các trường: Họ tên, Email, Mật khẩu, Xác nhận mật khẩu.
// UX: Hiển thị thông báo lỗi ngay khi người dùng nhập sai (inline validation).

// Code minh họa component React với kiểm tra đầu vào thời gian thực
import React, { useState } from 'react';

const RegistrationForm = () => {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    password: '',
    confirmPassword: '',
  });
  const [errors, setErrors] = useState({});

  // Hàm kiểm tra đầu vào ngay khi người dùng gõ (real-time validation)
  const validateField = (name, value) => {
    switch (name) {
      case 'email':
        // UX: Kiểm tra định dạng email hợp lệ
        return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)
          ? ''
          : 'Email không hợp lệ';
      case 'password':
        // UX: Mật khẩu phải có ít nhất 8 ký tự, bao gồm chữ hoa, chữ thường và số
        return value.length >= 8 && /[A-Z]/.test(value) && /[0-9]/.test(value)
          ? ''
          : 'Mật khẩu phải có ít nhất 8 ký tự, gồm chữ hoa và số';
      case 'confirmPassword':
        // UX: Xác nhận mật khẩu phải khớp
        return value === formData.password ? '' : 'Mật khẩu không khớp';
      default:
        return '';
    }
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData((prev) => ({ ...prev, [name]: value }));

    // UI: Hiển thị lỗi ngay lập tức (inline error message)
    const error = validateField(name, value);
    setErrors((prev) => ({ ...prev, [name]: error }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // Kiểm tra tất cả trường trước khi submit
    const newErrors = {};
    Object.keys(formData).forEach((key) => {
      const error = validateField(key, formData[key]);
      if (error) newErrors[key] = error;
    });
    setErrors(newErrors);

    if (Object.keys(newErrors).length === 0) {
      // Gửi dữ liệu lên server
      console.log('Đăng ký thành công', formData);
    }
  };

  return (
    <form onSubmit={handleSubmit} style={{ maxWidth: '400px', margin: '0 auto' }}>
      <div style={{ marginBottom: '1rem' }}>
        <label htmlFor="name">Họ tên:</label>
        <input
          type="text"
          id="name"
          name="name"
          value={formData.name}
          onChange={handleChange}
          style={{ width: '100%', padding: '8px', border: errors.name ? '2px solid red' : '1px solid #ccc' }}
        />
        {errors.name && <span style={{ color: 'red', fontSize: '0.8rem' }}>{errors.name}</span>}
      </div>

      <div style={{ marginBottom: '1rem' }}>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          style={{ width: '100%', padding: '8px', border: errors.email ? '2px solid red' : '1px solid #ccc' }}
        />
        {errors.email && <span style={{ color: 'red', fontSize: '0.8rem' }}>{errors.email}</span>}
      </div>

      {/* Các trường password và confirmPassword tương tự */}
      <button type="submit" style={{ padding: '10px 20px', backgroundColor: '#4CAF50', color: 'white', border: 'none' }}>
        Đăng ký
      </button>
    </form>
  );
};

export default RegistrationForm;
```
**Mẹo:** Để tránh hiểu lầm với vendor, hãy cung cấp một style guide (hướng dẫn về màu sắc, font chữ, khoảng cách) và một số màn hình mẫu (high-fidelity mockups) trước khi phát triển. Công cụ như Figma, Sketch là lựa chọn phổ biến vào năm 2026.
# Bước ngoặt phân rõ trách nhiệm với vendor
### Tại sao cần sự đồng thuận?

Khi đã có danh sách yêu cầu chi tiết, bước tiếp theo là đạt được sự đồng thuận với vendor. Đây là bước ngoặt quan trọng vì nó xác định ranh giới trách nhiệm: chủ đầu tư chịu trách nhiệm về _nội dung_ (các yêu cầu kinh doanh), vendor chịu trách nhiệm về _kỹ thuật_ (cách triển khai). Nếu không có sự đồng thuận, vendor có thể đổ lỗi cho chủ đầu tư vì yêu cầu mơ hồ, hoặc chủ đầu tư có thể yêu cầu vendor làm thêm việc ngoài phạm vi.

Một hợp đồng tốt thường bao gồm:

- **Phạm vi công việc (Scope of Work - SoW)**: Liệt kê tất cả các yêu cầu đã chốt.
- **Tiêu chí chấp nhận (Acceptance Criteria)**: Điều kiện để vendor bàn giao sản phẩm.
- **Quy trình thay đổi (Change Management)**: Cách xử lý khi có yêu cầu mới phát sinh.

### Quy trình đạt đồng thuận

Dưới đây là quy trình 5 bước để đạt được sự đồng thuận với vendor:

1. **Review tài liệu yêu cầu**: Cả hai bên cùng đọc và góp ý cho SRS.
2. **Ước lượng và đánh giá rủi ro**: Vendor ước lượng thời gian, chi phí; chủ đầu tư đánh giá mức độ phù hợp.
3. **Đàm phán**: Thương lượng về các điểm chưa thống nhất (ví dụ: giảm bớt chức năng để giữ ngân sách).
4. **Ký kết**: Cả hai bên ký vào tài liệu yêu cầu và hợp đồng.
5. **Kick-off meeting**: Họp khởi động dự án, xác nhận lại lần cuối.

Ví dụ về một phần của tài liệu đồng thuận:
```javascript
// Phần xác nhận yêu cầu trong hợp đồng (contract snippet)
const contractRequirements = {
  functional: [
    { id: 'FR-001', description: 'Đăng nhập bằng email/mật khẩu', status: 'approved' },
    { id: 'FR-002', description: 'Đặt hàng tối đa 10 sản phẩm', status: 'approved' },
  ],
  nonFunctional: [
    { id: 'NFR-001', description: 'API response < 200ms', slo: '99.9%', status: 'approved' },
  ],
  uiUx: [
    { id: 'UIUX-001', description: 'Responsive trên mobile và desktop', status: 'pending' },
  ],
  // Ghi chú: UIUX-001 đang chờ phê duyệt từ phía chủ đầu tư
};

// Hàm kiểm tra trạng thái đồng thuận
function checkConsensus(requirements) {
  const pending = Object.values(requirements).flat().filter(req => req.status === 'pending');
  if (pending.length > 0) {
    console.log(`Còn ${pending.length} yêu cầu chưa được phê duyệt:`);
    pending.forEach(req => console.log(`- ${req.id}: ${req.description}`));
    return false;
  }
  console.log('Tất cả yêu cầu đã được đồng thuận. Sẵn sàng phát triển!');
  return true;
}

checkConsensus(contractRequirements);
```
**Cảnh báo:** Đừng bao giờ bắt đầu phát triển khi còn yêu cầu ở trạng thái “pending”. Điều này dẫn đến rủi ro vendor làm sai hướng hoặc phát sinh chi phí ngoài dự kiến.
# Những sai lầm thường gặp
Dưới đây là 5 sai lầm phổ biến khi viết Requirement Definition và cách khắc phục:

1. **Yêu cầu mơ hồ**: Ví dụ: “Hệ thống phải thân thiện với người dùng”. **Giải pháp**: Cụ thể hóa thành “Thời gian hoàn thành tác vụ đặt hàng dưới 3 phút”.
    
2. **Bỏ sót yêu cầu phi chức năng**: Nhiều dự án chỉ tập trung vào chức năng mà quên mất hiệu năng, bảo mật. **Giải pháp**: Dành ít nhất 20% thời gian để viết NFR.
    
3. **Không có tiêu chí chấp nhận**: Vendor bàn giao sản phẩm nhưng không biết thế nào là “đạt”. **Giải pháp**: Viết acceptance criteria cho từng yêu cầu (ví dụ: “Khi nhấn nút đăng nhập, nếu sai mật khẩu 5 lần, tài khoản bị khóa 30 phút”).
    
4. **Thay đổi yêu cầu liên tục**: Chủ đầu tư thêm chức năng mới sau khi đã chốt. **Giải pháp**: Áp dụng quy trình change management: mỗi thay đổi phải được đánh giá tác động và ký lại.
    
5. **Không kiểm tra tính khả thi**: Yêu cầu kỹ thuật quá cao so với công nghệ hiện có. **Giải pháp**: Vendor cần đưa ra đánh giá rủi ro kỹ thuật trước khi ký hợp đồng.
# Thực hành tốt nhất
Để đảm bảo Requirement Definition thành công, hãy áp dụng 7 thực hành sau:

1. **Sử dụng template chuẩn**: Dùng IEEE 830 hoặc các template hiện đại như Volere để đảm bảo không thiếu phần nào.
2. **Phân loại yêu cầu rõ ràng**: Dùng ID như FR-001, NFR-001, UIUX-001 để dễ theo dõi.
3. **Viết yêu cầu có thể kiểm thử**: Mỗi yêu cầu phải có cách kiểm chứng (test case) cụ thể.
4. **Ưu tiên yêu cầu theo MoSCoW**: Must-have, Should-have, Could-have, Won’t-have để quản lý phạm vi.
5. **Sử dụng công cụ quản lý yêu cầu**: Như Jira, Confluence, hoặc các công cụ chuyên dụng như Jama Software.
6. **Tổ chức review định kỳ**: Cả hai bên họp hàng tuần để cập nhật trạng thái yêu cầu.
7. **Lưu trữ lịch sử thay đổi**: Mọi thay đổi phải được ghi lại với lý do và người phê duyệt.

> **Mẹo:** Vào năm 2026, các công cụ AI như ChatGPT có thể hỗ trợ viết yêu cầu, nhưng bạn vẫn cần kiểm tra tính chính xác và phù hợp với ngữ cảnh dự án.

# Tài liệu tham khảo
- [IEEE 830-1998 – Recommended Practice for Software Requirements Specifications](https://standards.ieee.org/ieee/830/1222/) (Tiêu chuẩn viết SRS phổ biến)
- [Volere Requirements Resources](https://www.volere.org/) (Template và hướng dẫn viết yêu cầu)
- [OWASP – Non-functional Requirements for Security](https://owasp.org/www-project-top-ten/) (Hướng dẫn yêu cầu bảo mật)
- [Stack Overflow – Requirements Definition](https://stackoverflow.com/questions/tagged/requirements) (Cộng đồng hỗ trợ)
# Sum Up
Trong bài học này, bạn đã học được cách phân loại và viết ba loại yêu cầu cốt lõi trong Requirement Definition:

- **Functional Requirements**: Mô tả hệ thống _làm gì_, cần cụ thể, có thể kiểm thử và kèm exception cases.
- **Non-functional Requirements**: Mô tả hệ thống _tốt như thế nào_, bao gồm hiệu năng, bảo mật, khả năng mở rộng – cần đo lường bằng SLO.
- **UI/UX Requirements**: Mô tả giao diện và trải nghiệm, cần kèm wireframe, prototype và style guide.
- **Đồng thuận với vendor**: Bước ngoặt quan trọng, cần ký kết hợp đồng dựa trên tài liệu yêu cầu đã chốt.

Hãy nhớ rằng, một Requirement Definition tốt không chỉ giúp dự án thành công mà còn xây dựng lòng tin giữa các bên. Đầu tư thời gian vào giai đoạn này sẽ tiết kiệm rất nhiều công sức về sau. Trong bài tiếp theo, chúng ta sẽ đi sâu vào cách viết User Story và Acceptance Criteria trong môi trường Agile.
