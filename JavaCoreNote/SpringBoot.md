# Spring Core
## Dependency Injection (DI) là gì và bạn dùng nó trong dự án thực tế như thế nào?
Là pattern cho phép Spring inject dependency thay vì tự tạo, dùng để giảm coupling giữa các class, dễ test hơn vì có thể mock dependency, và dễ maintain vì thay đổi implementation chỉ cần đổi config.
Trong dự án, chủ yếu dùng constructor injection - đảm bảo dependency luôn có sẵn, dễ test và có thể làm field final.
## Bean Scopes trong Spring
Là phạm vi hoạt động của Bean
- `singleton`: 1 instance cho cả app, VD service, repository (default)
- `prototype`: mỗi lần lấy bean tạo instance mới
- `request`: mỗi HTTP request có 1 instance
- `session`: mỗi user session có 1 instance
- `application`: 1  instance cho toàn bộ ServletContext
## Component Scan hoạt động như thế nào?
Hỗ trợ Spring tìm kiếm bean tương ứng với annotation như @Component, @Repository, @Service, @Controller
Nếu không có Component Scan: phải đăng ký từng Bean thủ công trong large project
``` AppConfig.java
@Configuration
public class AppConfig {
    @Bean
    public UserService userService() {
        return new UserService(userRepository());
    }

    @Bean
    public UserRepository userRepository() {
        return new JdbcUserRepository(dataSource());
    }

    @Bean
    public OrderService orderService() {
        return new OrderService(paymentService(), userRepository());
    }

    @Bean
    public PaymentService paymentService() {
        return new StripePaymentService(stripeConfig());
    }

    @Bean
    public EmailService emailService() {
        return new EmailService(javaMailSender());
    }

    @Bean
    public NotificationService notificationService() {
        return new NotificationService();
    }

    @Bean
    public ProductService productService() {
        return new ProductService(productRepository());
    }
    // ... 50+ beans khác phải đăng ký thủ công
}
```

Thực tế các dự án không khai báo riêng @ComponentScan annotation mà nó đã có sẵn trong annotation @SpringBootApplication và scan package chứa class đó. Tôi thường giữ cấu trúc package rõ ràng để scan đơn giản, không cần custom.
Có component scan, chỉ cần thêm @Service, class tự động được scan và tạo bean.
## Sự khác nhau giữa @Component, @Service, @Repository là gì?
Cả 3 đều dùng để khai báo Bean và đều được @ComponentScan quét. Khác biệt chủ yếu nằm ở semantic - đánh dấu vai trò của class trong kiến trúc layered.
- @Component: cho bean chung
- @Service: cho bean thuộc tầng business layer
- @Repository: cho data access
Trong code thường dùng đúng để rõ ý đồ và dễ maintain
## Các cách khai báo bean trong Spring
Có 3 cách phổ biến:
- Stereotype annotation: @Component, @Service, @Repository và @Controller
- Dùng @Bean trong class @Configuration
- Dùng XML config (thường ít dùng)
`<bean id="userService" class="com.example.UserService" />`

Kèm `@Primary` — Bean mặc định khi có nhiều bean cùng kiểu
## @RestController khác gì @Controller?
`@RestController = @Controller + @ResponseBody`.
