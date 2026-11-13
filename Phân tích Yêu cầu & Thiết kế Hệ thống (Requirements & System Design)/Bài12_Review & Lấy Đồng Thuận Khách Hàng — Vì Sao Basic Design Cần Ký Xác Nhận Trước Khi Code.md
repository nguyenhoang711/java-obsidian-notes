Trong quy trình phát triển phần mềm chuyên nghiệp, **Basic Design** (Thiết kế cơ bản) là bước chuyển tiếp quan trọng giữa việc xác định “làm gì” (What) và “làm như thế nào” (How). Đây là giai đoạn mà các yêu cầu nghiệp vụ được chuyển hóa thành kiến trúc tổng thể, luồng dữ liệu, và giao diện người dùng. Tuy nhiên, nhiều đội ngũ phát triển thường mắc sai lầm khi bỏ qua hoặc làm qua loa bước **Review và lấy đồng thuận từ khách hàng**, dẫn đến những hậu quả nghiêm trọng như làm lại toàn bộ, vượt ngân sách, hoặc mất lòng tin.

Bài học này sẽ đi sâu vào quy trình review Basic Design, lý do tại sao việc có sự nhất trí của phía khách hàng trước khi bắt đầu code là bắt buộc, và các kỹ thuật thực tế để đảm bảo thành công. Bạn sẽ hiểu được rằng việc ký xác nhận không chỉ là thủ tục hành chính mà là công cụ quản lý rủi ro mạnh mẽ, giúp cả hai bên cùng nắm rõ phạm vi và kỳ vọng. Vào năm 2026, với sự phát triển của các phương pháp Agile và DevOps, quy trình này càng trở nên quan trọng để duy trì tốc độ mà không hy sinh chất lượng.

> **Note:** Bài viết này dành cho các lập trình viên, trưởng nhóm kỹ thuật, và quản lý dự án muốn xây dựng quy trình phát triển chuyên nghiệp hơn.
# Basic Design Là Gì? Tại Sao Cần Review?
### Định Nghĩa Basic Design

**Basic Design** (còn gọi là thiết kế khái quát hoặc thiết kế mức cao) là giai đoạn xác định kiến trúc tổng thể của hệ thống, bao gồm:

- Các module chính và mối quan hệ giữa chúng.
- Luồng dữ liệu và xử lý nghiệp vụ.
- Giao diện người dùng (UI/UX) ở mức wireframe hoặc mockup.
- Các thành phần kỹ thuật như cơ sở dữ liệu, API, và tích hợp bên thứ ba.

Khác với **Detailed Design** (thiết kế chi tiết) đi sâu vào từng hàm, lớp, và thuật toán, Basic Design tập trung vào bức tranh lớn để đảm bảo mọi người hiểu đúng hướng đi.

### Vai Trò Của Review Trong Basic Design

Review Basic Design không chỉ là kiểm tra kỹ thuật mà còn là cơ hội để:

- **Xác nhận tính khả thi**: Liệu thiết kế có đáp ứng được yêu cầu nghiệp vụ không?
- **Phát hiện sớm lỗi**: Các vấn đề về logic, luồng dữ liệu, hoặc bảo mật có thể được phát hiện trước khi code.
- **Đồng bộ hóa kỳ vọng**: Đảm bảo khách hàng và đội ngũ phát triển có cùng hiểu biết về sản phẩm cuối.
```js
// Ví dụ: Quy trình review Basic Design đơn giản (dùng Node.js để mô phỏng)
const designReviewProcess = {
  stages: [
    { name: 'Tạo tài liệu Basic Design', owner: 'Technical Lead' },
    { name: 'Review nội bộ kỹ thuật', owner: 'Development Team' },
    { name: 'Review với khách hàng', owner: 'Project Manager' },
    { name: 'Ký xác nhận (Sign-off)', owner: 'Client Representative' }
  ],
  validate: function() {
    // Kiểm tra xem tất cả các bước đã hoàn thành chưa
    const completed = this.stages.every(stage => stage.completed);
    if (!completed) {
      console.error('Chưa hoàn thành tất cả các bước review!');
      return false;
    }
    console.log('Quy trình review đã hoàn tất. Có thể bắt đầu code.');
    return true;
  }
};

// Thực thi
designReviewProcess.stages[0].completed = true;
designReviewProcess.stages[1].completed = true;
designReviewProcess.stages[2].completed = true;
designReviewProcess.stages[3].completed = true;
designReviewProcess.validate();
// Output: Quy trình review đã hoàn tất. Có thể bắt đầu code.
```
# Quy Trình Review Basic Design Chi Tiết
### Bước 1: Chuẩn Bị Tài Liệu Review

Tài liệu Basic Design cần được chuẩn bị kỹ lưỡng với các thành phần sau:

- **Sơ đồ kiến trúc**: Sử dụng UML hoặc diagram tool (ví dụ: [Draw.io](http://draw.io/), Lucidchart).
- **Wireframe/Mockup**: Giao diện người dùng ở mức độ thô.
- **Mô tả luồng nghiệp vụ**: Các use case và user story.
- **Danh sách API**: Endpoint, phương thức, và dữ liệu đầu vào/đầu ra.

### Bước 2: Review Nội Bộ Kỹ Thuật

Trước khi trình khách hàng, đội ngũ kỹ thuật cần review nội bộ để:

- Phát hiện các vấn đề kỹ thuật (ví dụ: lựa chọn công nghệ không phù hợp).
- Đảm bảo tính nhất quán giữa các module.
- Đánh giá hiệu năng và khả năng mở rộng.

> **Tip:** Tổ chức review nội bộ dưới dạng **walkthrough** hoặc **inspection** tùy theo quy mô dự án. Với dự án Agile, có thể dùng **sprint review** để tích hợp.

### Bước 3: Review Với Khách Hàng

Đây là bước quan trọng nhất. Khách hàng cần hiểu và đồng ý với thiết kế trước khi code. Các hoạt động chính:

- Trình bày tài liệu qua meeting (online hoặc offline).
- Giải thích các quyết định thiết kế.
- Thu thập phản hồi và điều chỉnh nếu cần.
```js
// Ví dụ: Mô phỏng quy trình gửi tài liệu review cho khách hàng
class DesignReview {
  constructor(clientEmail, documentUrl) {
    this.clientEmail = clientEmail;
    this.documentUrl = documentUrl;
    this.status = 'pending'; // pending, reviewed, approved, rejected
    this.feedback = [];
  }

  sendForReview() {
    console.log(`Gửi tài liệu Basic Design đến ${this.clientEmail}`);
    console.log(`URL tài liệu: ${this.documentUrl}`);
    this.status = 'reviewed';
    // Trong thực tế, đây là gửi email hoặc tạo ticket trên Jira/Notion
  }

  receiveFeedback(comment) {
    this.feedback.push(comment);
    console.log(`Phản hồi từ khách hàng: ${comment}`);
    if (comment.toLowerCase().includes('đồng ý') || comment.toLowerCase().includes('approve')) {
      this.status = 'approved';
    } else {
      this.status = 'rejected';
    }
  }

  isApproved() {
    return this.status === 'approved';
  }
}

// Sử dụng
const review = new DesignReview('client@example.com', 'https://docs.google.com/...');
review.sendForReview();
review.receiveFeedback('Thiết kế OK, đồng ý triển khai.');
console.log(`Trạng thái: ${review.isApproved() ? 'Đã phê duyệt' : 'Chưa phê duyệt'}`);
// Output: Trạng thái: Đã phê duyệt
```
### Bước 4: Ký Xác Nhận (Sign-off)

Ký xác nhận là bước cuối cùng, thường được thực hiện qua:

- Email xác nhận chính thức.
- Ký điện tử (DocuSign, Adobe Sign).
- Tick trong hệ thống quản lý dự án (Jira, Asana).

> **Warning:** Không bắt đầu code nếu chưa có sign-off chính thức. Điều này có thể dẫn đến tranh chấp sau này.
# Lý Do Bắt Buộc Phải Có Đồng Thuận Trước Khi Code
### Quản Lý Rủi Ro Và Phạm Vi

Một trong những nguyên nhân chính khiến dự án thất bại là **scope creep** (mở rộng phạm vi không kiểm soát). Khi Basic Design được ký xác nhận, nó trở thành **baseline** (đường cơ sở) để so sánh. Mọi thay đổi sau đó đều phải qua quy trình change request, giúp kiểm soát ngân sách và thời gian.

**Ví dụ thực tế:**

- Dự án A: Không ký xác nhận Basic Design → Khách hàng yêu cầu thêm tính năng trong quá trình code → Phải làm lại 30% code → Trễ tiến độ 2 tháng.
- Dự án B: Có ký xác nhận → Mọi thay đổi đều được đánh giá tác động → Quản lý rủi ro tốt hơn.

### Đảm Bảo Tính Minh Bạch Và Trách Nhiệm

Khi khách hàng ký xác nhận, họ chịu trách nhiệm về quyết định của mình. Điều này tạo ra sự minh bạch:

- Khách hàng hiểu rõ họ sẽ nhận được gì.
- Đội ngũ phát triển biết chính xác phạm vi công việc.
- Tránh tình trạng “đổ lỗi” khi có vấn đề phát sinh.

### Cải Thiện Chất Lượng Giao Tiếp

Quy trình review buộc cả hai bên phải giao tiếp chi tiết. Ví dụ, khách hàng có thể phát hiện ra rằng wireframe không phản ánh đúng quy trình nghiệp vụ thực tế, từ đó điều chỉnh kịp thời.
```js
// Ví dụ: So sánh hai kịch bản có và không có sign-off
const scenarios = [
  {
    name: 'Không có sign-off',
    risks: ['Scope creep', 'Làm lại nhiều lần', 'Mất lòng tin'],
    costImpact: 'Cao (tăng 50-100% ngân sách)',
    successRate: 'Thấp (dưới 40%)'
  },
  {
    name: 'Có sign-off',
    risks: ['Chậm tiến độ review', 'Khó thay đổi sau này'],
    costImpact: 'Thấp (tăng 10-20% nếu có change request)',
    successRate: 'Cao (trên 80%)'
  }
];

scenarios.forEach(scenario => {
  console.log(`Kịch bản: ${scenario.name}`);
  console.log(`Rủi ro: ${scenario.risks.join(', ')}`);
  console.log(`Tác động chi phí: ${scenario.costImpact}`);
  console.log(`Tỷ lệ thành công: ${scenario.successRate}`);
  console.log('---');
});
// Output: So sánh rõ ràng giữa hai kịch bản
```
# Các Kỹ Thuật Review Hiệu Quả
### Sử Dụng Checklist Review

Checklist giúp đảm bảo không bỏ sót vấn đề quan trọng. Dưới đây là checklist mẫu cho Basic Design:

|Mục|Mô tả|Trạng thái|
|---|---|---|
|Kiến trúc|Có sơ đồ kiến trúc tổng thể không?|✅/❌|
|Luồng dữ liệu|Mô tả luồng dữ liệu chính?|✅/❌|
|Giao diện|Wireframe đã được phê duyệt?|✅/❌|
|Bảo mật|Có xem xét các rủi ro bảo mật không?|✅/❌|
|Hiệu năng|Đã đánh giá hiệu năng dự kiến?|✅/❌|

### Tổ Chức Workshop Review

Thay vì chỉ gửi tài liệu qua email, tổ chức **workshop** với khách hàng để:

- Trình bày trực tiếp thiết kế.
- Thảo luận và giải đáp thắc mắc ngay lập tức.
- Sử dụng các công cụ cộng tác như Miro, Figma để chỉnh sửa real-time.

> **Tip:** Với khách hàng không rành kỹ thuật, hãy dùng ngôn ngữ đơn giản, tránh thuật ngữ chuyên ngành. Tập trung vào “giá trị nghiệp vụ” thay vì “công nghệ”.

### Sử Dụng Prototype

Một prototype (bản mẫu) có thể giúp khách hàng hình dung rõ hơn về sản phẩm. Các công cụ như Figma, Adobe XD, hoặc thậm chí code prototype bằng React có thể được sử dụng.
```js
// Ví dụ: Tạo prototype đơn giản bằng React (phiên bản 2026)
import React, { useState } from 'react';

function DesignPrototype() {
  const [step, setStep] = useState(1);
  const [feedback, setFeedback] = useState('');

  const handleNext = () => {
    if (step < 3) setStep(step + 1);
    else alert('Hoàn thành prototype review!');
  };

  return (
    <div>
      <h2>Prototype Basic Design - Bước {step}/3</h2>
      {step === 1 && <p>Màn hình đăng nhập: username, password, nút Login</p>}
      {step === 2 && <p>Màn hình dashboard: biểu đồ, menu điều hướng</p>}
      {step === 3 && <p>Màn hình báo cáo: filter, export PDF</p>}
      <textarea 
        placeholder="Nhập phản hồi của bạn..."
        value={feedback}
        onChange={e => setFeedback(e.target.value)}
      />
      <button onClick={handleNext}>Tiếp theo</button>
    </div>
  );
}

export default DesignPrototype;
```
# Các Sai Lầm Thường Gặp
### 1. Bỏ Qua Review Nội Bộ Trước Khi Trình Khách Hàng

**Sai lầm:** Gửi tài liệu chưa được kiểm tra kỹ thuật cho khách hàng.  
**Giải pháp:** Luôn tổ chức review nội bộ trước. Sử dụng peer review hoặc code review tool.

### 2. Không Ghi Lại Các Quyết Định Thiết Kế

**Sai lầm:** Chỉ có tài liệu cuối cùng mà không có lịch sử thay đổi.  
**Giải pháp:** Sử dụng version control (Git) cho tài liệu, ghi chú lý do thay đổi.

### 3. Cho Rằng Khách Hàng Luôn Hiểu Đúng

**Sai lầm:** Giả định khách hàng hiểu hết về kỹ thuật.  
**Giải pháp:** Kiểm tra lại bằng cách hỏi “Anh/chị có thể mô tả lại cách hệ thống hoạt động không?”

### 4. Bắt Đầu Code Khi Chưa Có Sign-off

**Sai lầm:** “Code trước, ký sau” để tiết kiệm thời gian.  
**Giải pháp:** Tuân thủ quy trình. Nếu vội, hãy làm prototype thay vì code production.

### 5. Không Cập Nhật Tài Liệu Khi Có Thay Đổi

**Sai lầm:** Chỉnh sửa code mà không cập nhật Basic Design.  
**Giải pháp:** Thiết lập quy trình change management: mọi thay đổi đều phải cập nhật tài liệu và được phê duyệt lại.
# Các Thực Hành Tốt Nhất
### 1. Sử Dụng Template Chuẩn Cho Tài Liệu Basic Design

Tạo template với các section cố định: Mục tiêu, Phạm vi, Kiến trúc, Luồng dữ liệu, Giao diện, Rủi ro. Điều này giúp đảm bảo tính nhất quán và dễ review.

### 2. Thiết Lập Quy Trình Change Request Rõ Ràng

Khi có thay đổi sau sign-off, cần:

- Đánh giá tác động (thời gian, chi phí).
- Trình lên khách hàng phê duyệt.
- Cập nhật tài liệu và ký lại nếu cần.

### 3. Tổ Chức Review Định Kỳ

Ngay cả khi đã có sign-off, vẫn nên tổ chức review định kỳ (ví dụ: mỗi sprint) để đảm bảo không có sai lệch.

### 4. Sử Dụng Công Cụ Hỗ Trợ

- **Jira/Confluence**: Quản lý tài liệu và quy trình.
- **Figma**: Thiết kế giao diện và prototype.
- **Lucidchart**: Vẽ sơ đồ kiến trúc.
- **DocuSign**: Ký điện tử.

### 5. Đào Tạo Khách Hàng Về Quy Trình

Giải thích cho khách hàng hiểu tầm quan trọng của việc review và sign-off. Họ sẽ hợp tác tốt hơn khi hiểu rõ lợi ích.

### 6. Lưu Trữ Lịch Sử Phiên Bản

Sử dụng Git cho tài liệu (Markdown, PDF) để có thể trace lại mọi thay đổi. Ví dụ:
```bash
# Lưu trữ tài liệu Basic Design trên Git
git init design-docs
cd design-docs
echo "# Basic Design - Dự án XYZ" > README.md
git add .
git commit -m "Initial commit: Basic Design v1.0"
# Khi có thay đổi
git commit -m "Update: Thêm module báo cáo theo yêu cầu khách hàng"
```
### 7. Đo Lường Hiệu Quả Của Quy Trình

Sử dụng các KPI như:

- Số lần phải làm lại sau review.
- Thời gian trung bình từ review đến sign-off.
- Mức độ hài lòng của khách hàng.
# Tài liệu tham khảo
- [Official PMI Guide to Project Management](https://www.pmi.org/learning/library/project-scope-management-6939) - Hướng dẫn quản lý phạm vi dự án.
- [GitHub - Agile Design Documentation Template](https://github.com/joelparkerhenderson/agile-design-document) - Template tài liệu thiết kế Agile.
- [Stack Overflow - How to get client sign-off on design?](https://stackoverflow.com/questions/1234567/how-to-get-client-sign-off-on-design) - Câu hỏi và câu trả lời về lấy đồng thuận khách hàng.
- [Figma Official Documentation](https://help.figma.com/hc/en-us) - Tài liệu về công cụ thiết kế Figma.
# Tổng Kết
- **Basic Design** là giai đoạn xác định kiến trúc tổng thể và luồng nghiệp vụ, cần được review kỹ lưỡng trước khi code.
- **Review và lấy đồng thuận khách hàng** là bước bắt buộc để quản lý rủi ro, đảm bảo minh bạch, và tránh scope creep.
- Quy trình review gồm 4 bước: Chuẩn bị tài liệu → Review nội bộ → Review với khách hàng → Ký xác nhận.
- Các sai lầm thường gặp bao gồm bỏ qua review nội bộ, không ghi lại quyết định, và code trước khi có sign-off.
- Thực hành tốt nhất: Sử dụng template, thiết lập change request, tổ chức workshop, và lưu trữ lịch sử phiên bản.
- **Ký xác nhận không chỉ là thủ tục mà là công cụ quản lý rủi ro mạnh mẽ**, giúp dự án thành công với tỷ lệ cao hơn.

> **Lời khuyên cuối cùng:** Đừng bao giờ đánh đổi chất lượng quy trình để lấy tốc độ. Một giờ review Basic Design có thể tiết kiệm hàng tuần code lại sau này. Hãy coi khách hàng là đối tác, không phải là người phê duyệt thụ động. Sự đồng thuận từ cả hai phía là chìa khóa cho mọi dự án phần mềm thành công.
