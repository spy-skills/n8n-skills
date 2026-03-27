# Hướng Dẫn Triển Khai n8n-skills Trên Hệ Thống n8n

> Tài liệu hướng dẫn chi tiết cách cài đặt, cấu hình và sử dụng 7 Claude Code skills để xây dựng workflow n8n chuyên nghiệp.

---

## Mục Lục

1. [Tổng Quan Kiến Trúc](#1-tổng-quan-kiến-trúc)
2. [Điều Kiện Tiên Quyết](#2-điều-kiện-tiên-quyết)
3. [Cài Đặt n8n-mcp Server](#3-cài-đặt-n8n-mcp-server)
4. [Cài Đặt 7 Skills](#4-cài-đặt-7-skills)
5. [Chi Tiết Từng Skill](#5-chi-tiết-từng-skill)
6. [Quy Trình Xây Dựng Workflow](#6-quy-trình-xây-dựng-workflow)
7. [Các Pattern Workflow Chính](#7-các-pattern-workflow-chính)
8. [Validation và Debug](#8-validation-và-debug)
9. [AI Agent Workflow](#9-ai-agent-workflow)
10. [Code Node - JavaScript và Python](#10-code-node---javascript-và-python)
11. [Best Practices và Lỗi Thường Gặp](#11-best-practices-và-lỗi-thường-gặp)
12. [Checklist Triển Khai](#12-checklist-triển-khai)

---

## 1. Tổng Quan Kiến Trúc

### Hệ thống gồm 2 thành phần chính:

```
+-------------------+     +-------------------+
|   n8n-mcp MCP     |     |   Claude Skills   |
|   Server          |     |   (7 skills)      |
|                   |     |                   |
| - 800+ nodes data |     | - Expert guidance |
| - Validation      |     | - HOW to use MCP  |
| - Templates       |     | - Patterns        |
| - Workflow mgmt   |     | - Error fixing    |
+-------------------+     +-------------------+
        |                         |
        +------------+------------+
                     |
              Expert Workflow
                 Builder
```

- **n8n-mcp MCP Server**: Cung cấp dữ liệu (800+ nodes, validation, templates, workflow management)
- **Claude Skills**: Cung cấp hướng dẫn chuyên gia về CÁCH sử dụng MCP tools hiệu quả
- **Kết hợp**: Tạo ra một workflow builder chuyên nghiệp với progressive disclosure

### Các MCP Tools chính:

| Nhóm | Tool | Chức năng |
|------|------|-----------|
| Node Discovery | `search_nodes` | Tìm node theo keyword |
| Node Discovery | `get_node` | Thông tin chi tiết node |
| Validation | `validate_node` | Kiểm tra cấu hình node |
| Validation | `validate_workflow` | Kiểm tra toàn bộ workflow |
| Workflow Mgmt | `n8n_create_workflow` | Tạo workflow mới |
| Workflow Mgmt | `n8n_update_partial_workflow` | Cập nhật workflow (MOST USED!) |
| Workflow Mgmt | `n8n_deploy_template` | Deploy template lên n8n |
| Templates | `search_templates` | Tìm workflow mẫu |

---

## 2. Điều Kiện Tiên Quyết

### Phần cứng/Phần mềm cần thiết:

- [ ] **n8n instance** đang chạy (self-hosted hoặc cloud)
- [ ] **Node.js** >= 18.x
- [ ] **npm** hoặc **yarn**
- [ ] **Claude Code** (desktop app), **Claude.ai** (web), hoặc **Claude API**
- [ ] **n8n API Key** (tạo trong Settings > API > Create API Key)

### Lấy n8n API Key:

1. Đăng nhập vào n8n instance
2. Vào **Settings** > **API**
3. Click **Create API Key**
4. Lưu lại API Key (chỉ hiện thị 1 lần)

### Lấy n8n API URL:

- Self-hosted: `http://localhost:5678` hoặc `https://your-domain.com`
- n8n Cloud: `https://your-instance.app.n8n.cloud`

---

## 3. Cài Đặt n8n-mcp Server

### Bước 1: Cài đặt n8n-mcp

```bash
npm install -g n8n-mcp
```

### Bước 2: Cấu hình MCP trong `.mcp.json`

Tạo file `.mcp.json` trong thư mục gốc của project hoặc `~/.mcp.json`:

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error",
        "DISABLE_CONSOLE_OUTPUT": "true",
        "N8N_API_URL": "https://your-n8n-instance.com",
        "N8N_API_KEY": "your-api-key-here"
      }
    }
  }
}
```

> **Lưu ý**: `N8N_API_URL` và `N8N_API_KEY` là tùy chọn nhưng cần thiết để sử dụng các tool quản lý workflow (create, update, deploy).

### Bước 3: Kiểm tra kết nối

```bash
# Test MCP server
npx n8n-mcp

# Test API access
curl -H "X-N8N-API-KEY: your-key" https://your-n8n-instance/api/v1/workflows
```

### Tools không cần API (luôn hoạt động):

- `search_nodes`, `get_node`
- `validate_node`, `validate_workflow`
- `search_templates`, `get_template`
- `tools_documentation`, `ai_agents_guide`

### Tools cần API:

- `n8n_create_workflow`, `n8n_update_partial_workflow`
- `n8n_validate_workflow` (theo ID)
- `n8n_deploy_template`, `n8n_test_workflow`
- `n8n_workflow_versions`, `n8n_executions`

---

## 4. Cài Đặt 7 Skills

### Phương pháp 1: Claude Code Plugin (Khuyến nghị)

```bash
/plugin install czlonkowski/n8n-skills
```

### Phương pháp 2: Thủ công

```bash
# Clone repo
git clone https://github.com/czlonkowski/n8n-skills.git
cd n8n-skills

# Copy skills vào Claude Code
# macOS/Linux:
mkdir -p ~/.claude/skills
cp -r skills/* ~/.claude/skills/

# Windows:
# New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills"
# Copy-Item -Recurse skills\* "$env:USERPROFILE\.claude\skills\"
```

### Phương pháp 3: Claude.ai (Web)

1. Download từng folder skill từ `skills/`
2. Zip mỗi folder
3. Upload qua **Settings > Capabilities > Skills**

### Kiểm tra cài đặt:

```
# Test 1: MCP server
"Can you search for the webhook node using n8n-mcp?"
-> Kết quả: search_nodes trả về nodes-base.webhook

# Test 2: Skill activation
"How do I access webhook data in n8n expressions?"
-> Kết quả: Skill n8n Expression Syntax kích hoạt, chỉ ra $json.body

# Test 3: Cross-skill
"Build and validate a webhook to Slack workflow"
-> Kết quả: Nhiều skill cùng hoạt động
```

---

## 5. Chi Tiết Từng Skill

### Skill 1: n8n Expression Syntax

**Mục đích**: Dạy cú pháp biểu thức n8n đúng chuẩn

**Kích hoạt khi**: Viết expressions, sử dụng `{{}}`, truy cập `$json/$node`, lỗi expression

**Kiến thức cốt lõi**:

| Biến | Công dụng | Ví dụ |
|------|-----------|-------|
| `$json` | Dữ liệu node hiện tại | `{{$json.email}}` |
| `$node` | Tham chiếu node khác | `{{$node["HTTP Request"].json.data}}` |
| `$now` | Thời gian hiện tại | `{{$now.toFormat('yyyy-MM-dd')}}` |
| `$env` | Biến môi trường | `{{$env.API_KEY}}` |

**LỖI NGHIÊM TRỌNG NHẤT - Webhook data**:
```
SAI:  {{$json.name}}
ĐÚNG: {{$json.body.name}}
```
Webhook node bọc dữ liệu vào `.body` property!

**Quy tắc quan trọng**:
- Luôn dùng `{{ }}` cho nội dung động
- KHÔNG dùng `{{ }}` trong Code node (dùng JavaScript trực tiếp)
- KHÔNG dùng expressions trong webhook path hoặc credential fields
- Tên node phân biệt hoa thường: `{{$node["HTTP Request"]}}` khác `{{$node["http request"]}}`

---

### Skill 2: n8n MCP Tools Expert (ƯU TIÊN CAO NHẤT)

**Mục đích**: Hướng dẫn sử dụng n8n-mcp MCP tools hiệu quả

**Kích hoạt khi**: Tìm node, validate cấu hình, quản lý workflow, dùng template

**Quy trình sử dụng phổ biến nhất**:

```
search_nodes (tìm node)
    |  (~18s)
    v
get_node (hiểu cấu hình, detail="standard" mặc định)
    |  (~23s)
    v
validate_node (kiểm tra, profile="runtime")
    |  (~58s)
    v
n8n_create_workflow (tạo workflow)
    |
    v
n8n_validate_workflow (kiểm tra toàn bộ)
    |  (~56s giữa mỗi edit)
    v
n8n_update_partial_workflow (cập nhật lặp đi lặp lại)
    |
    v
activateWorkflow (kích hoạt!)
```

**QUAN TRỌNG - 2 định dạng nodeType khác nhau**:

| Context | Định dạng | Ví dụ |
|---------|-----------|-------|
| Search/Validate tools | `nodes-base.*` | `nodes-base.slack` |
| Workflow tools | `n8n-nodes-base.*` | `n8n-nodes-base.slack` |
| LangChain (AI) | `@n8n/n8n-nodes-langchain.*` | `@n8n/n8n-nodes-langchain.agent` |

`search_nodes` trả về CẢ HAI định dạng:
```json
{
  "nodeType": "nodes-base.slack",
  "workflowNodeType": "n8n-nodes-base.slack"
}
```

**Smart Parameters cho IF/Switch**:
```javascript
// IF node - dùng branch thay vì sourceIndex
{ branch: "true" }   // thay vì sourceIndex: 0
{ branch: "false" }  // thay vì sourceIndex: 1

// Switch node - dùng case
{ case: 0 }  // thay vì sourceIndex: 0
```

**Luôn dùng `intent` parameter**:
```javascript
n8n_update_partial_workflow({
  id: "abc",
  intent: "Add error handling for API failures",
  operations: [...]
})
```

---

### Skill 3: n8n Workflow Patterns

**Mục đích**: 5 pattern kiến trúc workflow đã được chứng minh

**5 Pattern chính**:

| # | Pattern | Khi nào dùng | Ví dụ |
|---|---------|-------------|-------|
| 1 | **Webhook Processing** | Nhận dữ liệu từ bên ngoài | Stripe payment -> Update DB -> Send email |
| 2 | **HTTP API Integration** | Lấy dữ liệu từ API | Fetch GitHub issues -> Transform -> Jira |
| 3 | **Database Operations** | Đồng bộ/ETL database | Postgres -> Transform -> MySQL |
| 4 | **AI Agent Workflow** | AI với tools và memory | Chatbot với search + DB access |
| 5 | **Scheduled Tasks** | Automation định kỳ | Daily report -> Email team |

**Thống kê sử dụng**:
- Webhook trigger: 35% workflow
- Schedule trigger: 28%
- Manual trigger: 22%
- Service triggers: 15%

---

### Skill 4: n8n Validation Expert

**Mục đích**: Đọc hiểu và sửa lỗi validation

**Quy trình validation (lặp đi lặp lại)**:
```
Cấu hình -> validate_node (23s suy nghĩ)
    -> Đọc lỗi -> Sửa lỗi (58s sửa)
    -> validate_node lại -> Lặp lại 2-3 lần
```

**4 Validation Profiles**:

| Profile | Khi nào dùng | Mức độ |
|---------|-------------|--------|
| `minimal` | Khi đang chỉnh sửa | Nhanh, chỉ required fields |
| `runtime` | **Trước deploy (KHUYẾN NGHỊ)** | Cân bằng, bắt lỗi thật |
| `ai-friendly` | AI-generated config | Giảm false positives |
| `strict` | Production critical | Tối đa, có thể nhiều warning |

**Auto-Sanitization** (tự động sửa):
- Binary operators (equals, contains) -> xóa `singleValue`
- Unary operators (isEmpty, isNotEmpty) -> thêm `singleValue: true`
- IF/Switch metadata -> thêm tự động

**KHÔNG tự động sửa được**:
- Kết nối bị hỏng (broken connections)
- Số lượng branch không khớp
- Trạng thái corrupt

---

### Skill 5: n8n Node Configuration

**Mục đích**: Hướng dẫn cấu hình node theo operation

**Nguyên tắc cốt lõi**: Cấu hình phụ thuộc vào operation!

```javascript
// Slack - post message
{ resource: "message", operation: "post",
  channel: "#general",   // BẮT BUỘC
  text: "Hello!" }        // BẮT BUỘC

// Slack - update message
{ resource: "message", operation: "update",
  messageId: "123",      // BẮT BUỘC (KHÁC!)
  text: "Updated!" }      // BẮT BUỘC
```

**Cây quyết định chọn detail level**:
```
Bắt đầu -> get_node (standard, ~1-2K tokens)
  |
  Đủ thông tin? -> Có -> Cấu hình
  |
  Không -> Tìm field cụ thể? -> get_node(mode: "search_properties")
  |
  Vẫn chưa đủ -> get_node(detail: "full", ~3-8K tokens)
```

**Property Dependencies**: Fields xuất hiện/biến mất dựa trên giá trị field khác
- HTTP Request: `method="POST"` -> hiện `sendBody`
- `sendBody=true` -> hiện `body`
- `body.contentType="json"` -> hiện `content`

---

### Skill 6: n8n Code JavaScript

**Mục đích**: Viết JavaScript trong Code node

**Quy tắc thiết yếu**:

1. **Chọn mode "Run Once for All Items"** (95% trường hợp)
2. **Truy cập dữ liệu**: `$input.all()`, `$input.first()`, hoặc `$input.item`
3. **PHẢI trả về**: `[{json: {...}}]`
4. **Webhook data**: dưới `$json.body` (KHÔNG PHẢI `$json` trực tiếp)
5. **KHÔNG dùng `{{ }}`** trong Code node

```javascript
// Template cơ bản
const items = $input.all();
return items.map(item => ({
  json: {
    ...item.json,
    processed: true,
    timestamp: new Date().toISOString()
  }
}));
```

**Built-in functions**:
- `$helpers.httpRequest()` - Gọi HTTP từ code
- `DateTime` (Luxon) - Xử lý ngày giờ
- `$jmespath()` - Query JSON
- `$node["Name"].json` - Tham chiếu node khác

**5 lỗi thường gặp nhất**:
1. Không có `return` statement
2. Dùng `{{ }}` trong code (sai!)
3. Trả về object thay vì array: `{json: ...}` thay vì `[{json: ...}]`
4. Không kiểm tra null: dùng `item.json?.user?.email`
5. Truy cập webhook data sai: `$json.email` thay vì `$json.body.email`

---

### Skill 7: n8n Code Python

**Mục đích**: Viết Python trong Code node (Beta)

**QUAN TRỌNG**: Dùng JavaScript cho 95% trường hợp! Chỉ dùng Python khi:
- Cần standard library cụ thể (statistics, re, hashlib)
- Rất quen Python
- Logic phù hợp với list comprehensions

**Hạn chế lớn nhất**: KHÔNG có external libraries!
```python
# KHÔNG ĐƯỢC:
import requests  # ModuleNotFoundError!
import pandas    # ModuleNotFoundError!
import numpy     # ModuleNotFoundError!

# ĐƯỢC (standard library):
import json, datetime, re, base64, hashlib
import urllib.parse, math, random, statistics
```

**Cú pháp khác với JavaScript**:
```python
# Python dùng _ thay vì $
items = _input.all()        # thay vì $input.all()
data = _json["body"]["name"]  # thay vì $json.body.name
node_data = _node["Webhook"]["json"]  # thay vì $node["Webhook"].json

# Trả về
return [{"json": {"result": value}}]
```

**Workarounds khi cần HTTP/pandas**:
- HTTP requests -> Dùng HTTP Request node trước Code node
- Data analysis -> Dùng module `statistics` hoặc chuyển sang JavaScript
- Web scraping -> Dùng HTTP Request + HTML Extract node

---

## 6. Quy Trình Xây Dựng Workflow

### Quy trình 4 giai đoạn:

```
PLANNING -> IMPLEMENTATION -> VALIDATION -> DEPLOYMENT
```

### Giai đoạn 1: Lập kế hoạch (Planning)
- [ ] Xác định pattern (webhook, API, database, AI, scheduled)
- [ ] Liệt kê các node cần thiết (`search_nodes`)
- [ ] Hiểu luồng dữ liệu (input -> transform -> output)
- [ ] Lên kế hoạch xử lý lỗi

### Giai đoạn 2: Triển khai (Implementation)
- [ ] Tạo workflow với trigger phù hợp
- [ ] Thêm data source nodes
- [ ] Cấu hình authentication/credentials
- [ ] Thêm transformation nodes (Set, Code, IF)
- [ ] Thêm output/action nodes
- [ ] Cấu hình xử lý lỗi

### Giai đoạn 3: Kiểm tra (Validation)
- [ ] Validate từng node (`validate_node` với `profile: "runtime"`)
- [ ] Validate toàn bộ workflow (`validate_workflow`)
- [ ] Test với dữ liệu mẫu
- [ ] Xử lý edge cases (dữ liệu rỗng, lỗi)

### Giai đoạn 4: Triển khai (Deployment)
- [ ] Review workflow settings
- [ ] Kích hoạt workflow (`activateWorkflow` operation)
- [ ] Giám sát các execution đầu tiên
- [ ] Ghi chép mục đích và luồng dữ liệu

### Ví dụ quy trình đầy đủ:

```javascript
// 1. Tìm nodes
search_nodes({query: "webhook"})
search_nodes({query: "slack"})

// 2. Hiểu cấu hình
get_node({nodeType: "nodes-base.webhook"})
get_node({nodeType: "nodes-base.slack"})

// 3. Tạo workflow
n8n_create_workflow({
  name: "Webhook to Slack",
  nodes: [...],
  connections: {...}
})

// 4. Validate
n8n_validate_workflow({id: "workflow-id"})

// 5. Sửa lỗi (lặp lại)
n8n_update_partial_workflow({
  id: "workflow-id",
  intent: "Fix validation errors",
  operations: [...]
})

// 6. Validate lại
n8n_validate_workflow({id: "workflow-id"})

// 7. Kích hoạt
n8n_update_partial_workflow({
  id: "workflow-id",
  intent: "Activate for production",
  operations: [{type: "activateWorkflow"}]
})
```

---

## 7. Các Pattern Workflow Chính

### Pattern 1: Webhook Processing (Phổ biến nhất - 35%)

```
Webhook -> [Validate] -> [Transform] -> Action -> [Respond]
```

**Ví dụ**: Form submission -> Validate -> Format -> Slack notification

```javascript
// Webhook node
{ path: "form-submit", httpMethod: "POST" }

// Truy cập dữ liệu - LUÔN dùng .body
text: "={{$json.body.name}} - {{$json.body.email}}"
```

### Pattern 2: HTTP API Integration

```
Trigger -> HTTP Request -> Transform -> Action -> Error Handler
```

**Ví dụ**: Fetch GitHub issues -> Transform -> Tạo Jira tickets

### Pattern 3: Database Operations

```
Schedule -> Query -> Transform -> Write -> Verify
```

**Ví dụ**: Postgres (read) -> Transform -> MySQL (write)

### Pattern 4: AI Agent Workflow

```
Trigger -> AI Agent (Model + Tools + Memory) -> Output
```

**8 loại AI connection**:
1. `ai_languageModel` - LLM (OpenAI, Anthropic)
2. `ai_tool` - Functions agent có thể gọi
3. `ai_memory` - Ngữ cảnh hội thoại
4. `ai_outputParser` - Parse structured outputs
5. `ai_embedding` - Vector embeddings
6. `ai_vectorStore` - Vector database
7. `ai_document` - Document loaders
8. `ai_textSplitter` - Text chunking

### Pattern 5: Scheduled Tasks

```
Schedule -> Fetch -> Process -> Deliver -> Log
```

**Ví dụ**: Hàng ngày 9AM -> Fetch analytics -> Tổng hợp -> Email báo cáo

### Data Flow Patterns:

```
Linear:     Trigger -> Transform -> Action -> End
Branching:  Trigger -> IF -> [True Path] / [False Path]
Parallel:   Trigger -> [Branch 1] -> Merge
                    -> [Branch 2] -/
Loop:       Trigger -> Split Batches -> Process -> Loop
Error:      Main Flow -> [Success] / [Error Trigger -> Handler]
```

---

## 8. Validation và Debug

### Validation Loop (pattern phổ biến nhất):

```
Cấu hình node
    |
validate_node (profile: "runtime")
    |
Đọc lỗi (23s suy nghĩ)
    |
Sửa lỗi (58s)
    |
validate_node lại
    |
Lặp lại 2-3 lần -> Valid!
```

### Các loại lỗi chính:

| Loại | Ý nghĩa | Cách sửa |
|------|---------|----------|
| `missing_required` | Thiếu field bắt buộc | Thêm field theo get_node |
| `invalid_value` | Giá trị không hợp lệ | Kiểm tra allowed values |
| `type_mismatch` | Sai kiểu dữ liệu | Chuyển đổi (string->number) |
| `invalid_expression` | Lỗi cú pháp expression | Kiểm tra `{{ }}` |
| `invalid_reference` | Node tham chiếu không tồn tại | Sửa tên node |

### Recovery Strategies:

1. **Start Fresh**: Khi config bị hỏng nặng -> tạo config tối giản mới
2. **Binary Search**: Workflow chạy sai -> xóa 1/2 node, test, tìm lỗi
3. **Clean Stale Connections**: Lỗi "Node not found" -> `cleanStaleConnections`
4. **Auto-fix**: Lỗi operator -> `n8n_autofix_workflow`

### False Positives (cảnh báo có thể bỏ qua):

- "Missing error handling" -> Chấp nhận cho workflow đơn giản
- "No retry logic" -> Chấp nhận cho API có retry riêng
- "Missing rate limiting" -> Chấp nhận cho internal API
- Dùng `profile: "ai-friendly"` để giảm false positives

---

## 9. AI Agent Workflow

### Kiến trúc cơ bản:

```
Trigger (Webhook/Manual)
    |
    v
AI Agent Node
    |--- ai_languageModel --> OpenAI/Anthropic/Gemini
    |--- ai_tool --> HTTP Request Tool
    |--- ai_tool --> Database Tool
    |--- ai_tool --> Code Tool
    |--- ai_memory --> Window Buffer Memory
    |
    v
Output (Webhook Response/Slack/Email)
```

### Cấu hình kết nối AI:

```javascript
// Kết nối Language Model
{
  type: "addConnection",
  source: "OpenAI Chat Model",
  target: "AI Agent",
  sourceOutput: "ai_languageModel"
}

// Kết nối Tool
{
  type: "addConnection",
  source: "HTTP Request Tool",
  target: "AI Agent",
  sourceOutput: "ai_tool"
}

// Kết nối Memory
{
  type: "addConnection",
  source: "Window Buffer Memory",
  target: "AI Agent",
  sourceOutput: "ai_memory"
}
```

### Bảo mật cho AI Agent:

- [ ] Database tool: CHỈ dùng read-only user
- [ ] Validate input trước khi cho AI xử lý
- [ ] Giới hạn độ dài input (max 1000 chars)
- [ ] Rate limiting cho webhook
- [ ] Giám sát tool usage

### Agent Types:

| Type | Khi nào dùng |
|------|-------------|
| Conversational Agent | Chat, hỗ trợ khách hàng (phổ biến nhất) |
| OpenAI Functions Agent | Nhiều tools, cần structured output |
| ReAct Agent | Suy luận nhiều bước, debug |

---

## 10. Code Node - JavaScript và Python

### So sánh nhanh:

| Tính năng | JavaScript | Python |
|-----------|-----------|--------|
| Khuyến nghị | 95% trường hợp | 5% trường hợp |
| HTTP requests | `$helpers.httpRequest()` | KHÔNG CÓ (dùng HTTP node) |
| DateTime | Luxon (mạnh mẽ) | `datetime` (cơ bản) |
| External libs | Không cần | KHÔNG HỖ TRỢ |
| Cú pháp truy cập | `$json.body.name` | `_json["body"]["name"]` |
| Trả về | `[{json: {...}}]` | `[{"json": {...}}]` |
| Safe access | `item?.json?.field` | `item.get("json", {}).get("field")` |

### Template JavaScript:

```javascript
// Run Once for All Items (khuyến nghị)
const items = $input.all();

if (!items || items.length === 0) return [];

return items
  .filter(item => item.json.status === 'active')
  .map(item => ({
    json: {
      id: item.json.id,
      name: item.json.name,
      processed: true,
      timestamp: DateTime.now().toFormat('yyyy-MM-dd HH:mm')
    }
  }));
```

### Template Python:

```python
# Run Once for All Items
items = _input.all()

if not items:
    return []

return [
    {
        "json": {
            "id": item["json"].get("id"),
            "name": item["json"].get("name", "Unknown"),
            "processed": True
        }
    }
    for item in items
    if item["json"].get("status") == "active"
]
```

---

## 11. Best Practices và Lỗi Thường Gặp

### NÊN làm:

- Xây dựng workflow **từng bước** (trung bình 56s giữa mỗi edit)
- Validate **sau mỗi thay đổi quan trọng**
- Dùng `get_node(detail: "standard")` trước (95% đủ dùng)
- Chỉ định validation profile (`runtime` khuyến nghị)
- Dùng smart parameters (`branch`, `case`) cho rõ ràng
- Luôn thêm `intent` parameter khi update workflow
- Test với dữ liệu mẫu trước khi activate
- Dùng `includeExamples: true` để lấy config thực tế

### KHÔNG NÊN:

- KHÔNG xây workflow một lần (iterate!)
- KHÔNG dùng `detail: "full"` khi không cần thiết (tốn token)
- KHÔNG quên prefix nodeType (`nodes-base.*` vs `n8n-nodes-base.*`)
- KHÔNG bỏ qua validation trước khi activate
- KHÔNG dùng expressions `{{ }}` trong Code node
- KHÔNG truy cập webhook data trực tiếp (`$json.name` -> `$json.body.name`)
- KHÔNG hardcode credentials trong parameters
- KHÔNG deploy mà không test

### Top 10 Lỗi Thường Gặp:

| # | Lỗi | Nguyên nhân | Cách sửa |
|---|-----|-------------|----------|
| 1 | Webhook data undefined | Thiếu `.body` | `$json.body.name` |
| 2 | Node not found | Sai nodeType format | Kiểm tra prefix |
| 3 | Expression hiện literal text | Thiếu `{{ }}` | Thêm `{{ }}` |
| 4 | Code node crash | Thiếu return | Thêm `return [...]` |
| 5 | Sai return format | Object thay vì array | `[{json: ...}]` |
| 6 | Validation loop | Không đọc lỗi kỹ | Đọc message + fix |
| 7 | Python import error | External library | Chỉ dùng standard lib |
| 8 | Node name mismatch | Case sensitive | Kiểm tra tên chính xác |
| 9 | Expression trong Code | Dùng `{{ }}` trong code | Dùng JS trực tiếp |
| 10 | Missing singleValue | Operator structure | Auto-sanitization sẽ sửa |

---

## 12. Checklist Triển Khai

### Trước khi bắt đầu:

- [ ] n8n instance đang chạy và có thể truy cập
- [ ] Có n8n API Key
- [ ] n8n-mcp đã cài đặt và cấu hình
- [ ] `.mcp.json` đã đúng
- [ ] Skills đã cài đặt (7 skills)
- [ ] Test kết nối MCP thành công

### Khi xây dựng workflow:

- [ ] Xác định pattern phù hợp
- [ ] Tìm nodes cần thiết (`search_nodes`)
- [ ] Hiểu cấu hình node (`get_node` standard)
- [ ] Tạo workflow (`n8n_create_workflow`)
- [ ] Cấu hình từng node với đúng operation
- [ ] Kiểm tra expressions (`{{ }}` đúng chỗ, `.body` cho webhook)
- [ ] Validate từng node (`validate_node` runtime)
- [ ] Validate toàn bộ (`validate_workflow`)
- [ ] Test với dữ liệu thực

### Trước khi deploy:

- [ ] Tất cả validation errors đã fix
- [ ] Warnings đã review (chấp nhận hoặc fix)
- [ ] Error handling đã cấu hình
- [ ] Credentials đúng cách (không hardcode)
- [ ] Test thành công với dữ liệu mẫu
- [ ] Workflow đã activate (`activateWorkflow`)
- [ ] Giám sát execution đầu tiên

### Sau khi deploy:

- [ ] Kiểm tra execution logs
- [ ] Giám sát hiệu suất
- [ ] Thiết lập cảnh báo lỗi
- [ ] Lưu version (`n8n_workflow_versions`)
- [ ] Ghi chép workflow (notes field)

---

## Tài Liệu Tham Khảo

- **Repository**: https://github.com/czlonkowski/n8n-skills
- **n8n-mcp**: https://github.com/czlonkowski/n8n-mcp
- **n8n Docs**: https://docs.n8n.io/
- **Video giới thiệu**: https://youtu.be/e6VvRqmUY2Y

---

> Conceived by Romuald Członkowski - [www.aiadvisors.pl/en](https://www.aiadvisors.pl/en)
>
> Tài liệu hướng dẫn triển khai tạo bởi Claude Code dựa trên phân tích chi tiết 7 skills trong n8n-skills repository.
