Chào mừng bạn đến với Bài 15 trong loạt bài về **Detailed Design** (Thiết kế chi tiết). Trong giai đoạn này, chúng ta chuyển từ việc xác định **What** (các tính năng cần xây dựng) sang **How** (cách xây dựng chúng một cách cụ thể). Một trong những phần quan trọng nhất của thiết kế chi tiết là xác định **hợp đồng interface (Interface Contract)** giữa các thành phần, xây dựng **sơ đồ quan hệ thực thể (ERD)** và định nghĩa **schema cùng các ràng buộc dữ liệu**. Đây là những “viên gạch” nền tảng giúp đảm bảo hệ thống hoạt động ổn định, dễ bảo trì và mở rộng.

Tại sao điều này lại quan trọng? Hãy tưởng tượng bạn xây một tòa nhà mà không có bản vẽ chi tiết về cách kết nối các ống nước, dây điện hay các phòng với nhau. Kết quả sẽ là sự hỗn loạn, tốn kém và nguy hiểm. Tương tự, trong phát triển phần mềm, một **Interface Contract** rõ ràng giúp các nhóm phát triển frontend, backend, và các dịch vụ khác nhau có thể làm việc độc lập mà vẫn đảm bảo tích hợp trơn tru. **ERD** và **Schema** giúp chúng ta tổ chức dữ liệu một cách logic, tránh dư thừa và đảm bảo tính toàn vẹn. Bài viết này sẽ trang bị cho bạn kiến thức và kỹ năng cần thiết để thực hiện những công việc này một cách chuyên nghiệp, sử dụng các công cụ và thực hành tốt nhất tính đến năm 2026.

Chúng ta sẽ đi qua các khái niệm từ cơ bản đến nâng cao, bao gồm cách định nghĩa API Contract với OpenAPI 3.1, xây dựng ERD trực quan, thiết kế Schema cơ sở dữ liệu với các ràng buộc, và các mẫu thiết kế phổ biến. Bạn sẽ thấy các ví dụ thực tế với code, sơ đồ ASCII, và bảng so sánh. Hãy sẵn sàng để biến những ý tưởng trừu tượng thành các bản thiết kế cụ thể, có thể triển khai ngay.
# Interface Contract – Hợp đồng giữa các thành phần
### Interface Contract là gì?

**Interface Contract** (Hợp đồng giao diện) là một thỏa thuận chính thức giữa các thành phần trong hệ thống, thường là giữa frontend và backend, hoặc giữa các microservices với nhau. Nó định nghĩa chính xác **các endpoint API**, **kiểu dữ liệu đầu vào/đầu ra**, **mã trạng thái**, và **cách xử lý lỗi**. Một contract tốt giống như một “bản cam kết” rõ ràng, giúp các nhóm phát triển độc lập mà không sợ “đụng hàng”.

Trong thực tế, Interface Contract thường được viết dưới dạng tài liệu OpenAPI (trước đây là Swagger) cho REST API, hoặc schema GraphQL, hoặc file proto cho gRPC. Tính đến năm 2026, OpenAPI 3.1 là tiêu chuẩn phổ biến nhất, hỗ trợ JSON Schema đầy đủ và tích hợp tốt với các công cụ như Postman, Swagger UI, và các framework như Fastify, Express.js.

> **Tip:** Sử dụng **OpenAPI 3.1** thay vì các phiên bản cũ vì nó tương thích với JSON Schema 2020-12, giúp bạn tái sử dụng schema dễ dàng hơn.

### Xây dựng Interface Contract với OpenAPI 3.1

Dưới đây là một ví dụ về Interface Contract cho một API quản lý người dùng (User Management) sử dụng OpenAPI 3.1. Contract này định nghĩa endpoint `POST /users` để tạo người dùng mới.
```yaml
# openapi.yaml (OpenAPI 3.1)
openapi: 3.1.0
info:
  title: User Management API
  version: 1.0.0
  description: API để quản lý thông tin người dùng trong hệ thống
paths:
  /users:
    post:
      summary: Tạo người dùng mới
      operationId: createUser
      tags:
        - Users
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
      responses:
        '201':
          description: Người dùng được tạo thành công
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserResponse'
        '400':
          description: Dữ liệu đầu vào không hợp lệ
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '409':
          description: Email đã tồn tại
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
components:
  schemas:
    CreateUserRequest:
      type: object
      required:
        - name
        - email
        - password
      properties:
        name:
          type: string
          description: Tên đầy đủ của người dùng
          minLength: 2
          maxLength: 100
        email:
          type: string
          format: email
          description: Địa chỉ email hợp lệ
        password:
          type: string
          description: Mật khẩu (tối thiểu 8 ký tự)
          minLength: 8
          maxLength: 128
    UserResponse:
      type: object
      properties:
        id:
          type: integer
          description: ID duy nhất của người dùng
        name:
          type: string
        email:
          type: string
        createdAt:
          type: string
          format: date-time
    ErrorResponse:
      type: object
      properties:
        error:
          type: string
          description: Mô tả lỗi
        code:
          type: integer
          description: Mã lỗi chi tiết
```
**Giải thích code:**

- `openapi: 3.1.0`: Khai báo phiên bản OpenAPI.
- `paths`: Định nghĩa các endpoint API. Ở đây là `POST /users`.
- `requestBody`: Mô tả dữ liệu gửi lên, yêu cầu schema `CreateUserRequest`.
- `responses`: Định nghĩa các phản hồi có thể xảy ra (201 thành công, 400 lỗi validation, 409 xung đột email).
- `components/schemas`: Định nghĩa các schema tái sử dụng, như `CreateUserRequest`, `UserResponse`, `ErrorResponse`.

> **Warning:** Khi định nghĩa contract, hãy chú ý đến các ràng buộc như `minLength`, `maxLength`, `format`. Chúng giúp validation tự động và tránh lỗi không mong muốn.
# Sơ đồ quan hệ thực thể (ERD)
### ERD là gì và tại sao cần nó?

**Sơ đồ quan hệ thực thể (Entity-Relationship Diagram - ERD)** là một công cụ trực quan dùng để mô hình hóa cấu trúc dữ liệu của hệ thống. Nó thể hiện các **thực thể** (entities) như bảng trong cơ sở dữ liệu, các **thuộc tính** (attributes) của chúng, và **mối quan hệ** (relationships) giữa các thực thể. ERD giúp các nhà phát triển, kiến trúc sư, và các bên liên quan hiểu rõ dữ liệu được tổ chức như thế nào trước khi viết code.

Trong thiết kế chi tiết, ERD là bước trung gian giữa phân tích yêu cầu và triển khai cơ sở dữ liệu. Nó giúp phát hiện các vấn đề tiềm ẩn như dư thừa dữ liệu, thiếu ràng buộc, hoặc mối quan hệ không rõ ràng. Tính đến năm 2026, các công cụ như **[dbdiagram.io](http://dbdiagram.io/)**, **Lucidchart**, và **[Draw.io](http://draw.io/)** vẫn rất phổ biến, nhưng các IDE như **DataGrip** hay **VS Code** với extension **ERD Editor** cũng hỗ trợ mạnh mẽ.

### Ví dụ ERD cho hệ thống quản lý đơn hàng

Dưới đây là một ERD đơn giản cho hệ thống quản lý đơn hàng (Order Management). Chúng ta có các thực thể: `User`, `Order`, `Product`, và `OrderItem`.
```sequence
+----------------+       +----------------+       +----------------+
|     User       |       |     Order      |       |    Product     |
+----------------+       +----------------+       +----------------+
| id (PK)        |<------| user_id (FK)   |       | id (PK)        |
| name           |       | id (PK)        |       | name           |
| email (UQ)     |       | status         |       | price          |
| created_at     |       | created_at     |       | stock          |
+----------------+       +----------------+       +----------------+
        |                         |                         |
        |                         |                         |
        |                         |                         |
        |                  +----------------+               |
        |                  |   OrderItem    |               |
        |                  +----------------+               |
        |                  | id (PK)        |               |
        |                  | order_id (FK)  |---------------|
        |                  | product_id (FK)|               |
        |                  | quantity       |               |
        |                  | price          |               |
        +------------------+----------------+---------------+
```
**Giải thích sơ đồ:**

- **User**: Có khóa chính `id`, các thuộc tính `name`, `email` (unique), `created_at`.
- **Order**: Có khóa chính `id`, khóa ngoại `user_id` liên kết đến `User`, các thuộc tính `status`, `created_at`.
- **Product**: Có khóa chính `id`, các thuộc tính `name`, `price`, `stock`.
- **OrderItem**: Là bảng trung gian giữa `Order` và `Product`, có khóa chính `id`, khóa ngoại `order_id` và `product_id`, cùng `quantity` và `price`.

> **Note:** Trong ERD, ký hiệu `(PK)` là Primary Key, `(FK)` là Foreign Key, `(UQ)` là Unique Constraint. Mũi tên thể hiện mối quan hệ một-nhiều (one-to-many).
# Schema và ràng buộc dữ liệu
### Schema cơ sở dữ liệu là gì?

**Schema** là cấu trúc logic của cơ sở dữ liệu, bao gồm các bảng, cột, kiểu dữ liệu, và các ràng buộc. Nó định nghĩa cách dữ liệu được tổ chức và lưu trữ. Trong cơ sở dữ liệu quan hệ, schema thường được viết bằng ngôn ngữ SQL (Data Definition Language - DDL). Tính đến năm 2026, **PostgreSQL 16** và **MySQL 9.0** là những hệ quản trị cơ sở dữ liệu phổ biến, với hỗ trợ mạnh mẽ cho các ràng buộc phức tạp như `CHECK`, `UNIQUE`, `FOREIGN KEY`, và `EXCLUDE`.

**Ràng buộc dữ liệu (Constraints)** là các quy tắc áp dụng lên dữ liệu để đảm bảo tính toàn vẹn. Ví dụ:

- `NOT NULL`: Cột không được để trống.
- `UNIQUE`: Giá trị trong cột phải là duy nhất.
- `PRIMARY KEY`: Kết hợp `NOT NULL` và `UNIQUE`, xác định mỗi hàng duy nhất.
- `FOREIGN KEY`: Đảm bảo giá trị trong cột tham chiếu đến một hàng hợp lệ trong bảng khác.
- `CHECK`: Kiểm tra điều kiện logic (ví dụ: `price > 0`).

### Ví dụ Schema với ràng buộc

Dưới đây là schema cho hệ thống quản lý đơn hàng, được viết bằng SQL cho PostgreSQL 16.
```sql
-- Schema cho hệ thống quản lý đơn hàng (PostgreSQL 16)
-- Bảng User
CREATE TABLE users (
    id SERIAL PRIMARY KEY, -- Tự động tăng, khóa chính
    name VARCHAR(100) NOT NULL, -- Tên không được null, tối đa 100 ký tự
    email VARCHAR(255) NOT NULL UNIQUE, -- Email không null và duy nhất
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW() -- Mặc định là thời gian hiện tại
);

-- Bảng Product
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    price DECIMAL(10, 2) NOT NULL CHECK (price > 0), -- Giá phải lớn hơn 0
    stock INTEGER NOT NULL DEFAULT 0 CHECK (stock >= 0) -- Tồn kho không âm
);

-- Bảng Order
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE, -- Khóa ngoại, xóa user thì xóa order
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'confirmed', 'shipped', 'delivered', 'cancelled')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Bảng OrderItem
CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INTEGER NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
    product_id INTEGER NOT NULL REFERENCES products(id) ON DELETE RESTRICT, -- Không cho xóa product nếu có order item
    quantity INTEGER NOT NULL CHECK (quantity > 0), -- Số lượng phải lớn hơn 0
    price DECIMAL(10, 2) NOT NULL CHECK (price > 0), -- Giá tại thời điểm đặt hàng
    UNIQUE (order_id, product_id) -- Mỗi sản phẩm chỉ xuất hiện một lần trong một đơn hàng
);

-- Tạo index để tăng tốc truy vấn
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
```
**Giải thích code:**

- `SERIAL`: Kiểu tự động tăng, thường dùng cho khóa chính.
- `REFERENCES ... ON DELETE CASCADE`: Khi xóa user, tất cả order của user đó cũng bị xóa.
- `REFERENCES ... ON DELETE RESTRICT`: Không cho xóa product nếu có order item tham chiếu đến.
- `CHECK (price > 0)`: Đảm bảo giá luôn dương.
- `UNIQUE (order_id, product_id)`: Mỗi sản phẩm chỉ xuất hiện một lần trong một đơn hàng.

> **Warning:** Khi sử dụng `ON DELETE CASCADE`, hãy cẩn thận vì nó có thể xóa nhiều dữ liệu không mong muốn. Chỉ dùng khi thực sự cần thiết.
# So sánh các phương pháp thiết kế API
### REST vs GraphQL vs gRPC

Khi thiết kế API, bạn có nhiều lựa chọn. Dưới đây là bảng so sánh ba phương pháp phổ biến tính đến năm 2026:

|Tiêu chí|REST|GraphQL|gRPC|
|---|---|---|---|
|**Giao thức**|HTTP/1.1, HTTP/2|HTTP/1.1, HTTP/2|HTTP/2|
|**Định dạng dữ liệu**|JSON, XML, YAML|JSON|Protobuf (binary)|
|**Hiệu suất**|Trung bình (overhead HTTP)|Tốt (lấy đúng dữ liệu cần)|Rất tốt (binary, streaming)|
|**Khả năng mở rộng**|Dễ dàng với caching|Khó caching hơn|Tốt với multiplexing|
|**Học tập**|Dễ|Trung bình|Khó|
|**Công cụ**|OpenAPI, Postman|Apollo, Relay|protoc, gRPC-web|
|**Trường hợp sử dụng**|CRUD, public API|Ứng dụng phức tạp, mobile|Microservices, real-time|

**Khi nào nên chọn gì?**

- **REST**: Phù hợp cho các API công khai, đơn giản, hoặc khi cần caching dễ dàng.
- **GraphQL**: Tốt cho ứng dụng có nhiều màn hình khác nhau, cần lấy dữ liệu linh hoạt (ví dụ: mobile app).
- **gRPC**: Lý tưởng cho microservices nội bộ, yêu cầu hiệu suất cao và streaming.

> **Tip:** Nếu bạn mới bắt đầu, hãy chọn REST với OpenAPI 3.1. Nó có hệ sinh thái lớn và dễ học. Khi hệ thống phức tạp hơn, bạn có thể chuyển sang GraphQL hoặc gRPC.
# Mẫu thiết kế phổ biến: Repository Pattern
### Repository Pattern là gì?

**Repository Pattern** là một mẫu thiết kế giúp tách biệt logic truy cập dữ liệu khỏi business logic. Nó tạo ra một lớp trung gian (repository) chịu trách nhiệm thao tác với cơ sở dữ liệu, giúp code dễ kiểm thử và bảo trì hơn. Trong thiết kế chi tiết, bạn thường kết hợp Repository Pattern với Interface Contract để định nghĩa rõ ràng các phương thức truy cập dữ liệu.

### Ví dụ Repository Pattern với TypeScript

Dưới đây là ví dụ về Repository Pattern cho thực thể `User`, sử dụng TypeScript và PostgreSQL (với thư viện `pg`).
```ts
// user.repository.ts
import { Pool } from 'pg'; // Thư viện PostgreSQL cho Node.js
import { User } from './user.model'; // Interface định nghĩa kiểu User

// Interface cho Repository (hợp đồng)
export interface IUserRepository {
  create(user: Omit<User, 'id' | 'createdAt'>): Promise<User>;
  findById(id: number): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  update(id: number, data: Partial<User>): Promise<User>;
  delete(id: number): Promise<boolean>;
}

// Triển khai Repository với PostgreSQL
export class UserRepository implements IUserRepository {
  private pool: Pool;

  constructor(pool: Pool) {
    this.pool = pool;
  }

  async create(userData: Omit<User, 'id' | 'createdAt'>): Promise<User> {
    const { name, email, password } = userData;
    // Sử dụng parameterized query để tránh SQL injection
    const query = `
      INSERT INTO users (name, email, password)
      VALUES ($1, $2, $3)
      RETURNING id, name, email, created_at
    `;
    const values = [name, email, password];
    const result = await this.pool.query(query, values);
    return result.rows[0] as User; // Trả về user vừa tạo
  }

  async findById(id: number): Promise<User | null> {
    const query = 'SELECT id, name, email, created_at FROM users WHERE id = $1';
    const result = await this.pool.query(query, [id]);
    return result.rows[0] || null; // Trả về null nếu không tìm thấy
  }

  async findByEmail(email: string): Promise<User | null> {
    const query = 'SELECT id, name, email, created_at FROM users WHERE email = $1';
    const result = await this.pool.query(query, [email]);
    return result.rows[0] || null;
  }

  async update(id: number, data: Partial<User>): Promise<User> {
    // Xây dựng câu lệnh UPDATE động
    const fields = Object.keys(data);
    const setClauses = fields.map((field, index) => `${field} = $${index + 2}`);
    const query = `
      UPDATE users
      SET ${setClauses.join(', ')}
      WHERE id = $1
      RETURNING id, name, email, created_at
    `;
    const values = [id, ...fields.map(field => (data as any)[field])];
    const result = await this.pool.query(query, values);
    return result.rows[0];
  }

  async delete(id: number): Promise<boolean> {
    const query = 'DELETE FROM users WHERE id = $1 RETURNING id';
    const result = await this.pool.query(query, [id]);
    return result.rowCount > 0; // Trả về true nếu xóa thành công
  }
}
```
**Giải thích code:**

- `IUserRepository`: Interface định nghĩa hợp đồng cho repository, giúp dễ dàng thay đổi implementation (ví dụ: từ PostgreSQL sang MongoDB).
- `parameterized query` (`$1`, `$2`…): Ngăn chặn SQL injection, một lỗ hổng bảo mật phổ biến.
- `RETURNING`: Trả về dữ liệu sau khi thực hiện INSERT/UPDATE/DELETE, giúp tránh truy vấn thêm.
- `Partial<User>`: Cho phép cập nhật một phần dữ liệu.

> **Tip:** Kết hợp Repository Pattern với **Dependency Injection** (DI) để dễ dàng mock repository trong kiểm thử. Ví dụ, sử dụng thư viện `tsyringe` hoặc `inversify`.
# Các lỗi thường gặp
1. **Không định nghĩa Interface Contract trước khi code**: Dẫn đến frontend và backend không đồng bộ, gây lỗi tích hợp. **Giải pháp**: Luôn viết contract trước, sử dụng OpenAPI hoặc GraphQL schema.
    
2. **Thiếu ràng buộc dữ liệu trong schema**: Ví dụ, không có `CHECK (price > 0)` dẫn đến giá âm. **Giải pháp**: Thêm tất cả ràng buộc cần thiết ngay từ đầu.
    
3. **Sử dụng `ON DELETE CASCADE` không cẩn thận**: Xóa một user có thể xóa hàng loạt order và order item. **Giải pháp**: Chỉ dùng cascade khi thực sự cần, ưu tiên `RESTRICT` hoặc `SET NULL`.
    
4. **Không tối ưu index**: Dẫn đến truy vấn chậm khi dữ liệu lớn. **Giải pháp**: Tạo index cho các cột thường xuyên xuất hiện trong `WHERE`, `JOIN`, `ORDER BY`.
    
5. **Không kiểm tra lỗi từ database**: Ví dụ, không xử lý lỗi `UNIQUE` violation dẫn đến crash. **Giải pháp**: Luôn bắt lỗi và trả về response phù hợp (ví dụ: mã 409 Conflict).
# Thực hành tốt nhất
1. **Sử dụng OpenAPI 3.1 cho REST API**: Đây là tiêu chuẩn mới nhất, hỗ trợ JSON Schema 2020-12 và tích hợp tốt với các công cụ hiện đại.
    
2. **Thiết kế ERD trước khi viết SQL**: Dùng công cụ như [dbdiagram.io](http://dbdiagram.io/) để trực quan hóa, sau đó sinh code SQL tự động.
    
3. **Thêm ràng buộc ở cả database và application layer**: Database đảm bảo toàn vẹn dữ liệu, application layer cung cấp thông báo lỗi thân thiện hơn.
    
4. **Sử dụng parameterized queries**: Ngăn chặn SQL injection, một trong những lỗ hổng bảo mật nguy hiểm nhất.
    
5. **Tạo index cho các cột thường xuyên truy vấn**: Ví dụ, `user_id` trong bảng `orders`, `email` trong bảng `users`.
    
6. **Viết unit test cho repository**: Sử dụng mock database để kiểm tra logic truy vấn mà không cần kết nối thật.
    
7. **Document API contract tự động**: Dùng Swagger UI hoặc Redoc để tạo tài liệu từ file OpenAPI, giúp các bên liên quan dễ dàng tra cứu.
# Tài liệu tham khảo
- [OpenAPI 3.1 Official Specification](https://spec.openapis.org/oas/v3.1.0) – Tài liệu chính thức về OpenAPI 3.1.
- [PostgreSQL 16 Documentation](https://www.postgresql.org/docs/16/) – Hướng dẫn chi tiết về PostgreSQL 16.
- [dbdiagram.io](https://dbdiagram.io/) – Công cụ vẽ ERD trực tuyến miễn phí.
- [Stack Overflow: Database Design](https://stackoverflow.com/questions/tagged/database-design) – Câu hỏi và câu trả lời về thiết kế cơ sở dữ liệu.
# SUM UP
- **Interface Contract** (hợp đồng giao diện) là thỏa thuận chính thức giữa các thành phần, thường được viết bằng OpenAPI 3.1 hoặc GraphQL schema.
- **ERD** (sơ đồ quan hệ thực thể) giúp trực quan hóa cấu trúc dữ liệu và mối quan hệ giữa các thực thể.
- **Schema và ràng buộc dữ liệu** đảm bảo tính toàn vẹn, bao gồm `PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE`, `CHECK`, và `NOT NULL`.
- **Repository Pattern** tách biệt logic truy cập dữ liệu khỏi business logic, giúp code dễ kiểm thử và bảo trì.
- **Các lỗi thường gặp** bao gồm thiếu contract, thiếu ràng buộc, lạm dụng cascade, và không tối ưu index.
- **Thực hành tốt nhất** bao gồm sử dụng OpenAPI 3.1, parameterized queries, và viết unit test cho repository.

Hy vọng bài viết này đã cung cấp cho bạn một cái nhìn toàn diện về thiết kế API & cơ sở dữ liệu trong giai đoạn Detailed Design. Hãy áp dụng ngay những kiến thức này vào dự án của bạn để xây dựng các hệ thống ổn định, bảo mật và dễ mở rộng. Chúc bạn thành công!