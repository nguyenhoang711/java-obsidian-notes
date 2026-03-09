# Gradle
- build.gradle: chứa các dependencies (giống pom.xml)
Mỗi class có thể áp dụng pattern Singleton chỉ tạo 1 instance duy nhất (tùy vào config của mình)

# Annotation
## @SpringBootApplication
gồm 3 tính năng
- @EnableAutoConfiguration: cho phép tự động đoán và cấu hình các bean dựa theo các dependency
- @ComponentScan: giúp @Component scan ở package của application
- @Configuration: khởi tạo thêm các bean khác trong context hoặc thêm vào các lớp cấu hình tùy chỉnh thêm
## @Component và @Autowired
@Component giúp spring nhận diện bean tự động. Tức là:
- Scan toàn bộ app để tìm các class được đánh dấu bằng @Component
- Khởi tạo chúng và truyền các phụ thuộc (nếu có)
@Autowired: inject dependency tự động
