# Day 1
## 1.1 Difference between Vibe coding and agentic engineering
Sự khác biệt ở chỗ how output get verified
- Vibe coding: classic eye test
- Agentic engineering: hard test để verify deterministic code (code do môi trường, ngoại cảnh tạo ra), sử dụng systemmatic evaluations để verify các non-deterministic reasoning của AI
--> no systematic evaluation == vibe coding
## 1.2 Sự thay đổi với developer
Đó là kĩ thuật `prompt engineering`, cố gắng để tricking model đưa ra good answer and cleverly worded instructions.
New core skill là `context engineering` : cung cấp cho AI highly structured diet về thông tin codebase và architecture.

### 1.2.1 Types of context
- Static context: information that always loads into the AI's brain for that project. like agents.md file. Tuy nhiên nếu load quá nhiều static context như diagram, rules và nói rằng'figure it out' --> nó sẽ tự trả ra kết quả.
Tuy nhiên điều này sẽ gặp phải vấn đề là `context rot`. LLM có massive context windows, nhưng nếu load quá nhiều vào mà không liên quan dự án --> noise --> dilute attention
- Forget the primary instruction 
- Get variables from unrelated folder
Tránh những hạn chế của static context có dynamic context (agent skills): give AI lightwright menu of skills ngay từ đầu để nó có thể làm việc ngay
### 1.2.2 Criteria for building context
Để tránh việc AI bị halucinating --> cần  chú tâm 6 types of context
- Instructions: define cho agents core boundaries
- Knowledge: architecture diagrams, API docs và style guide
- Memory: để tránh việc endless trial và error loops ở vibe coding gặp phải
- Example: đưa cho agent good code trong repo của bạn, nhờ đó nó không phải invent lại solution từ đầu
- Tools: CLIs, APIs để agent có thể thao tác và sử dụng trong quá trình phát triển. 
- Guardrail concept (gác chắn) là phần đánh chặn. Nếu AI đột nhiên halluciates plan, tự ý xoá data trong DB khi mà hết bộ nhớ --> panic
Khi thực hiện tác vụ cần đến skills nào, agent tự load skills đó và load các tools cần thiết cho task đó.
Ngoài ra cũng hỗ trợ preserving computing resource, chỉ thực hiện những thứ thực sự cần thiết

## 1.3 Change in SDLC With Vibe Coding
Timeline để build software thực sự thay đổi hoàn toàn  software development life cycle (SDLC)
Tuy nhiên human bottleneck vẫn còn đó, ở bước outer là architecture and verification.
Trajectory evaluation: journey to answer, kiểm tra intermediate steps AI tiến hành để cho ra kết quả
Ngày nay developer giỏi không phải là người hands-on toàn bộ code mà là người xây được model factory tốt của riêng mình.
Tạo ra các cánh tay robot hỗ trợ, routing the raw metarials và placing quality control sensors để đảm bảo đầu ra.
## 1.4 Harness Engineer
`The Agent = The Model + The Harness`
Giàn giáo (scaffolding) hỗ trợ model gọi là Harness
Gồm 1 vài thứ:
- Sandboxes: môi trường test mà ở đó AI chạy code mà không bị gián đoạn hoặc làm sập production
- Orchestration logic: bộ não routes các massive task với các sub agents. Nó sẽ cần thiết có observability infrastructure. (hệ thống giám sát) gồm các logs file, execution traces, cost metering.
Agen drift: phản hồi chậm hoặc đưa ra các bad logic.

Impact của harness engineering là ko thể chối cãi. Thường handle các tác vụ như context, middleware translators và tools definitions.
Với việc có AI, day-to-day task của AI agent có 2 mode chính:
- Conductor: hands-on, real time coding, using tools. Mode này cần khi debug complex tricky code logic, hoặc customized part of codebase mà human intuition là điều cần thiết.
- orchestrator: bạn đưa ra yêu cầu tổng quát cho terminal agents, phù hợp cho massive test ting hoặc sweeping refactors.

Vấn đề chung: `80% problem`
- Agent có thể generate 80% của feature, vượt qua toàn bộ boilplate. Nhưng 20% còn lại business logic, subtle system intgrations luôn cần sự can thiệp của con người.
- AI ko thay thế, nó chỉ automate 80% của việc implementation, dồn năng lượng của con người vào 20% giá trị cao còn lại, ở đó architectural judgement cần được thực thi
## 1.5 Token economy
Đốt tiền từ ban đầu để dùng AI, build API schemas, write the comprehensive agents .md file, tạo sandboxes và code deterministic evils.
Khi factory hoàn thành, chi phí biên để tạo ra 1 feature giảm đáng kể
Harder upfront but financially sustainable forever.
### 1.5.1 Model routing
Với hệ thống harness tốt, nó sẽ tự động routing các task simple và phức tạp với từng model cụ thể (frontier model or base model)

--> Game changer for token economy

## New vobabs
halluciating (adj) : ảo giác AI
going off the rails: lệnh đường ray
from scratch: từ đầu
dilute = decrease: giảm bớt (AI)
attention mechanism dilutes when you flood them with noise
Trajectory  evaluation: journey to answer 
dismanlessly (adj): không cần con người can thiệp

Intent is the new interface

# Day 2
## 2.1 Điều cần thiết có 1 standard tools giám sát agents
Khi bạn vibe coder mà không có standard protocols --> massive technical debt
--> cần thiết của MCP (model context protocol) đánh giá là USB type C của model harness
MCP đóng vai trò điều phối
VD: chạy trên 5 models với 10 tools khác nhau, nếu cấu hình chay thì độ phức tạp là O(N * M)
- n: số tools
- m: số models
Với MCP --> O(N + M): với mỗi tools 
Sử dụng `standard transport trong MCP`, bạn ko cần trách nhiệm việc connect tools với model mà việc của bạn chỉ điều phối (orchestrator)
Standards transports: stdio (c++), sse (server-sent event với server host bên ngoài)
MCP: chính là tạo 1 plumbing ( hệ thống ống nước) để sẵn khi mà AI cần gì có thể tạo instructions tới tools cần truy cập qua MCP

### 2.1.2 Cách cấu hình trong thực tế
Bạn không cần phải build từ đầu để có thể sử dụng. Các bước để onboarding một MCP server
1. Discovery

Sử dụng locate pre-built MCP server từ các nguồn có thể tìm:
- Public MCP Registries: có nơi chứa các MCP server có sẵn
- Third-party 3P remote MCP servers: các máy chủ MCP chính thức của Google
- Internal Registry: internal tools được expose qua MCP server và được lập thư mục ở security cao. Triển khai technical được managed qua API gateway, GCP Agent Registry hoặc private microservice portal.
2. Configuration
Trước khi connect tới agents, xác định các standard parameters để xác định server's scope và các permissions. Gồm xác định các environment file để handle API credentials (Personal Access Token hoặc Oauth)
3. Connection
Tạo kết nối từ model agent tới MCP server
### 2.1.3 Các lưu ý khi sử dụng MCP
1. MCP chưa được kiểm duyệt
--> Cần kiểm soát các quyền khi mà bạn open a door để ứng dụng bên ngoài gọi tới agent và thao tác trên system của bạn. 
Guard station ---> Model Armor
Hỗ trợ truy xuất luồng data và ngăn chặn rủi ro rò rỉ dữ liệu độc hại
2. Agent's cognitive load
Dump 50 tools schemas trong agent system prompt --> loãng sự chú ý thông tin cần + tốn context window
Tips: sử dụng RAG (retrieval augmented generation): chỉ sử dụng tools khi cần thiết
VD: khi có tác vụ lập lịch, gọi MCP server của Google Calendar thôi
## 2.2 Debugging Issues with MCP Servers
Khi agent gặp halucinate, việc mọi người thường làm là argue with "Làm cách này sai rồi... Như này mới đúng" --> khiến agent brittle (dễ vỡ, mỏng manh, ko tự tin)
Cách làm đúng: MCP inspector tools hoặc Chrome dev tools
`Fix the pipes, don't yell the water`: hãy sửa đường ống, đừng thay đổi dòng nước
Đưa cho agent perfect tools đủ để làm các việc mà bạn yêu cầu
### 2.2.1 Vibe coder toolkits
Khi sử dụng MCP servers:
- Nên
1. Luôn đọc mã nguồn của MCP server trước khi gán vào agent để sử dụng
2. Sử dụng RAG cho tool: load động các tools chỉ khi thực sự cần thiết, và drop tools khỏi context window khi dùng xong để tránh attention dilution
3. Ưu tiên sử dụng internal tool registries để tránh việc sử dụng các unvetted code.
4. Sử dụng MCP Inspector: khi agent bị hallucinates, look at MCP Inspector hoặc Chrome Devtools để theo dõi luồng data thay vì cứ đọc prompt.

## 2.3 Agent-to-Agent (A2A) Interoperability
Giải pháp kiến trúc ở đây là sẽ tách workload cho các specialized sub-agent
Có 1 question: Khi mà có rất nhiều các tools bên ngoài cung cấp sẵn thay vì đưa có thành standard tools sử dụng MCP?
### 2.3.1 Bounded and unbounded domains
Bounded: Những tools cung cấp sẵn, chỉ chuyên về 1 domains --> `passive instrument`.
Unbounded domain: cung cấp 1 AI specialists, vận hành trên unbounded domain.
VD: bạn thuê 1 contractor để renovate kitchen, nhưng bạn sẽ không thể chỉ cần đưa bản vẽ và yêu cầu mong muốn. Bạn cần đưa cho họ bản vẽ đường dây điện, trao đổi giá cả, giải pháp.
### 2.3.2 The GOTO Problem in Agentic Architecture
Bởi Agent domain là unbounded, khi cố gắng để force vào standard tool wrapper
Do đó khi gọi collaborative agent, control flow rời khỏi expected structured context. Agent có thể gián đoạn, yêu cầu thông tin và có khả năng không bao giờ trả ra expected output.
DO đó cần paradigm để cô lập trạng thái phức tạo, multi-turn state. Cần 1 giao thức cho phép agent tạm dừng thực thi, liên hệ lại với orchestrator, đàm phán giải pháp và return to work mà ko mất conversational state.
Đó gọi là giao thức `A2A protocol`, isolate collaborative routing tới A2A layer, do đó keep tool layer (MCP) clean.
### 2.3.2 A2A Interoperability
Cần thiết 1 giao thức (protocol) để isolate và manage collaborative routing --> A2A (Agent-To-Agent)
A2A giống như radio channel để các sub-agents giao tiếp với nhau khi làm việc
Tuy nhiên các agent cần biết nhau chuyên trách về tác vụ gì --> `Agent card`
Developer cần post Agent card như là CV lên agent registries
Mỗi Agent có thể có nhiều agent card

Vocabs:
technical debt: chỉ những mã viết bởi AI về sau khó maintain manually (vibe code)
orchestrator (n): người điều phối (vai trò điều phố các agents làm việc)
structural trap: bẫy hệ thống, khi mà thiết kế API ko theo quy chuẩn cụ thể
unvetted(adj): chưa được kiểm duyệt
attention dilusion (n): làm loãng sự chú ý
monolithic ceiling: trần tiêu thụ