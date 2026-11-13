# Configure Claude
## CLAUDE.md
Các rules sẽ luôn đưa vào CLAUDE.md --> cố gắng giữ file này càng nhỏ càng tốt.
Sử dụng file đúng cách, thay vì lưu vào file, hãy sử dụng `hooks`
Hook: đoạn code chạy trước khi CLAUDE take an action, và nó có thể block the action
### The four locations
CLAUDE.md không phải chỉ có 1 file đặt trong project. mà nó có ở trong 4 vị trí, mà claude load all mỗi khi launch
- Managed policy: tệp các cấp tổ chức file.
- User: các tuỳ chọn cá nhân, đi theo trên mọi dự án trên máy của bạn
- Project: share với team, check trong repo
- Local: git ignore.
Cái cuối cùng vô cùng quan trọng, ở local thì ko ảnh hưởng tới những đồng nghiệp khác. Chỉ áp dụng các rule mà bạn cho rằng là đúng và thuận tiện
### Split big file with imports
khi dự án phình to, cần có cách tổ chức rule, conventions để đảm bảo claude hoạt động đúng:
```
@.claude/conventions/code-style.md @.claude/conventions/testing.md
@.claude/conventions/workflow.md
```
Khi Claude launch, truy cập các file được import trong prompt. Sử dụng import file chỉ giúp dễ đọc, chứ ko shrink the load.
### Phrasing is what makes rules stick
Cách để phrasing các rule trong CLAUDE.md file:
- Be specific and checkable: đừng viết chung chung, Claude sẽ không hiểu được
	- Vague: _"Follow best practices for API routes."_
	- Specific: "Put new API routes in `src/api/handlers`, one per file."
- Hãy đưa ra giải pháp khác, đừng chỉ phủ nhận hành động: 
	- Leaves it open: _"Don't use default exports."_ Okay, but then what?
	- Closes it: "Use named exports, not default exports."
- Nhấn mạnh (Emphasis) nên sử dụng đúng lúc: nếu tất cả đều IMPORTANT thì không có gì quan trọng cả. Hãy chỉ dành những thứ thực sự đặc biệt (2 hoặc 3 rules) để emphasis
- Keep file under revision: đưa CLAUDE.md file giống như 1 file code trong repo. Có thể ra lệnh cho Claude "add that to the CLAUDE.md file,"
### Sum up:
- Move hard rules to hooks, where they actually enforced
- Organize long file with imports
- Make every rule specific and checkable
- Spend emphasis budget on the few rules that matter cost
- Keep revising the file whenever Claude get something wrong

## Verification skills
Sử dụng skills giúp việc automate repeated work.
### Why a verification skill is the one to build first
Đôi lúc việc phải ghi nhớ các thay đổi của Claude để double-check có thể bị thiếu.
Do đó cần verification skills: đảm bảo change matches the skill's description:
- Run the test suite
- Read the diff
- Check that no test was weakened just to make things pass
- Reports pass or fail, with the evidence attached
Nhờ có skill mà làm giống nhau trong mọi lần
### A skill folder can hold more than instructions
`reference.md`: cho chi tiết các tài liệu, sau đó link từ `skill.md`. Claude chỉ đọc khi thực sự cần. Nhờ đó main file ngắn hơn
The takeaway: giữ cho `skill.md` file lean. Push heavy material, long explainations và executable scripts vào side files.
### Which instruction surface owns which rule
`CLAUDE.md`: lưu các conventions như naming rules, where files go.
`skills`: procedure và material reference lưu trong skill
`Hook`: các rule mà claude không thể skip, bởi đó là những đoạn code mà bắt buộc Claude phải thực thi.

## Permission Modes
Shift + Tab: chuyển đổi giữa các mode khi conversation với Clade
- `Manual`: read only, mọi thứ phải asks first
- `Accept edits`:  cho phép edit file, sửa các bash commands mà không cần asking. Review sau khi thực hiện
- `Plan`: chỉ đọc, đưa ra plan chờ được thực thi
- `Auto`: cho phép tất cả, model tự review tất cả các bước trước khi chạy
- `Don't ask`: chỉ các tools pre-approved. Các còn lại auto-denied with no prompt.
- `Bypass permission`: skip tất cả check, chỉ chạy inside isolated container hoặc virtual machine
### How auto mode works
Nó được coi là hands-off mode. Có 1 vài trường hợp đặc biệt sẽ bị block lại:
- Production deploy and migrations
- Force pushing, or piping downloaded code straight into a shell
- Sending sensitive data tới external endpoints
- Destroy files mà exist trong session
### What the classifier can't do
Classifier checks intent, không phải correctness. Nên nếu như mà có yêu cầu fixbug authen thì classifier sẽ bỏ qua, vì broken không dangerous
Do đó cần kết hợp `auto mode và stop hook`:
- Auto mode kiểm tra Claude đang cố gắng làm gì trong lúc chạy
- The `stop hook` confirms code run khi Claude finish
### Don't ask, for unattended runs
Phù hợp với công việc không cần con người can thiệp hoặc approve prompt: CI pipelines, scheduled jobs, overnight batches
Chỉ các pre-approved tools mới có thể thực hiện

### Match the mode to the job
`Auto`: classifier checks intent trước khi hành động, stop hook checks correctness sau đó.
`Don't ask`: unattended pipelines mà không ai approved
Bypass permissions: inside isolated containers và VMs
## Hook
Có vấn đề với CLAUDE.md: nó là request, không phải guarantee. Cần hook để luôn luôn chạy tại 1 fixed point trong loop, turn into "Claude can't skip it"
### The hook events
Chia thành các hook events qua the course of a session
- `PreToolUse`: kích hoạt trước khi gọi tool. Stop mọi thứ trước khi nó bắt đầu
- `PostToolUse`: kích hoạt sau khi gọi tools thành công. Có thể là auto-formatting hoặc  auto-lint kích hoạt
- `Stop`: kích hoạt khi Claude muốn end. Bạn có thể ngắt nó và nói "no, you're not done yet" nếu một vài điều kiện chưa đáp ứng. **SubagentStop** khi sub-agent hoàn thành
- `PreCompact` và `PostCompact`: kích hoạt trước và sau khi compact 
- **InstructionsLoaded** kích hoạt khi CLAUDE.md hoặc rule file loads. Thích hợp để kiểm tra xem thứ gì đc load vào context
- `SessionStart` kích hoạt khi start hoặc primes environment. 
### PreToolUse: returning a decision as JSON
`PreToolUse`: có thể block tool call trước khi đc gọi. Cách giao tiếp với Claude là printing JSON và exiting zero. Key field là `permissionDecision`, có các giá trị:
- allow: cho phép call through
- deny: stop the call
- ask: đưa cho người dùng tự quyết định
### Exit codes, for hooks that don't return JSON
Không phải tất cả hook đều speak JSON. Có 3 giá trị cần quan tâm:
- 0 là thành công. Sau đó plain text đc add vào context --> khiến preserver hook work
- 2 là blocking error. Standard error feed back tới Claude
- Còn lại là non-blocking. error được log lại, và Claude carries on.
### A real guardrail: redact instead of block
## Routines and Headless
### Routines: a saved prompt that runs in the cloud
Cần 3 yếu tối cho 1 routines: a prompt, the repository và connectors it needs.
Routines thực thi có 1 số loại:
- Cron schedule, VD 9h sáng hàng ngày
- HTTP POST tới API endpoint, nhờ đó own code có thể kick off
- Github Event, new pull request landing
### Two ways to create one
Tạo trên giao diện: routines, đưa vào instructions, pick a repository và choose trigger
Tạo với terminal: `/schedule` command và describe what you want
VD: /schedule daily dependency audit at 9am
### Three things to know before you rely on routines
Lưu ý khi sử dụng Routines:
- Research preview: behavior và limits luôn moving, vì vậy chấp nhận nó thay đổi.
- **A recurring schedule runs at most hourly**. Điều đó ko phù hợp với routines.
- Thay đổi đưa vào `claude/` branch để tránh việc rewriting main
### Headless mode: when you need your own environment
Core là -p flag (--print). Nó chạy Claude Code với one-shot command với no interactive UI. Đọc dữ liệu và trả ra kết quả tương ứng.
```
claude -p "summarize the changes in this diff"
```
Điều lưu ý: `-p` sẽ skip auto-discovery hooks, skills và plugins, MCP, và CLAUDE.md file.
### The Agent SDK: Claude Code inside your own app
Dùng Agent SDK sẽ ko kế thừa được các harness từ Claude Code
Chỉ dùng lại
- allowedTools: control what CLaude can do
- system prompt
- a permission mode
## Plugins
Đóng gói các .claude directory để đưa người dùng khác sử dụng
### What a plugin is
```
/plugin install org-name@plugin-name
```
Claude Code install và yêu cầu `/reload-plugins` để apply thay đổi
### Components run alongside yours
Không ghi đè config, mà chạy song song với config claude của bạn. 
Plugin cũng ship `settings.json` file, nhưng chỉ là narrow one. 