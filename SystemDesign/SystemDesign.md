# 1. What is System Design
Là thiết kế ra structure hoặc operation of a system để đáp ứng yêu cầu cụ thể
System Design: dùng các kiến thức về computer science và kiến thức công nghệ để giải quyết bài toán business
**System Design = Problem Solving + Computer Science**
Cần traiu dồi kiến trức Problem Solving + Computer Science (độc lập)
**System Design Interview != System Design Reality** 
In reality: constraint resource and harmful effects (known and unknown)
![[Pasted image 20260503232908.png]]
## 1.2 Architecture and Design

|                | Architecture                                                                                              | Design                                                                                                                                  |
| -------------- | --------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Abstract Level | Highest level strategy (chiến lược)                                                                       | High level tactic (chiến thuật)                                                                                                         |
| Scope          | Focus vào non-functional requirement<br>- Định nghĩa kiểu kiến trúc để đáp ứng non-functional requirement | Focus giải quyết non-functional requirement + kèm giải quyết bài toán cụ thể<br>- Cấu trúc dữ liệu, algo --> giải quyết bài toán cụ thể |

Chiến lược: định nghĩa hệ thống có tính chất như là performance, availability --> non functional requirements
Architecture = High level Design
## 1.3 Kiến thức cho thiết kế hệ thống (Knowledge Mapping)?
![[Pasted image 20260504233950.png]]
### Kiến thức nào là nền tảng? Kiến thức nào là quan trọng?
## 1.4 Knowledge classification?
Fundamentals:
- Problem solving
- DSA
- OS
- Network
- Concurrent programming
- Computer Science
Importance:
- **Scale out hệ thống**
- **Database**
- **Caching**
- Communication
Strategy:
- **Quan trọng** học trước, nền tảng tích lũy dần
- Nếu mắc ở quan trọng --> xuống tìm hiểu lại **nền tảng** liên quan
## 1.5 Cần show off gì trong SD interview?
Problem solving
- How to clarify problem?
- Analyze the problem and solutions?
Solid Fundamental knowledge
- Computer Science
Communication
- Proactive
- Presentation
Nice to have: product mindset
Giải quyết đc vấn đề của User
VD: bài toán tìm đường đi từ A tới B
Mình có thể hỏi thêm sản phẩm có thêm tính năng về stop point ko VD từ A đến C rồi đến B
## 1.6 Các bước trong Design System
1. hiểu bài toán và scope của nó
2. Ước tính
3. high-level design
	1. API design: đảm bảo hiểu function làm việc như nào
	2. Data model: tổng hợp từ bước 1 và 2 (các entity, service)
	3. High level design: draft solution (giải pháp tổng quát)
4. Deep drive (low level design)
	1. Bài toán về storage, concurrency
	2. Chú ý:
		1. Không có giải pháp cuối cho design
		2. Low-level decision ảnh hưởng tới high-level design
Nhiệm vụ của SA: khi design phải đi cùng đội dev để xem xảy ra vấn đề gì khi triển khai không, có thể gặp trade-offs nên cần thay đổi thiết kế
# 2. What is the problem
## 2.1 Định nghĩa vấn đề là gì?
![[Pasted image 20260507004132.png]]
Là khoảng cách giữa thực tế và kết quả kì vọng
Đôi khi ở mỗi góc nhìn khoảng cách này sẽ khác nhau
Mỗi Role có góc nhìn khác nhau --> đối với stackholder liên quan tới problem
## 2.2 How to understand the problem?
- Kéo thực tại về mong muốn (dev làm theo ý khách hàng)
- Kéo mong muốn về thực tại (thuyết phục khách hàng -->giảm mong muốn)
- Kéo về 1 điểm trung gian (ở giữa): biết cách + hướng giải quyết vấn đề
