Trong quá trình phát triển phần mềm, chúng ta thường nghe đến khái niệm “thiết kế chi tiết” hay Detailed Design. Đây là giai đoạn quan trọng nằm giữa kiến trúc tổng thể (High-Level Design) và việc viết code thực tế. Nếu High-Level Design trả lời câu hỏi **“Cái gì?”** (What) — như hệ thống gồm những module nào, chúng giao tiếp ra sao — thì Detailed Design trả lời câu hỏi **“Như thế nào?”** (How) — cụ thể là bên trong mỗi module, chúng ta sẽ triển khai thuật toán, cấu trúc dữ liệu, và logic xử lý như thế nào mà người dùng cuối không nhìn thấy.

Tại sao Detailed Design lại quan trọng? Hãy tưởng tượng bạn xây một ngôi nhà: kiến trúc tổng thể cho biết ngôi nhà có mấy tầng, phòng nào ở đâu, nhưng thiết kế chi tiết mới quyết định hệ thống dây điện đi ngầm trong tường ra sao, ống nước được bố trí thế nào để không bị rò rỉ. Tương tự, trong phần mềm, Detailed Design giúp đảm bảo rằng code được viết ra không chỉ hoạt động đúng mà còn dễ bảo trì, mở rộng, và tối ưu hiệu năng. Đây là lớp “ẩn bên trong” (Internal Layer) mà người dùng không thấy, nhưng nó quyết định sự thành bại của dự án về lâu dài.

Trong bài học này, bạn sẽ được khám phá Detailed Design từ góc nhìn thực tế: các phương pháp, công cụ, và cách triển khai. Chúng ta sẽ đi sâu vào việc thiết kế các lớp bên trong như xử lý dữ liệu, quản lý trạng thái, và tối ưu hóa hiệu năng. Đến cuối bài, bạn sẽ hiểu rõ tại sao việc đầu tư vào Detailed Design lại giúp tiết kiệm thời gian và công sức trong dài hạn.
# Detailed Design là gì? Định nghĩa How
### Sự khác biệt giữa High-Level Design và Detailed Design

High-Level Design (HLD) giống như bản đồ thành phố: nó cho thấy các quận, đường chính, và cách chúng kết nối. Detailed Design (DD) giống như bản vẽ kỹ thuật của một tòa nhà cụ thể: nó chỉ rõ từng viên gạch, dây điện, và ống nước được đặt ở đâu. Trong phần mềm, HLD định nghĩa các module và giao tiếp giữa chúng, còn DD định nghĩa cách mỗi module hoạt động bên trong.

Ví dụ, nếu HLD quyết định rằng hệ thống có một module “Xử lý thanh toán”, thì DD sẽ trả lời các câu hỏi:

- Thuật toán nào được dùng để kiểm tra tính hợp lệ của thẻ tín dụng?
- Dữ liệu giao dịch được lưu trữ trong cấu trúc nào (ví dụ: queue, database)?
- Làm thế nào để xử lý lỗi khi kết nối với cổng thanh toán bị gián đoạn?

> **Tip:** Một nguyên tắc hay là “Mỗi module trong HLD nên có một tài liệu DD riêng”. Điều này giúp tránh việc thiết kế quá chung chung, dẫn đến code lộn xộn.

### Internal Layer: Phần ẩn bên trong

Internal Layer là thuật ngữ chỉ tất cả những gì diễn ra bên trong một module mà người dùng cuối (hoặc các module khác) không cần biết. Nó bao gồm:

- **Logic xử lý**: Các thuật toán, quy tắc nghiệp vụ.
- **Quản lý trạng thái**: Cách dữ liệu được lưu trữ tạm thời (ví dụ: biến, cache) và vĩnh viễn (database).
- **Giao tiếp nội bộ**: Cách các thành phần trong module tương tác với nhau (ví dụ: event emitters, callbacks, promises).
- **Tối ưu hóa**: Các kỹ thuật như memoization, lazy loading, hoặc batching.

Ví dụ, trong một ứng dụng chat, module “Gửi tin nhắn” có Internal Layer bao gồm:

- Kiểm tra nội dung tin nhắn có chứa từ ngữ nhạy cảm không.
- Nén tin nhắn trước khi gửi qua WebSocket.
- Lưu tin nhắn vào queue nếu server tạm thời không khả dụng.

Người dùng chỉ thấy tin nhắn được gửi đi và nhận được, nhưng không thấy các bước xử lý bên trong.
# Các phương pháp thiết kế chi tiết phổ biến
### Thiết kế hướng đối tượng (OOD)

OOD là một trong những phương pháp phổ biến nhất cho Detailed Design. Nó tập trung vào việc định nghĩa các lớp (class) và đối tượng (object), cùng với các mối quan hệ giữa chúng (kế thừa, đa hình, đóng gói). Khi áp dụng OOD, bạn cần trả lời các câu hỏi:

- Module này cần những class nào?
- Mỗi class có những thuộc tính (properties) và phương thức (methods) gì?
- Các class tương tác với nhau ra sao?

Ví dụ, thiết kế một module quản lý người dùng:
```js
// Class User: đại diện cho một người dùng
class User {
  constructor(id, name, email) {
    this.id = id;          // ID duy nhất
    this.name = name;      // Tên người dùng
    this.email = email;    // Email
    this.createdAt = new Date(); // Thời gian tạo
  }

  // Phương thức cập nhật email với kiểm tra hợp lệ
  updateEmail(newEmail) {
    if (!this.isValidEmail(newEmail)) {
      throw new Error('Email không hợp lệ');
    }
    this.email = newEmail;
    console.log(`Email của user ${this.name} đã được cập nhật`);
  }

  // Phương thức kiểm tra email (ẩn bên trong - private method)
  isValidEmail(email) {
    // Regex kiểm tra email cơ bản
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
  }
}

// Class UserManager: quản lý danh sách người dùng
class UserManager {
  constructor() {
    this.users = []; // Mảng lưu trữ người dùng
  }

  // Thêm người dùng mới
  addUser(name, email) {
    const id = this.users.length + 1; // Tạo ID tự động
    const newUser = new User(id, name, email);
    this.users.push(newUser);
    return newUser;
  }

  // Tìm người dùng theo email
  findByEmail(email) {
    return this.users.find(user => user.email === email);
  }
}

// Sử dụng
const manager = new UserManager();
const user1 = manager.addUser('Nguyen Van A', 'a@example.com');
console.log(user1); // User { id: 1, name: 'Nguyen Van A', email: 'a@example.com', createdAt: ... }
```
> **Note:** Trong ví dụ trên, phương thức `isValidEmail` là private (chỉ dùng nội bộ). Đây là một ví dụ điển hình của Internal Layer — người dùng class `User` không cần biết email được kiểm tra thế nào.

### Thiết kế hướng chức năng (Functional Design)

Trong lập trình hàm, Detailed Design tập trung vào các hàm thuần túy (pure functions) và tránh side effects. Cách tiếp cận này đặc biệt hữu ích cho các module xử lý dữ liệu, nơi tính nhất quán và dễ kiểm thử là ưu tiên hàng đầu.
```js
// Hàm thuần túy: chuyển đổi dữ liệu người dùng
function formatUserData(user) {
  // Không thay đổi dữ liệu gốc
  return {
    id: user.id,
    displayName: user.name.toUpperCase(), // Chuyển tên thành chữ hoa
    maskedEmail: maskEmail(user.email),   // Che giấu một phần email
  };
}

// Hàm phụ trợ: che giấu email
function maskEmail(email) {
  const [localPart, domain] = email.split('@');
  // Chỉ hiển thị 2 ký tự đầu của local part
  const maskedLocal = localPart.slice(0, 2) + '***';
  return `${maskedLocal}@${domain}`;
}

// Sử dụng
const rawUser = { id: 1, name: 'Nguyen Van A', email: 'a@example.com' };
const formattedUser = formatUserData(rawUser);
console.log(formattedUser); // { id: 1, displayName: 'NGUYEN VAN A', maskedEmail: 'a***@example.com' }
```
**Tip:** Functional Design giúp việc kiểm thử đơn giản hơn vì mỗi hàm chỉ phụ thuộc vào đầu vào và trả về đầu ra. Bạn không cần mock các đối tượng phức tạp.
# Công cụ và kỹ thuật hỗ trợ Detailed Design
### Sử dụng UML cho thiết kế chi tiết

Unified Modeling Language (UML) là bộ công cụ trực quan hóa thiết kế phổ biến. Trong Detailed Design, các biểu đồ UML thường dùng gồm:

- **Class Diagram**: Mô tả các class, thuộc tính, phương thức, và mối quan hệ.
- **Sequence Diagram**: Mô tả luồng tương tác giữa các đối tượng theo thời gian.
- **State Diagram**: Mô tả các trạng thái của một đối tượng và cách chúng chuyển đổi.

Ví dụ, Sequence Diagram cho quy trình đăng nhập:
```uml
User -> LoginController: Gửi yêu cầu đăng nhập
LoginController -> AuthService: Xác thực thông tin
AuthService -> Database: Truy vấn user
Database -> AuthService: Trả về user (hoặc null)
AuthService -> LoginController: Kết quả xác thực
LoginController -> User: Phản hồi (thành công/thất bại)
```
> **Warning:** UML không phải là bắt buộc cho mọi dự án. Với các module nhỏ, việc vẽ UML có thể gây lãng phí thời gian. Hãy sử dụng UML khi module có nhiều tương tác phức tạp.

### Kỹ thuật thiết kế dựa trên hành vi (BDD)

Behavior-Driven Development (BDD) giúp bạn thiết kế chi tiết từ góc nhìn của người dùng. Bạn viết các kịch bản (scenarios) mô tả hành vi mong đợi, sau đó thiết kế code để đáp ứng các kịch bản đó.
```js
// Ví dụ: Sử dụng Jest và Gherkin-style test
describe('Module Xử lý đơn hàng', () => {
  test('Khi đơn hàng hợp lệ, hệ thống xác nhận và gửi email', () => {
    // Arrange: Chuẩn bị dữ liệu
    const order = {
      id: 123,
      items: [{ product: 'Laptop', price: 15000000, quantity: 1 }],
      total: 15000000,
      customerEmail: 'customer@example.com',
    };

    // Act: Thực hiện xử lý
    const result = processOrder(order);

    // Assert: Kiểm tra kết quả
    expect(result.success).toBe(true);
    expect(result.confirmationEmail).toBeDefined();
    expect(result.confirmationEmail.to).toBe('customer@example.com');
  });

  test('Khi đơn hàng không có sản phẩm, hệ thống trả về lỗi', () => {
    const invalidOrder = {
      id: 124,
      items: [], // Không có sản phẩm
      total: 0,
      customerEmail: 'test@example.com',
    };

    expect(() => processOrder(invalidOrder)).toThrow('Đơn hàng phải có ít nhất một sản phẩm');
  });
});
```
# Triển khai Detailed Design trong thực tế
### Quy trình 5 bước thiết kế chi tiết

1. **Phân tích yêu cầu**: Xác định chức năng cụ thể của module. Ví dụ: “Module tìm kiếm sản phẩm phải hỗ trợ tìm kiếm theo tên, giá, và danh mục.”
2. **Chọn cấu trúc dữ liệu**: Quyết định dùng mảng, map, tree, hay graph. Ví dụ: Dùng Map để lưu sản phẩm theo ID để truy xuất nhanh.
3. **Thiết kế thuật toán**: Viết pseudocode hoặc flowchart cho logic chính.
4. **Xác định giao diện (interface)**: Định nghĩa cách module giao tiếp với bên ngoài (API, events).
5. **Kiểm thử và tối ưu**: Viết unit test và đánh giá hiệu năng.

Ví dụ triển khai module lọc sản phẩm:
```js
// Module lọc sản phẩm với cấu trúc dữ liệu Map
class ProductFilter {
  constructor(products) {
    // Sử dụng Map để lưu sản phẩm theo ID (truy xuất O(1))
    this.productMap = new Map();
    products.forEach(product => {
      this.productMap.set(product.id, product);
    });
  }

  // Lọc sản phẩm theo khoảng giá
  filterByPrice(minPrice, maxPrice) {
    // Chuyển Map thành mảng để lọc
    const result = [];
    for (const [id, product] of this.productMap) {
      if (product.price >= minPrice && product.price <= maxPrice) {
        result.push(product);
      }
    }
    return result;
  }

  // Tìm kiếm sản phẩm theo tên (case-insensitive)
  searchByName(keyword) {
    const lowerKeyword = keyword.toLowerCase();
    const result = [];
    for (const [id, product] of this.productMap) {
      if (product.name.toLowerCase().includes(lowerKeyword)) {
        result.push(product);
      }
    }
    return result;
  }
}

// Sử dụng
const products = [
  { id: 1, name: 'Laptop Dell', price: 15000000 },
  { id: 2, name: 'Laptop HP', price: 12000000 },
  { id: 3, name: 'Mouse Logitech', price: 500000 },
];
const filter = new ProductFilter(products);
console.log(filter.filterByPrice(10000000, 20000000));
// Output: [ { id: 1, name: 'Laptop Dell', price: 15000000 }, { id: 2, name: 'Laptop HP', price: 12000000 } ]
```
### Quản lý trạng thái nội bộ (Internal State Management)

Một phần quan trọng của Detailed Design là quyết định cách quản lý trạng thái bên trong module. Ví dụ, trong ứng dụng real-time như chat, bạn cần quản lý trạng thái kết nối WebSocket:
```js
class WebSocketManager {
  constructor(url) {
    this.url = url;
    this.socket = null;
    this.isConnected = false;   // Trạng thái kết nối
    this.messageQueue = [];     // Hàng đợi tin nhắn khi chưa kết nối
    this.reconnectAttempts = 0; // Số lần thử kết nối lại
    this.MAX_RECONNECT = 5;     // Giới hạn số lần thử
  }

  connect() {
    this.socket = new WebSocket(this.url);
    
    this.socket.onopen = () => {
      this.isConnected = true;
      this.reconnectAttempts = 0;
      // Gửi các tin nhắn đang chờ trong queue
      while (this.messageQueue.length > 0) {
        const message = this.messageQueue.shift();
        this.socket.send(message);
      }
    };

    this.socket.onclose = () => {
      this.isConnected = false;
      this.attemptReconnect();
    };

    this.socket.onerror = (error) => {
      console.error('Lỗi WebSocket:', error);
    };
  }

  sendMessage(message) {
    if (this.isConnected) {
      this.socket.send(JSON.stringify(message));
    } else {
      // Nếu chưa kết nối, đưa vào hàng đợi
      this.messageQueue.push(JSON.stringify(message));
    }
  }

  attemptReconnect() {
    if (this.reconnectAttempts < this.MAX_RECONNECT) {
      this.reconnectAttempts++;
      console.log(`Đang thử kết nối lại lần ${this.reconnectAttempts}...`);
      setTimeout(() => this.connect(), 2000 * this.reconnectAttempts);
    } else {
      console.error('Không thể kết nối lại sau 5 lần thử');
    }
  }
}
```
**Warning:** Quản lý trạng thái nội bộ cần cẩn thận với các vấn đề đồng thời (race conditions). Trong JavaScript, do đơn luồng nên ít gặp hơn, nhưng với Web Workers hoặc môi trường đa luồng, bạn cần dùng mutex hoặc atomic operations.
# Các lỗi thường gặp trong Detailed Design
### 1. Thiết kế quá phức tạp (Over-engineering)

**Lỗi:** Thêm quá nhiều lớp trừu tượng, interface, hoặc design patterns không cần thiết cho module đơn giản.  
**Giải pháp:** Áp dụng nguyên tắc YAGNI (You Ain’t Gonna Need It). Chỉ thiết kế những gì cần cho yêu cầu hiện tại.

### 2. Bỏ qua xử lý lỗi (Error handling)

**Lỗi:** Không thiết kế cách xử lý các trường hợp ngoại lệ như mạng chậm, dữ liệu không hợp lệ.  
**Giải pháp:** Luôn bao gồm try-catch, validation, và fallback strategies trong thiết kế.

### 3. Không tối ưu hiệu năng từ đầu

**Lỗi:** Chọn cấu trúc dữ liệu hoặc thuật toán kém hiệu quả (ví dụ: dùng mảng để tìm kiếm khi cần Map).  
**Giải pháp:** Phân tích độ phức tạp (Big O) trước khi quyết định.

### 4. Thiếu tài liệu cho Internal Layer

**Lỗi:** Chỉ viết code mà không ghi chú cách hoạt động bên trong.  
**Giải pháp:** Viết JSDoc hoặc TypeScript types để mô tả rõ ràng.

### 5. Không tách biệt logic nghiệp vụ khỏi infrastructure

**Lỗi:** Trộn code xử lý database với logic nghiệp vụ trong cùng một hàm.  
**Giải pháp:** Sử dụng Repository Pattern hoặc Service Layer.
# Best Practices cho Detailed Design
1. **Sử dụng TypeScript thay vì JavaScript thuần**: TypeScript giúp định nghĩa interface rõ ràng, giảm lỗi runtime. Tính đến 2026, TypeScript 5.x là tiêu chuẩn.
2. **Áp dụng SOLID principles**: Đặc biệt là Single Responsibility và Dependency Inversion.
3. **Viết unit test song song với thiết kế**: Test-Driven Development (TDD) giúp bạn phát hiện thiếu sót ngay từ đầu.
4. **Tối ưu hóa sau cùng (Premature optimization is the root of all evil)**: Thiết kế sạch trước, sau đó profile và tối ưu khi cần.
5. **Sử dụng design patterns một cách có chọn lọc**: Factory, Observer, Strategy là những pattern phổ biến cho Internal Layer.
6. **Document quyết định thiết kế**: Ghi lại lý do tại sao chọn thuật toán A thay vì B, để người sau hiểu.
7. **Đánh giá hiệu năng định kỳ**: Dùng công cụ như Chrome DevTools hoặc Node.js profiler.
# So sánh các phương pháp thiết kế
|Phương pháp|Ưu điểm|Nhược điểm|Khi nào dùng|
|---|---|---|---|
|OOD (Hướng đối tượng)|Dễ mở rộng, tái sử dụng|Có thể phức tạp, nhiều class|Hệ thống lớn, nhiều module|
|Functional Design|Dễ kiểm thử, ít bug|Khó quản lý trạng thái|Xử lý dữ liệu, pipeline|
|Procedural Design|Đơn giản, dễ hiểu|Khó bảo trì khi lớn|Script nhỏ, prototype|
# Kết luận
Detailed Design là bước không thể thiếu để biến một kiến trúc tổng thể thành code chất lượng. Bằng cách tập trung vào Internal Layer — phần ẩn bên trong mà người dùng không thấy — bạn đảm bảo rằng hệ thống không chỉ hoạt động đúng mà còn bền vững theo thời gian.

**Key Takeaways:**

- Detailed Design trả lời câu hỏi “How” — cách triển khai bên trong mỗi module.
- Internal Layer bao gồm thuật toán, cấu trúc dữ liệu, quản lý trạng thái, và xử lý lỗi.
- Các phương pháp phổ biến: OOD, Functional Design, và BDD.
- Công cụ hỗ trợ: UML diagrams, TypeScript, và unit testing.
- Tránh over-engineering, luôn xử lý lỗi, và ưu tiên đơn giản.
- Best practices: SOLID, TDD, và documentation.

Hãy nhớ rằng, một thiết kế chi tiết tốt sẽ giúp bạn tiết kiệm hàng giờ debug và refactor sau này. Đầu tư thời gian vào Detailed Design là đầu tư vào chất lượng phần mềm.
# References
- [MDN Web Docs: JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide) - Tài liệu chính thức về JavaScript.
- [TypeScript Documentation](https://www.typescriptlang.org/docs/) - Hướng dẫn TypeScript 5.x.
- [Refactoring Guru: Design Patterns](https://refactoring.guru/design-patterns) - Tổng hợp design patterns chi tiết.
- [Stack Overflow: Software Design](https://stackoverflow.com/questions/tagged/software-design) - Câu hỏi và câu trả lời về thiết kế phần mềm.
- [UML Diagrams Guide](https://www.uml-diagrams.org/) - Hướng dẫn sử dụng UML cho thiết kế.
