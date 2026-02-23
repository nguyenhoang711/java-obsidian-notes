Xuất hiện trong Java 8
- tiện khi làm việc với data list
- Giảm thiểu code
1. Định nghĩa
Trong Java, Stream là một API được giới thiệu từ phiên bản Java 8 để xử lý dữ liệu theo cách linh hoạt, hiệu quả và dễ đọc.
2. Điểm quan trọng trong Stream java
- **Lười biếng (Lazy Evaluation)**: chỉ thực sự ghi lại kết quả khi cần thiết --> giúp tiết kiệm tài nguyên, cải thiện hiệu suất
- Luồng tuần tự: stream xem như luồng tuần tự các phần tử dữ liệu. Thực hiện các phép biến đổi stream 1 cách tuần tự mà không cần quan tâm bản chất dữ liệu.
- Phương thức trung gian và kết thúc: các phép biến đổi qua phương thức trung gian như filter(), map, sorted, ... Và cuối cùng là phương thức kết thúc như reduce, collect, forEach, để lấy kết quả cuối cùng sau khi thực hiện các phép biến đổi.
- **Không thay đổi dữ liệu gốc**: ko làm thay đổi mà tạo ra dữ liệu mới qua các phép biến đổi.
- **Parallel streams**: cho phép xử lý song song streams trên nhiều luồng, nâng cao hiệu suất.
# Stream API & Cách sử dụng
## Stream API là gì? Tại sao lại ra đời?
Là công cụ xử lý **sequence elements** theo **functional programming** cho phép thao tác dữ liệu **declarative** (khai báo) thay vì **imperative** (mệnh lệnh)

Đặc điểm:
- Không lưu dữ liệu
- Functional style
- Lazy evaluation (chỉ xử lý khi cần)
- Parallel dễ dàng
- Immutable

## 3. Cấu trúc Stream Pipeline
```java
Source -> Intermediate Operations -> Terminal Operation
```

### 1. Source
- Collection: *list.stream()*
- Array: Arrays.stream(array)
- Generator: *Stream.of(1, 2, 3)*
### 2.Intermediate Operation (Lazy - Lười)
- **filter()**: lọc theo điều kiên
- **map()**:biến đổi elements
- **sorted()**: sắp xếp
- **distinct()**: lấy giá trị unique
- **limit()**: giới hạn số lượng
Lazy: chưa thực thi ngay, chỉ định nghĩa pipeline
### 3. Terminal Operation
- **collect()**: thu thập kết quả
- **forEach()**: duyệt từng element
- **count()**: đếm
- **reduce()**: gộp thành 1 giá trị
- **anyMatch(), allMatch()**: kiểm tra điều kiện
**Eager**: kích hoạt thực thi toàn bộ pipeline ngay lập tức
## 4. Eager và Lazy Evaluation
### Lazy (Lười - Intermediate Operations)
Chỉ định nghĩa, chưa thực thi
```java
Stream<String> stream = users.stream()
						.filter(user ->{
							System.out.println("filtering: " + user.getName());
							return user.getAge(); 
						})
						.map(User::getName);
```
### Eager (Háo hức - Terminal Operations)
Kích hoạt thực thi toàn bộ pipeline:
```java
 stream.collect(Collectors.toList());
 // Thực thi và in ra
```

### Vì sao cần Lazy?
Lợi ích
- Hiệu quả: không xử lý thừa, chỉ xử lý khi cần
- Short-circuit: dừng sớm khi đủ kết quả
- **Optimization**: JVM có thể tối ưu hóa pipeline
VD: **short-circuit**
```java
//chỉ tìm 1 user, không cần duyệt hết
Optional<User> user = users.stream()
	.filter(u -> u.getAge() > 18)
	.findFirst(); // dừng ngay khi tìm thấy
```
## 5. Ví dụ thực tế
VD1: lọc và transform
```java
// lấy email của users active
List<String> emails = users.stream()
	.filter(User::isActive) // active = true
	.map(User::getEmail)
	.collect(COllectors.toList());
```
VD2: grouping
```java
// nhóm users theo độ tuổi
Map<Integer, List<User>> userByAge = users.stream()
	.collect(Collectors.groupingBy(User::getAge));
```
VD3: tính toán
```java
// tính tổng lương theo employees
double totalSalary = employees.stream()
	.mapToDouble(Employee::getSalary)
	.sum();
```
VD4: find first
```java
// tìm user đầu tiên > 18
Optional<User> user = users.stream()
		.filter(u -> u.getAge() > 18)
		.findFirst();
```
## 6. Parallel stream
Dễ dàng parallel hóa:
```java

```
Lưu ý sử dụng parallel stream:
- Chỉ sử dụng với data lớn
- Tránh parallel với I/O operations
- **Overhead (chi phí hệ thống)** của thread pool

## 8. Nhược điểm Stream API
- Debug khó: stack trace phức tạp
- Overhead: chậm hơn khi loop với data nhỏ
- One-time use: chỉ dùng 1 lần
## 9. Khi nào nên dùng Stream API
**NÊN dùng khi:**
- Xử lý collections (filter, map, reduce)
- Code ngắn gọn
- Cần parallel processing
**KHÔNG NÊN** khi:
- Data rất nhỏ (< 100 dòng)
- Cần performance tối đa
- Logic phức tạp, side-effects
## 10. So sánh Imperative và Declarative

| Tiêu chí    | Imperative (Loop) | Declarative (Stream) |
| ----------- | ----------------- | -------------------- |
| Style       | HOW (cách làm)    | WHAT (làm gì)        |
| Code        | Dài, phức tạp     | Ngắn, rõ rang        |
| Mutable     | CÓ                | không                |
| Parallel    | Khó               | Dễ                   |
| Performance | Nhanh (data nhỏ)  | Overhead (data nhỏ)  |
## TÓM TẮT
Stream API: Xử lý sequence of elements theo functional style
Tại sao: Giải quyết code imperative dài dòng, khó đọc, khó parallel
Pipeline: Source → Intermediate (lazy) → Terminal (eager)
Ưu điểm: Ngắn gọn, dễ đọc, functional, dễ parallel
Nhược điểm: Learning curve, debug khó, overhead với data nhỏ
Dùng khi: Xử lý collections, cần code rõ ràng, parallel processing

# map() trong Stream API
## 1.Map() là gì ? Cách hoạt động? Khi nào dùng Map
map() là 1 **intermediate operation** trong Stream API, dùng để transform biến đổi mỗi element thành element khác theo 1 function
**Đặc điểm**
- Transform 1 - 1 (1 input - 1 output)
- Không thay đổi số lượng elements
- Lazy evaluation
- Return stream mới
**Signature**
```java
<R> Stream<R> map(Function<? super T, ?  extends R> mapper)
```
## 2. Cách hoạt động
Nguyên lý:
```java
Input: [A, B, C]
		|  |  |
Output:[X,  Y, Z] 
```
Ví dụ đơn giản:
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

List<Integer> doubled =  numbers.stream()
		.map(n -> n * 2)
		.collect(Collectors.toList());
```
## 3. Các use case phổ biến
### Usecase 1: Extract property (lấy thuộc tính)
Lấy tên từ list users
```java
List<User> users = getUsers();

List<String> names = users.stream()
    .map(User::getName)  // User → String
    .collect(Collectors.toList());

// Input:  [User(id=1, name="John"), User(id=2, name="Jane")]
// Output: ["John", "Jane"]
```
### Use case 2: Transform object (Chuyển đổi object)
Convert User -> UserDTO
```java
List<UserDTO> dtos = users.stream()
    .map(user -> new UserDTO(user.getId(), user.getName()))
    .collect(Collectors.toList());

// Hoặc dùng method reference
List<UserDTO> dtos = users.stream()
    .map(UserDTO::from)
    .collect(Collectors.toList());
```
### Use case 3: Calculate (Tính toán)
### Use case 4: String manipulation
## 4. map() với các kiểu dữ liệu
Primitive types: mapToInt, mapToDouble, mapToLong
**Tránh boxing/unboxing overhead:**
```java
// ❌ Không tối ưu (boxing Integer)
Stream<Integer> stream = users.stream()
    .map(User::getAge);

// ✅ Tối ưu (primitive int)
IntStream intStream = users.stream()
    .mapToInt(User::getAge);

// Tính tổng tuổi
int totalAge = users.stream()
    .mapToInt(User::getAge)
    .sum();
```
## 5. Chain nhiều map
```java
List<String> result = users.stream()
    .map(User::getName)           // User → String
    .map(String::toUpperCase)     // String → String (uppercase)
    .map(name -> "Mr. " + name)   // String → String (add prefix)
    .collect(Collectors.toList());

// Input:  [User(name="john"), User(name="jane")]
// Output: ["Mr. JOHN", "Mr. JANE"]
```
## 6. map() với Optional
```java
Optional<User> userOpt = findUser(1);

Optional<String> nameOpt = userOpt.map(User::getName);

// Nếu user exists → Optional<String> with name
// Nếu user null → Optional.empty()
```
## 7. Ưu/ nhược điểm
Ưu:
- Declarative
- Immutable
- Composable
- type-safe: compiler check type
Nhược:
- Không xử lý nest collections
- Overhead: chậm hơn với data nhỏ
- Debug khó: stack trace
## 8. Khi nào nên dùng
NÊN khi
- Transform 1-1
- Extract property
- Convert type
- Tính toán đơn giản
- Không nest collections
KHÔNG NÊN khi
- Cần flatten nest collections --> *flatMap()*
- chỉ filter, không transform --> *filter()*
- Cần side-effect --> *forEach()* hoặc *peek()*
