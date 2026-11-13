Trong giai đoạn **Thiết kế chi tiết (Detailed Design)** của quy trình phát triển phần mềm, việc mô hình hóa logic xử lý là bước then chốt để biến các yêu cầu nghiệp vụ trừu tượng thành các thành phần cụ thể, có thể triển khai. Hai công cụ mạnh mẽ nhất mà chúng ta sử dụng ở giai đoạn này là **Class Diagram** (biểu đồ lớp) và **Sequence Diagram** (biểu đồ tuần tự). Class Diagram giúp định nghĩa cấu trúc tĩnh của hệ thống — các lớp, thuộc tính, phương thức và mối quan hệ giữa chúng. Sequence Diagram lại tập trung vào hành vi động — mô tả chi tiết luồng tương tác giữa các đối tượng theo thời gian.

Tại sao điều này lại quan trọng? Hãy tưởng tượng bạn đang xây một ngôi nhà. Nếu chỉ có bản vẽ kiến trúc tổng thể (tương tự Use Case Diagram), bạn sẽ không thể biết được dây điện chạy thế nào, ống nước đi đâu. Class Diagram và Sequence Diagram chính là bản vẽ chi tiết cho từng căn phòng, từng hệ thống kỹ thuật. Chúng giúp đội ngũ phát triển hiểu rõ **ai làm gì, khi nào và như thế nào**, giảm thiểu sai sót khi code và tăng khả năng bảo trì về sau. Đến năm 2026, với sự phổ biến của kiến trúc microservices và các framework hiện đại như Spring Boot 3.x, Django 5.x, hay [ASP.NET](http://asp.net/) Core 9, việc nắm vững hai loại biểu đồ này càng trở nên thiết yếu.

Trong bài học này, chúng ta sẽ đi sâu vào cách xây dựng Class Diagram và Sequence Diagram cho một luồng nghiệp vụ điển hình: **Xử lý đơn hàng trong hệ thống thương mại điện tử**. Bạn sẽ học được cách chuyển đổi từ phân tích nghiệp vụ sang thiết kế kỹ thuật, cùng với các ví dụ code mẫu minh họa.
# 1. Class Diagram: Xương sống của hệ thống
### 1.1. Định nghĩa và vai trò

**Class Diagram** là một biểu đồ cấu trúc trong UML, dùng để mô hình hóa các lớp (class) của hệ thống, cùng với các thuộc tính (attributes), phương thức (methods), và mối quan hệ (relationships) giữa chúng. Nó cung cấp một cái nhìn tĩnh về hệ thống, giống như bản vẽ kỹ thuật của một tòa nhà.

Vai trò chính của Class Diagram trong giai đoạn thiết kế chi tiết:

- **Xác định trách nhiệm:** Mỗi class đại diện cho một thực thể hoặc một khái niệm trong hệ thống, với các trách nhiệm rõ ràng.
- **Định nghĩa giao diện:** Các phương thức public của class chính là API mà các class khác có thể gọi.
- **Phát hiện sự phụ thuộc:** Các mối quan hệ như association, dependency, inheritance cho thấy class nào phụ thuộc vào class nào.
- **Tái sử dụng và bảo trì:** Một thiết kế tốt giúp dễ dàng thêm tính năng mới mà không phá vỡ code hiện có.

### 1.2. Các thành phần chính

Một class trong UML được biểu diễn bằng một hình chữ nhật chia làm 3 phần:
```uml
+---------------------------+
|       Order               |  <- Tên class
+---------------------------+
| - orderId: string         |  <- Thuộc tính (private)
| - items: OrderItem[]      |
| - totalAmount: number     |
| - status: OrderStatus     |
+---------------------------+
| + addItem(item): void     |  <- Phương thức (public)
| + calculateTotal(): void  |
| + processPayment(): bool  |
+---------------------------+
```
**Giải thích ký hiệu:**

- `-` (dấu trừ): private — chỉ class đó mới truy cập được.
- `+` (dấu cộng): public — ai cũng có thể truy cập.
- `#` (dấu thăng): protected — class con có thể truy cập.
- Kiểu dữ liệu được ghi sau dấu hai chấm (`:`).

### 1.3. Các mối quan hệ cơ bản

|Quan hệ|Ký hiệu|Ý nghĩa|Ví dụ|
|---|---|---|---|
|**Association**|Đường thẳng|Quan hệ cấu trúc giữa hai class|Order có một Customer|
|**Aggregation**|Hình thoi rỗng|Quan hệ “có” (has-a) yếu|Department có Employee|
|**Composition**|Hình thoi đặc|Quan hệ “có” mạnh (phần-tử)|Order có OrderItem|
|**Inheritance**|Mũi tên tam giác|Quan hệ “là” (is-a)|PayPalPayment là Payment|
|**Dependency**|Mũi tên đứt|Quan hệ tạm thời|OrderService phụ thuộc vào EmailService|

### 1.4. Ví dụ Class Diagram cho hệ thống đặt hàng

Dưới đây là Class Diagram cho luồng nghiệp vụ “Đặt hàng” trong hệ thống thương mại điện tử:
```uml
+-------------------+       +-------------------+
|     Customer      |       |       Order       |
+-------------------+       +-------------------+
| - customerId: int |       | - orderId: int    |
| - name: string    |       | - orderDate: Date |
| - email: string   |       | - status: string  |
| - address: string |       | - total: double   |
+-------------------+       +-------------------+
| + getOrders(): [] |<>---->| + addItem()       |
| + createOrder()   |       | + calculateTotal()|
+-------------------+       | + processPayment()|
                            | + ship()          |
                            +-------------------+
                                     |
                                     | 1
                                     |
                            +-------------------+
                            |    OrderItem      |
                            +-------------------+
                            | - itemId: int     |
                            | - product: Product|
                            | - quantity: int   |
                            | - price: double   |
                            +-------------------+
                            | + getSubtotal()   |
                            +-------------------+
```
**Giải thích:**

- Một **Customer** có thể có nhiều **Order** (quan hệ một-nhiều).
- Một **Order** có nhiều **OrderItem** (composition — nếu Order bị xóa, các OrderItem cũng bị xóa).
- **OrderItem** tham chiếu đến **Product** (association).
# 2. Sequence Diagram: Kịch bản của các tương tác
### 2.1. Định nghĩa và mục đích

**Sequence Diagram** là một biểu đồ hành vi trong UML, mô tả sự tương tác giữa các đối tượng theo thời gian. Nó tập trung vào **thứ tự** các message được gửi giữa các đối tượng để thực hiện một chức năng cụ thể.

Khác với Class Diagram (tĩnh), Sequence Diagram trả lời câu hỏi **“Làm thế nào để các class này phối hợp với nhau để hoàn thành một use case?”**. Nó đặc biệt hữu ích khi:

- Thiết kế luồng nghiệp vụ phức tạp.
- Xác định các bước xử lý và thứ tự thực hiện.
- Phát hiện các điểm có thể xảy ra lỗi hoặc cần đồng bộ.
- Giao tiếp giữa các thành viên trong nhóm.

### 2.2. Các thành phần của Sequence Diagram

Một Sequence Diagram điển hình bao gồm:

1. **Lifeline (đường sinh tồn):** Đường thẳng đứng đại diện cho một đối tượng hoặc actor. Ký hiệu: `[Actor/Class Name]` với một đường đứt nét kéo dài xuống dưới.
2. **Activation bar (thanh kích hoạt):** Hình chữ nhật hẹp trên lifeline, cho biết thời điểm đối tượng đang thực hiện một hành động.
3. **Message (thông điệp):** Mũi tên giữa các lifeline, biểu diễn việc gửi một message (gọi phương thức, gửi sự kiện, etc.).
4. **Self-message:** Mũi tên quay lại chính nó, biểu diễn việc gọi phương thức nội bộ.
5. **Return message:** Mũi tên đứt nét, biểu diễn giá trị trả về (thường được bỏ qua nếu không cần thiết).
6. **Combined fragment:** Hình chữ nhật có góc bo tròn, dùng để biểu diễn các cấu trúc điều khiển như `alt` (if-else), `loop` (vòng lặp), `opt` (tùy chọn).

### 2.3. Ví dụ Sequence Diagram: Xử lý đơn hàng

Giả sử use case “Đặt hàng” có luồng chính như sau:

1. Khách hàng (Customer) gửi yêu cầu tạo đơn hàng.
2. Hệ thống kiểm tra thông tin sản phẩm (ProductService).
3. Hệ thống tính tổng tiền (OrderService).
4. Hệ thống xử lý thanh toán (PaymentService).
5. Hệ thống gửi email xác nhận (NotificationService).
6. Hệ thống trả về kết quả cho khách hàng.

Sequence Diagram tương ứng (biểu diễn bằng text):
```uml
Customer    OrderController    OrderService    ProductService   PaymentService   NotificationService
    |              |                 |                |                |                    |
    |--(1) POST---->|                 |                |                |                    |
    |  /orders      |                 |                |                |                    |
    |              |--(2) createOrder|                |                |                    |
    |              |   (customerId,  |                |                |                    |
    |              |    productIds)  |                |                |                    |
    |              |                 |--(3) validate--|                |                    |
    |              |                 |    Products    |                |                    |
    |              |                 |<---------------|                |                    |
    |              |                 |--(4) calculate |                |                    |
    |              |                 |    Total       |                |                    |
    |              |                 |                |                |                    |
    |              |                 |--(5) process---|                |                    |
    |              |                 |    Payment     |                |                    |
    |              |                 |<---------------|                |                    |
    |              |                 |--(6) send------|                |                    |
    |              |                 |    Confirmation|                |                    |
    |              |                 |                |                |<-------------------|
    |              |<----------------|                |                |                    |
    |<--(7) 201----|                 |                |                |                    |
    |   Created    |                 |                |                |                    |
```
**Giải thích từng bước:**

1. Customer gửi HTTP POST request đến `OrderController`.
2. `OrderController` gọi phương thức `createOrder` của `OrderService`.
3. `OrderService` gọi `ProductService` để kiểm tra sản phẩm còn hàng không.
4. `OrderService` tính tổng tiền dựa trên số lượng và giá.
5. `OrderService` gọi `PaymentService` để xử lý thanh toán.
6. `OrderService` gọi `NotificationService` để gửi email xác nhận.
7. `OrderController` trả về HTTP 201 Created cho Customer.

> **Tip:** Trong thực tế, bạn nên vẽ Sequence Diagram bằng các công cụ như PlantUML, Lucidchart, hoặc [Draw.io](http://draw.io/). Các công cụ này hỗ trợ kéo thả và xuất hình ảnh chất lượng cao.
# 3. Chuyển đổi từ Class Diagram sang Code
### 3.1. Nguyên tắc chuyển đổi

Một Class Diagram tốt có thể được chuyển đổi trực tiếp sang code. Dưới đây là các bước cơ bản:

1. **Xác định các class:** Mỗi class trong diagram trở thành một class trong code.
2. **Định nghĩa thuộc tính:** Các thuộc tính trở thành biến instance (thường là private).
3. **Định nghĩa phương thức:** Các phương thức trở thành function/method.
4. **Xử lý quan hệ:** Association, aggregation, composition trở thành thuộc tính tham chiếu.
5. **Xử lý kế thừa:** Sử dụng `extends` (Java, TypeScript) hoặc `inherits` (Python).

### 3.2. Ví dụ code JavaScript (Node.js)

Dưới đây là ví dụ chuyển đổi Class Diagram ở trên thành code JavaScript (sử dụng ES6 classes):
```js
// === Class: Product ===
class Product {
  // Private fields (ES2022)
  #productId;
  #name;
  #price;
  #stockQuantity;

  constructor(productId, name, price, stockQuantity) {
    this.#productId = productId;
    this.#name = name;
    this.#price = price;
    this.#stockQuantity = stockQuantity;
  }

  // Getter methods
  getProductId() { return this.#productId; }
  getName() { return this.#name; }
  getPrice() { return this.#price; }
  getStockQuantity() { return this.#stockQuantity; }

  // Business method
  isInStock(quantity) {
    return this.#stockQuantity >= quantity;
  }

  reduceStock(quantity) {
    if (!this.isInStock(quantity)) {
      throw new Error('Insufficient stock');
    }
    this.#stockQuantity -= quantity;
  }
}

// === Class: OrderItem ===
class OrderItem {
  #product;
  #quantity;
  #price; // Giá tại thời điểm đặt hàng

  constructor(product, quantity) {
    this.#product = product;
    this.#quantity = quantity;
    this.#price = product.getPrice(); // Lưu giá hiện tại
  }

  getSubtotal() {
    return this.#price * this.#quantity;
  }

  getProduct() { return this.#product; }
  getQuantity() { return this.#quantity; }
}

// === Class: Order ===
class Order {
  #orderId;
  #customerId;
  #orderDate;
  #items; // Mảng các OrderItem
  #status; // 'pending', 'confirmed', 'shipped', 'delivered', 'cancelled'
  #total;

  constructor(orderId, customerId) {
    this.#orderId = orderId;
    this.#customerId = customerId;
    this.#orderDate = new Date();
    this.#items = [];
    this.#status = 'pending';
    this.#total = 0;
  }

  addItem(product, quantity) {
    const item = new OrderItem(product, quantity);
    this.#items.push(item);
    this.#total += item.getSubtotal();
  }

  calculateTotal() {
    this.#total = this.#items.reduce((sum, item) => sum + item.getSubtotal(), 0);
    return this.#total;
  }

  getOrderId() { return this.#orderId; }
  getStatus() { return this.#status; }
  setStatus(status) { this.#status = status; }
}

// === Class: OrderService (xử lý nghiệp vụ) ===
class OrderService {
  #productService;
  #paymentService;
  #notificationService;

  constructor(productService, paymentService, notificationService) {
    this.#productService = productService;
    this.#paymentService = paymentService;
    this.#notificationService = notificationService;
  }

  async createOrder(customerId, productIds, quantities) {
    // Bước 1: Kiểm tra sản phẩm
    const products = [];
    for (let i = 0; i < productIds.length; i++) {
      const product = await this.#productService.getProduct(productIds[i]);
      if (!product || !product.isInStock(quantities[i])) {
        throw new Error(`Product ${productIds[i]} is out of stock`);
      }
      products.push(product);
    }

    // Bước 2: Tạo đơn hàng
    const order = new Order(Date.now(), customerId);
    for (let i = 0; i < products.length; i++) {
      order.addItem(products[i], quantities[i]);
    }
    order.calculateTotal();

    // Bước 3: Xử lý thanh toán
    const paymentResult = await this.#paymentService.processPayment(customerId, order.getOrderId(), order.calculateTotal());
    if (!paymentResult.success) {
      throw new Error('Payment failed');
    }
    order.setStatus('confirmed');

    // Bước 4: Gửi thông báo
    await this.#notificationService.sendOrderConfirmation(customerId, order);

    return order;
  }
}
```
**Giải thích code:**

- Sử dụng private fields (`#`) để đảm bảo tính đóng gói (encapsulation).
- `OrderService` là nơi chứa logic nghiệp vụ chính, phối hợp các service khác.
- Các phương thức `async/await` xử lý bất đồng bộ (gọi API, database, etc.).
# 4. Tích hợp Sequence Diagram vào thiết kế
### 4.1. Từ Sequence Diagram đến mã nguồn

Sequence Diagram không chỉ là bản vẽ, nó là bản thiết kế chi tiết cho luồng xử lý. Khi chuyển đổi sang code, bạn cần:

1. **Xác định các actor/object:** Mỗi lifeline là một class hoặc module.
2. **Xác định các message:** Mỗi message là một lời gọi phương thức hoặc gửi sự kiện.
3. **Xác định thứ tự:** Thứ tự các message quyết định luồng code.
4. **Xử lý các combined fragment:** `alt` → if-else, `loop` → for/while, `opt` → if.

### 4.2. Ví dụ code từ Sequence Diagram

Dưới đây là code minh họa cho Sequence Diagram xử lý đơn hàng ở trên, sử dụng Express.js (Node.js) cho controller:
```js
// === Controller: OrderController (Express.js route handler) ===
const express = require('express');
const router = express.Router();

// Giả sử các service đã được inject qua dependency injection
const orderService = new OrderService(
  new ProductService(),
  new PaymentService(),
  new NotificationService()
);

// POST /api/orders — Tạo đơn hàng mới (tương ứng bước 1 trong Sequence Diagram)
router.post('/orders', async (req, res) => {
  try {
    // Lấy dữ liệu từ request body
    const { customerId, productIds, quantities } = req.body;

    // Validation cơ bản
    if (!customerId || !productIds || !quantities) {
      return res.status(400).json({ error: 'Missing required fields' });
    }
    if (productIds.length !== quantities.length) {
      return res.status(400).json({ error: 'productIds and quantities must have same length' });
    }

    // Gọi OrderService để xử lý nghiệp vụ (bước 2-6 trong Sequence Diagram)
    const order = await orderService.createOrder(customerId, productIds, quantities);

    // Trả về kết quả thành công (bước 7)
    return res.status(201).json({
      message: 'Order created successfully',
      orderId: order.getOrderId(),
      status: order.getStatus(),
      total: order.calculateTotal()
    });

  } catch (error) {
    // Xử lý lỗi — có thể log và trả về lỗi phù hợp
    console.error('Error creating order:', error.message);
    return res.status(500).json({ error: error.message || 'Internal server error' });
  }
});

module.exports = router;
```
**Giải thích:**

- Controller nhận request, validation dữ liệu đầu vào.
- Gọi `OrderService.createOrder` — đây là nơi thực hiện toàn bộ luồng xử lý.
- Trả về HTTP status code phù hợp (201 cho thành công, 400/500 cho lỗi).
- Cấu trúc này hoàn toàn khớp với Sequence Diagram đã vẽ.
# 5. So sánh các phương pháp thiết kế
### 5.1. Top-down vs Bottom-up

|Phương pháp|Mô tả|Ưu điểm|Nhược điểm|
|---|---|---|---|
|**Top-down**|Bắt đầu từ use case, vẽ Sequence Diagram trước, sau đó suy ra Class Diagram|Dễ hiểu, tập trung vào nghiệp vụ|Có thể bỏ sót chi tiết kỹ thuật|
|**Bottom-up**|Bắt đầu từ Class Diagram, xác định các thực thể, sau đó vẽ Sequence Diagram|Chi tiết, chính xác|Có thể quá tập trung vào cấu trúc|

> **Tip:** Trong thực tế, bạn nên kết hợp cả hai. Bắt đầu với một Class Diagram sơ bộ, vẽ Sequence Diagram cho luồng chính, sau đó tinh chỉnh Class Diagram dựa trên những gì học được từ Sequence Diagram.

### 5.2. Công cụ hỗ trợ vẽ diagram (2026)

- **PlantUML** (miễn phí, dùng text-based): Tích hợp tốt với IDE và CI/CD.
- **Lucidchart** (có phí, giao diện kéo thả): Dễ sử dụng, hỗ trợ cộng tác.
- **[Draw.io](http://draw.io/) / [diagrams.net](http://diagrams.net/)** (miễn phí): Tích hợp với Google Drive, GitHub.
- **Mermaid.js** (miễn phí, dùng markdown): Tích hợp với GitHub, GitLab, Notion.
- **StarUML** (miễn phí, desktop): Mạnh mẽ, hỗ trợ nhiều loại UML.
# 6. Các lỗi thường gặp
### 6.1. Class Diagram quá chi tiết hoặc quá trừu tượng

- **Lỗi:** Vẽ quá nhiều class (hàng trăm class) hoặc chỉ vẽ 2-3 class.
- **Giải pháp:** Tập trung vào các class cốt lõi cho luồng nghiệp vụ đang thiết kế. Sử dụng package diagram để nhóm các class liên quan.

### 6.2. Sequence Diagram thiếu các luồng ngoại lệ

- **Lỗi:** Chỉ vẽ luồng chính (happy path), bỏ qua các trường hợp lỗi.
- **Giải pháp:** Thêm các combined fragment `alt` cho các tình huống lỗi (ví dụ: thanh toán thất bại, sản phẩm hết hàng).

### 6.3. Không đồng bộ giữa Class Diagram và Sequence Diagram

- **Lỗi:** Class Diagram có 5 class nhưng Sequence Diagram lại sử dụng 7 class.
- **Giải pháp:** Luôn cập nhật cả hai diagram đồng thời. Dùng version control cho diagram (ví dụ: PlantUML file trong Git).

### 6.4. Bỏ qua tính bất đồng bộ (async)

- **Lỗi:** Vẽ các message như đồng bộ (mũi tên đặc) nhưng thực tế là bất đồng bộ (gọi API, database).
- **Giải pháp:** Sử dụng ký hiệu mũi tên khác cho async message, và thêm các return message khi cần.

> **Warning:** Trong hệ thống microservices, hầu hết các tương tác là bất đồng bộ. Hãy chắc chắn diagram của bạn phản ánh đúng điều này.
# 7. Best Practices
1. **Bắt đầu từ use case:** Luôn vẽ Sequence Diagram cho từng use case quan trọng trước khi vẽ Class Diagram chi tiết.
2. **Sử dụng naming convention nhất quán:** Đặt tên class, phương thức, thuộc tính theo chuẩn của ngôn ngữ (camelCase cho JavaScript, PascalCase cho C#, snake_case cho Python).
3. **Giữ diagram đơn giản:** Mỗi diagram chỉ nên tập trung vào một luồng nghiệp vụ. Nếu quá phức tạp, hãy chia nhỏ.
4. **Sử dụng dependency injection:** Trong Class Diagram, thể hiện rõ các dependency qua constructor injection (giúp dễ test và bảo trì).
5. **Document các quyết định thiết kế:** Ghi chú bên cạnh diagram lý do tại sao bạn chọn cách này mà không phải cách khác.
6. **Review diagram với team:** Tổ chức các buổi review diagram (giống code review) để phát hiện sớm các vấn đề.
7. **Tích hợp diagram vào CI/CD:** Sử dụng PlantUML hoặc Mermaid để tự động tạo diagram từ code, đảm bảo diagram luôn đồng bộ với code.
# 8. Tài liệu tham khảo
- [UML 2.5.1 Specification (OMG)](https://www.omg.org/spec/UML/2.5.1/About-UML/) — Tài liệu chính thức về UML.
- [PlantUML Official Documentation](https://plantuml.com/sequence-diagram) — Hướng dẫn vẽ Sequence Diagram bằng PlantUML.
- [Mermaid.js Documentation](https://mermaid.js.org/syntax/sequenceDiagram.html) — Vẽ Sequence Diagram bằng Markdown.
- [Stack Overflow: UML Tag](https://stackoverflow.com/questions/tagged/uml) — Hỏi đáp về UML.
# 9. Tổng kết
- **Class Diagram** là công cụ mô hình hóa cấu trúc tĩnh của hệ thống, bao gồm các lớp, thuộc tính, phương thức và mối quan hệ.
- **Sequence Diagram** mô hình hóa hành vi động, mô tả thứ tự tương tác giữa các đối tượng để thực hiện một use case.
- Cả hai diagram bổ sung cho nhau: Class Diagram trả lời **“có những class nào”**, Sequence Diagram trả lời **“chúng tương tác với nhau như thế nào”**.
- Khi chuyển đổi sang code, hãy đảm bảo mỗi class trong diagram có một class tương ứng trong code, và mỗi message trong Sequence Diagram là một lời gọi phương thức.
- Luôn xem xét các luồng ngoại lệ (lỗi, timeout, etc.) trong Sequence Diagram để code của bạn robust hơn.
- Sử dụng các công cụ hỗ trợ như PlantUML, Mermaid, hoặc Lucidchart để vẽ diagram nhanh chóng và dễ dàng chia sẻ với team.
- **Best practice:** Bắt đầu với Sequence Diagram cho use case chính, từ đó suy ra Class Diagram, sau đó tinh chỉnh cả hai qua nhiều vòng lặp.

> **Note:** Đến năm 2026, việc sử dụng diagram trong quy trình phát triển phần mềm đã trở nên phổ biến hơn bao giờ hết, đặc biệt với các phương pháp Agile và DevOps. Hãy coi diagram như một công cụ giao tiếp, không phải là một tài liệu cứng nhắc.
