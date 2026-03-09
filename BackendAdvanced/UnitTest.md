Tạo unit test trong folder test
Sử dụng **JUnit test** đối với các testcase đơn giản
**@BeforeAll**: chạy trước tất cả các test case
**@AfterAll**: tương tự nhưng chạy sau tất cả các test case
**Mockito**
dùng với các logic phức tạp (cần điều kiện giả thiết, bị phụ thuộc bởi các class khác)
- Hỗ trợ fake kết quả các phụ thuộc mà chúng ta cần test
- Giúp kết quả unit test mang tính độc lập (không ảnh hưởng bởi thành phần khác)
VD: crawler service phụ thuộc vào các hàm bên ngoài ()
Cách test:
- Mock (fake) kết quả của các hàm phụ thuộc
VD: bài toán scaper (Crawl) ta fake dữ liệu việc gọi request tới trang source statusCode = 200
--> để khi test thực tế ta sẽ không quan tâm việc gọi có thành công hay không --> luôn trả về 200
VD2: có thể sử dụng để fake data test việc catch exception

Best practices: cách đặt tên test nên gồm 3 phần
- tên của hàm cần test
- kịch bản cần test
- kết quả kì vọng khi chạy
Best practices: cài đặt test gồm 3 phần
- Arrange (given): khởi tạo các objects
- Act (When): các hành động (phương thức) với objects
- Assert (then): kiểm tra các điều kiện cần được thỏa mãn