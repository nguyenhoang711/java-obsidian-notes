Xuất hiện trong Java 8
- tiện khi làm việc với data list
- Giảm thiểu code
1. Định nghĩa
Trong Java, Stream là một API được giới thiệu từ phiên bản Java 8 để xử lý dữ liệu theo cách linh hoạt, hiệu quả và dễ đọc. Stream không phải là một cấu trúc dữ liệu để lưu trữ dữ liệu, mà nó là một chuỗi các phương thức cho phép thực hiện các thao tác xử lý dữ liệu như lọc, ánh xạ, giới hạn, sắp xếp, và gom nhóm trên các tập dữ liệu.
2. Điểm quan trọng trong Stream java
- **Lười biếng (Lazy Evaluation)**: chỉ thực sự ghi lại kết quả khi cần thiết --> giúp tiết kiệm tài nguyên, cải thiện hiệu suất
- Luồng tuần tự: stream xem như luồng tuần tự các phần tử dữ liệu. Thực hiện các phép biến đổi stream 1 cách tuần tự mà không cần quan tâm bản chất dữ liệu.
- Phương thức trung gian và kết thúc: các phép biến đổi qua phương thức trung gian như filter(), map, sorted, ... Và cuối cùng là phương thức kết thúc như reduce, collect, forEach, để lấy kết quả cuối cùng sau khi thực hiện các phép biến đổi.
- **Không thay đổi dữ liệu gốc**: ko làm thay đổi mà tạo ra dữ liệu mới qua các phép biến đổi.
- **Parallel streams**: cho phép xử lý song song streams trên nhiều luồng, nâng cao hiệu suất.
