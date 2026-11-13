# Harness là gì
Harness là lớp bao quanh để model thô trở thành agent biết làm việc
VD: chiếc xe ô tô
- Động cơ: được coi là model
- Agentic loop: vòng lặp tự gọi tools đến khi xong
- Context & Session: giúp agent quản lý ngữ cảnh, thông qua nhiều bước nhưng vẫn giữ được nội dung chung
- Tool intergration: tích hợp tool (đọc file, shell, search)
- Permission & Control: quyền kiểm soát, quyết định được làm gì
- Safety & Sandbox: kiểm soát, ngăn chặn thao tác nguy hiểm, không để chạy tự động
Những thứ nằm xung quanh model --> Harness
Do đó khi dùng chung 1 model nhưng ở trên claude code, antigravity, hay vscode cho ra kết quả khác nhau (với cùng 1 prompt)
ĐÓ là do lớp harness làm cho output khác nhau
Ko Harness = Gọi API thuần
Có harness = Sử dụng CLI hoặc app/ Agent SDK

