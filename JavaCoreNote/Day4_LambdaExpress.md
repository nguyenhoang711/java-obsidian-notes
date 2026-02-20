Cách code với lambda
- hay sử dụng trong Spring Security
# Functional Interface trong Java
Functional interface là gì?
Là interface chỉ có đúng **1 abstract method**. DÙng làm target type cho **lambda expressions** và **method references**.
Đặc điểm:
- Chỉ có 1 abstract method
- Có thể có nhiều default methods
- Có thể có nhiều static methods
- Annotation @FunctionalInteface là optional
- Nền tảng của lambda expressions

# Lambda expressions
## Built-ins functional inteface phổ biến
Cung cấp sẵn trong pkg *java.util.function*
### Function<T, R> - Transform
Nhập input T, return output R:
```java
@FunctionalInterface
interface Function<T, R> {
    R apply(T t);
}

```
Ví dụ:
```java
// String → Integer (length)
Function<String, Integer> length = str -> str.length();
int len = length.apply("Hello");  // 5

// Integer → String
Function<Integer, String> toString = num -> "Number: " + num;
String result = toString.apply(42);  // "Number: 42"

// Dùng trong Stream
List<String> names = Arrays.asList("John", "Jane", "Bob");
List<Integer> lengths = names.stream()
    .map(String::length)  // Function<String, Integer>
    .collect(Collectors.toList());
```
### Predicate - Test điều kiện
Nhập input T, return boolean
```java
@FunctionalInterface
interface Predicate<T> {
    boolean test(T t);
}
```
Consumer - Thực thi action
Nhập input, ko return gì
```java
@FunctionalInterface
interface Consumer<T> {
    void accept(T t);
}
```

Ví dụ:
```java
// In ra console
Consumer<String> print = str -> System.out.println(str);
print.accept("Hello");  // In: Hello

// Update object
Consumer<User> activate = user -> user.setActive(true);
activate.accept(user);

// Dùng trong Stream
List<String> names = Arrays.asList("John", "Jane", "Bob");
names.stream()
    .forEach(name -> System.out.println(name));  // Consumer<String>
```
### Supplier - Cung cấp giá trị
Không nhận input - return giá trị T
```java
@FunctionalInterface
interface Supplier<T> {
    T get();
}
```
Ví dụ (dùng trong Optional)
```java
// Cung cấp giá trị mặc định
Supplier<String> defaultName = () -> "Unknown";
String name = defaultName.get();  // "Unknown"

// Lazy initialization
Supplier<List<String>> listSupplier = () -> new ArrayList<>();
List<String> list = listSupplier.get();

// Dùng trong Optional
Optional<String> opt = Optional.empty();
String value = opt.orElseGet(() -> "Default");  // Supplier<String>
```
## Method reference
Functional inteface cho phép method reference
```java
Function<String, Integer> length = str -> str.length();

// method reference
Function<String, Integer> length = String::length;

// static method
Function<String, Integer> parseInt = Integer::parseInt;

```
## Ưu điểm của Functional Interface
- **Lambda support**: nền tảng cho lambda expressions
- **Concise**: code ngắn gọn hơn anonymous class
- Functional programming
- **Reusable**
- **Stream API**: nền tảng cho stream operations
## Khi nào dùng

Nên dùng khi:
- Cần lambda expressions
- Callback functions
- Stream operations
- Event handlers
- Strategy pattern
Built-in vs custom:
- Dùng built-in khi có thể
- Tạo custom khi logic đặc biệt hoặc tên có ý nghĩa đặt biệt

# Tóm tắt
- **Functional Interface**: Interface với 1 abstract method duy nhất
- **Mục đích**: Target type cho lambda expressions
- **Built-in**: Function (transform), Predicate (test), Consumer (action), Supplier (provide)
- Bi-variants: BiFunction, BiPredicate, BiConsumer (2 inputs)
- Primitive: IntFunction, IntPredicate, IntConsumer (tránh boxing)
- **Operators**: UnaryOperator (T→T), BinaryOperator (T,T→T)
- @**FunctionalInterface**: Optional nhưng nên dùng
# Lambda Expression
## Lambda Expression là gì? Cú pháp? Tại sao ra đời? Khi nào dùng?
Là cách viết ngắn gọn của **anonymous function** dùng để implement functional interface
Đặc điểm:
- Không tên
- Ngắn gọn hơn anonymous class
- FUnctional programming style
- Có thể truyền như parameter
- Có thể lưu trong biến
```java
(parameters) -> expression
(parameters) -> { statements; }

```
## Lí do ra đời
Anonymous class dài dòng
Với lambda: ngắn gọn, dễ đọc
```java
// Java 8+ - Lambda
Runnable runnable = () -> System.out.println("Hello");

// Comparator
Collections.sort(users, (u1, u2) -> u1.getAge() - u2.getAge());

```
## Cú pháp cơ bản
```java
// không có params

() -> expression
() -> { statements;}

// 1 param
param -> expression
param -> {statements;}
```
### 3.1 Quy tắc về {} và return
#### Quy tắc 1: Expression không cần {} và return
Single expression tự động return
```java
// không cần {} và return
Function<Integer, Integer> square = num -> num * num;
```
#### Quy tắc 2: Statement - Cần {} và Return
Nhiều statements hoặc có logic
```java
// cần {} và return
FUnction<Integer, Integer> calculate = num -> {
	int temp = num * 2;
	int result = temp + 10;
	return result;
}
```

#### Quy tắc 3: Void - Không cần Return
Consumer không return
```java
Consumer<String> pritn = str -> System.out.println(str);

// NHiều statements
Consumer<String> process = str -> {
	System.out.println("Processing: " + str);
	System.out.println("Done");
}
```
#### Quy tắc 4: Return trong block - Bắt buộc return
Nếu dùng {}, phải có return (trừ void):
```java
// ❌ Lỗi: Thiếu return
Function<Integer, Integer> square = num -> {
    num * num;  // Lỗi!
};

// ✅ Đúng: Có return
Function<Integer, Integer> square = num -> {
    return num * num;
};

// ✅ Hoặc bỏ {}
Function<Integer, Integer> square = num -> num * num;

```

### Quy tắc về type declaration
#### Quy tắc 1: type inference - Không cần khai báo
```java
// ✅ Compiler tự suy ra type
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
```
#### Quy tắc 2: khai báo type - Phải khai báo tất cả
```java
// ✅ Khai báo tất cả
BiFunction<Integer, Integer, Integer> add = (Integer a, Integer b) -> a + b;

// ❌ Lỗi: Khai báo một phần
BiFunction<Integer, Integer, Integer> add = (Integer a, b) -> a + b;  // Lỗi!

// ❌ Lỗi: Khai báo type phải có ()
Function<String, Integer> length = String str -> str.length();  // Lỗi!

// ✅ Đúng
Function<String, Integer> length = (String str) -> str.length();
```
### Tổng hợp các quy tắc
Bảng tổng hợp

| Trường hợp       | Cú pháp                | Ví dụ                         |
| ---------------- | ---------------------- | ----------------------------- |
| 0 params         | () -> expr             | () -> "Hello"                 |
| 1 param, no type | x -> expr              | str -> str.length()           |
| 2+ params        | (x, y) -> expr         | (a, b) -> a + b               |
| Expression       | Không cần {} và Return | x -> x + 2                    |
| Statement        | Cần {} và return       | x -> { return x + 2;}         |
| Void             | Không cần return       | x -> System.out.println(x)    |
| 1 param, có type | (Type x ) -> expre     | (String str) -> str.length(); |
### Type Inference
Compiler tự suy ra type
```java
// Không cần khai báo type
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;

// Có thể khai báo type (optional)
BiFunction<Integer, Integer, Integer> add = (Integer a, Integer b) -> a + b;

// Nếu khai báo type, phải khai báo tất cả
BiFunction<Integer, Integer, Integer> add = (Integer a, b) -> a + b;  // ❌ Error
```
### 5. Lambda với functional interface
```java
@FunctionalInterface
interface Calculator{
	int calculate(int a, int b);
}

Calculator add = (a, b) -> a + b;
Calculator subtract = (a, b) -> a - b;

int result = add.calculate(3, 5);
```
### 6.Lambda trong collections
```java
List<String> names = Arrays.asList("John", "Jane", "Bob");

// lambda
names.forEach(name -> System.out.println(name));

// method reference
names.forEach(System.out::println);
```

List.removeIf()
```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1,2,3,4,5));

// xoa so chan
numbers.removeIf(num -> num % 2 == 0);
```
### 7. Lambda trong Stream API
```java
List<User> users = getUsers();

// filter - Predicate
List<User> adults = users.stream()
    .filter(user -> user.getAge() >= 18)
    .collect(Collectors.toList());

// map - Function
List<String> names = users.stream()
    .map(user -> user.getName())
    .collect(Collectors.toList());

// reduce - BinaryOperator
int sum = numbers.stream()
    .reduce(0, (a, b) -> a + b);

// sorted - Comparator
List<User> sorted = users.stream()
    .sorted((u1, u2) -> u1.getAge() - u2.getAge())
    .collect(Collectors.toList());
```
### 8. Capturing variables
#### Effectively final
Lambda expressions có thể lấy các giá trị từ surrounding scope, nhưng với 1 điều kiện: là biến **local** và **effectively final**.
*Effectively final Rule:* giá trị không được thay đổi sau khi khởi tạo trong khi nó không đánh dấu rõ ràng là biến *final*.
Nếu bạn cố tình modify biến local variable trong lambda hoặc elsewhere sau khi nó được assign --> *compilation error*.
#### Workaround for Mutable Local Variables
Nếu bạn cần mutable value shared giữa hàm lambda và enclosing scope, cách cơ bản và dễ nhất là sử dụng **single-element array** hoặc **atomic** class (*AtomicInteger*)

#### Instance variables
Có thể access và modify instance variables
```java
class Counter {
    private int count = 0;
    
    public void increment() {
        Runnable r = () -> count++;  // ✅ OK
        r.run();
    }
}

```
### 10. Ưu nhược điểm lambda
#### Ưu điểm
- Ngắn gọn
- Dễ đọc
- Functional
- Flexible
- Performance: invoke dynamic optimization
#### Nhược điểm
- Debug khó
- Giới hạn: chỉ dùng cho functional interface
- Effectively final: không modify local variables
- Learning curve: khó học
### 11. Khi nào dùng Lambda
- Implement functional interface
- Code ngắn gọn
- Stream operations
- Event handlers
- Callbacks
**Không dùng khi**
- Logic phức tạp (> 5 dòng)
- Cần resuse nhiều --> method
- Cần nhiều abstract method --> class
- Debug --> method


# Tóm tắt
Lambda Expression: Anonymous function, implement Functional Interface
Cú pháp: (params) -> expression hoặc (params) -> { statements; }
Type inference: Compiler tự suy ra type
Effectively final: Chỉ access, không modify local variables
vs Anonymous class: Ngắn gọn hơn, this khác, chỉ Functional Interface
Dùng khi: Code ngắn (1-3 dòng), Stream, callbacks
Best practice: Ngắn gọn, tên có ý nghĩa, extract method nếu phức tạp

# Method reference trong Java
Method Reference là gì? Các loại Method Reference? Khi nào dùng?
## Method reference là gì?
Là cách viết ngắn gọn của lambda expression khi mà lambda chỉ gọi 1 method có sẵn
Đặc điểm:
- Shorthand của lambda expression
- Dùng operator **::**
- Dễ đọc hơn lambda
- 4 loại: static, instance, constructor và Arbitrary Object
Cú pháp:
```java
Classname::methodName;
```
## 2. Tại sao dùng method reference
So sánh 2 cách viết:
- Ngắn gọn hơn
- Dễ đọc
- Ít duplicate code
## 3. Các loại method reference
### 3.1 Static method reference
Reference đến static method
Cú pháp:
```java
Classname::staticMethodName;

//method reference
Function<String, Integer> parseInt = Integer::parseInt(str);

List<String> numbers = Arrays.asList("1", "2", "3");

// Method reference
List<Integer> ints = numbers.stream()
    .map(Integer::parseInt)
    .collect(Collectors.toList());
```
### 3.2 Instance Method Reference (Bound)
Reference đến instance method của object cụ thể
```java
objectInstance::instanceMethod

String prefix = "Hello, ";

// lambda
Function<String, String> addPrefix = str -> prefix.concat(str);

// Method reference
Function<String, String> addPrefix2 = prefix::concat;

// trong stream
List<String> names = Arrays.asList("John", "Jane", "bob");
String separator = ", ";

// lambda
String result = names.stream()
.collect(Collectors.joining(separator));

// method reference voi PrintStream
names.forEach(System.out::println);
```
### 3.3 Instance Method Reference (Unbound) 
Reference đến instance method của type, object được truyền vào:
Cú pháp:
```java
Classname::instanceMethodName

// get length of string
Function<String, Integer> length = str -> str.length();

Fuction<String, Integer> length = String::length;

int len = length.apply("Hello"); // 5

List<String> names = Arrays.asList("John", "Jane", "Bob");

// lambda
List<String> upper = names.stream()
.map(str -> str.toUpperCase())
.collect(Collectors.toList());

// method reference
List<String> upper = names.stream()
.map(String::toUpperCase)
.collect(Collectors.toList());
```
### 3.4 Constructor reference
Cu phap
```java
Classname:new

List<String> names = Arrays.asList("John", "Jane", "Bob");

// lambda
List<User> users = names.stream()
.map(name -> new User(name))
.collect(Collectors.toList());

// constructor reference
List<User> users2 = names.stream()
.map(User::new)
.collect(Collectors.toList());
```
Array constructors:
```java
Function<Integer, String[]> arrayCreator = size -> new String[size];

//constructor reference
Function<Integer, String[]> arrayCreator2 = String[]::new;
String[] array = arrayCreator.apply(10);

// stream
List<String> names = Arrays.asList("John", "Jane", "Bob");

// Lambda
String[] array = names.stream()
    .toArray(size -> new String[size]);
    
// constructor reference
String[] array = names.stream()
.toArray(String[]::new);
```
### 4. So sánh Lambda vs Method Reference

| Lambda                   | Method reference  |
| ------------------------ | ----------------- |
| str -> str.length()      | String::length    |
| () -> new ArrayList<>()  | ArrayList::new    |
| user -> user.getName()   | User::getName     |
| (a, b) -> a.compareTo(b) | String::compareTo |
### 5. Khi nào nên dùng Method Reference
Nên dùng khi:
- Lambda chỉ gọi 1 method
- Method đã tồn tại
- Code ngắn gọn, dễ đọc
Không nên dùng khi
- Lambda logic phức tạp
- Lambda gọi nhiều methods
- Method reference khó hiểu hơn lambda
### 6. Method reference với Optional
```java
Optional<User> userOpt = findUser(1);

// map với method reference
Optional<String> nameOpt = userOpt.map(User::getName);

// flatMap với method reference
Optional<Address> addressOpt = userOpt.flatMap(User::getAddress);

// ifPresent với method reference
userOpt.ifPresent(System.out::println);
```
### 7. Method reference với Comparator
```java
List<User> users = getUsers();

// comparing
users.sort(Comparator.comparing(User::getAge));

// comparingInt (primitive)
users.sort(Comparator.comparingInt(User::getAge));

// thenComparing
users.sort(Comparator.comparing(User::getAge)
                     .thenComparing(User::getName));

// reversed
users.sort(Comparator.comparing(User::getAge).reversed());

// nullsFirst/nullsLast
users.sort(Comparator.comparing(User::getName, 
                                Comparator.nullsLast(String::compareTo)));
```


