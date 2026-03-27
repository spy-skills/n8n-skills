---
date: 2026-03-26
type: learning
task: n8nskills-10p
tags: [n8n, skills, overview, analysis, scoring]
---

# Phân tích Tổng quan: 7 n8n Skills

## Phân nhóm theo lĩnh vực

### 1. Tool Usage
- **n8n-mcp-tools-expert** (~643L + 3 refs) — Hub skill, dạy cách dùng MCP tools (search, validate, workflow management). Orchestrator cho toàn bộ skill set.
- **n8n-node-configuration** (~786L + 2 refs) — Dùng `get_node` detail levels + `validate_node` để configure nodes operation-aware. Phụ thuộc mạnh vào MCP tools.

### 2. Validation
- **n8n-validation-expert** (~690L + 2 refs) — Chuyên gia validation errors, profiles, false positives, auto-sanitization. Domain expert cho mọi thứ có thể sai khi validate.

### 3. Patterns & Syntax
- **n8n-workflow-patterns** (~412L + 5 refs) — 5 architectural patterns (webhook, HTTP API, database, AI agent, scheduled). Hướng dẫn cấu trúc workflow.
- **n8n-expression-syntax** (~517L + 2 refs) — Syntax `{{ }}` expressions, `$json`, `$node`, `$now`, webhook `.body` gotcha. Hẹp nhưng thiết yếu.

### 4. Code Writing
- **n8n-code-javascript** (~700L + 4 refs) — JavaScript Code node: `$input` API, return format, `$helpers`, DateTime, error patterns.
- **n8n-code-python** (~750L + 4 refs) — Python Code node: `_input` API, standard library only (no external packages), `.get()` safety pattern.

## Đánh giá (Score 1-10)

| Skill | Completeness | Usability | Modularity | Safety | AVG |
|-------|:---:|:---:|:---:|:---:|:---:|
| n8n-mcp-tools-expert | 9 | 9 | 7 | 8 | **8.3** |
| n8n-validation-expert | 9 | 8 | 8 | 9 | **8.5** |
| n8n-workflow-patterns | 8 | 9 | 9 | 7 | **8.3** |
| n8n-node-configuration | 8 | 8 | 7 | 8 | **7.8** |
| n8n-expression-syntax | 8 | 9 | 9 | 8 | **8.5** |
| n8n-code-javascript | 9 | 9 | 9 | 9 | **9.0** |
| n8n-code-python | 8 | 8 | 9 | 9 | **8.5** |
| **OVERALL** | **8.4** | **8.6** | **8.3** | **8.3** | **8.4** |

## Ưu & Nhược điểm

### n8n-mcp-tools-expert (AVG 8.3)
**Ưu:**
- Telemetry-driven guidance (38K uses, 56s avg between edits) — dữ liệu thực tế, rất hiếm trong skills
- nodeType format disambiguation — giải quyết lỗi phổ biến nhất
- Complete tool lifecycle coverage (search → deploy → activate → monitor)

**Nhược:**
- 643 lines + lặp lại khái niệm nodeType ở 4+ chỗ
- Thiếu error response examples cho API tools
- Template library guidance mỏng

### n8n-validation-expert (AVG 8.5)
**Ưu:**
- False positives decision framework — flowchart cho "có nên fix warning này?"
- Known n8n issues documentation (#304, #306, #338)
- 4 recovery strategies (start fresh, binary search, clean stale, auto-fix)

**Nhược:**
- Overlap đáng kể với MCP tools expert
- Thiếu real validation output examples
- Operator structure section lặp 3-4 lần

### n8n-workflow-patterns (AVG 8.3)
**Ưu:**
- Pattern selection guide với scenario examples
- Per-pattern checklists (planning, implementation, validation, deployment)
- Statistics grounding (35% webhook, 28% schedule)

**Nhược:**
- Security guidance hời hợt (webhook signature, IP whitelisting)
- AI agent pattern incomplete (thiếu token cost, prompt injection)
- Không có cross-pattern composition (VD: scheduled + webhook)

### n8n-node-configuration (AVG 7.8)
**Ưu:**
- displayOptions deep dive (field visibility system)
- Slack operation matrix (fields per operation)
- Progressive discovery decision tree

**Nhược:**
- Chỉ cover 20 nodes / 525+ trong ecosystem
- Phụ thuộc nặng vào external tools
- Thiếu versioning guidance

### n8n-expression-syntax (AVG 8.5)
**Ưu:**
- Webhook `.body` gotcha elevated to CRITICAL status
- "When NOT to use expressions" section
- 15 common mistakes với exact error messages

**Nhược:**
- Advanced expressions (multi-line, conditional chains) mỏng
- Luxon/DateTime in expressions underserved
- Thiếu expression debugging workflow

### n8n-code-javascript (AVG 9.0 — HIGHEST)
**Ưu:**
- Error frequency percentages (38% empty code/missing return)
- 10 production patterns với 3-4 variations mỗi cái
- Complete built-in functions reference

**Nhược:**
- COMMON_PATTERNS.md quá dài (1110 lines) — cần summary table
- Thiếu async/await patterns (Promise.all, rate-limited calls)
- Error propagation to downstream nodes not covered

### n8n-code-python (AVG 8.5)
**Ưu:**
- Honest "JavaScript First" recommendation
- Standard library workarounds table (need X, use Y)
- `.get()` safety pattern drilled consistently

**Nhược:**
- "95% use JavaScript" framing lặp quá nhiều lần
- Không giải thích runtime (Pyodide? subprocess?)
- Missing advanced Python patterns

## Khả năng tích hợp vào hệ thống hiện tại

### Layered Architecture
```
Layer 4 (Code):     code-javascript  |  code-python
Layer 3 (Syntax):   expression-syntax
Layer 2 (Config):   node-configuration  |  validation-expert
Layer 1 (Patterns): workflow-patterns
Layer 0 (Tools):    mcp-tools-expert
```

### Strong Integration Points
- Webhook `.body` gotcha: xuất hiện trong 5/7 skills — cross-cutting concern tốt
- Validation loop (validate → think 23s → fix 58s): consistent across 3 skills
- Code skills correctly distinguish expressions vs direct JS/Python access
- Workflow patterns reference đúng skills khác ở mỗi phase

### Weak Integration Points
- AI agent pattern thiếu reference đến `ai_agents_guide()` tool
- n8n-node-config vs n8n-mcp-tools overlap trên `get_node` detail levels
- Code skills không reference validation expert

### Inconsistencies Found
1. scheduled_tasks.md nói "API/MCP cannot activate" nhưng MCP tools documents `activateWorkflow` operation — **MÂU THUẪN TRỰC TIẾP**
2. validation expert dùng `validate_node_operation()` vs MCP tools dùng `validate_node()` — naming mismatch
3. Error frequency percentages trong code-JS chỉ sum 57%, thiếu 43%

## Ghi chú kỹ thuật (Technical Decisions)

1. **Telemetry-driven prioritization** — Dùng dữ liệu thực (38K uses, 7.8K validate cycles) để structure content. Hiếm thấy trong prompt engineering.
2. **Progressive disclosure** — Mirror tool API design (standard vs full detail). 7 skills thay vì 1 monolithic skill.
3. **Consistent file structure** — Mọi skill: SKILL.md + N reference files. Code skills mirror nhau.
4. **Anti-pattern sections** — Explicit "Don't" sections. Effective prompt engineering technique.
5. **Dual nodeType format** — Identified critical usability issue, made first-class concept.
6. **Python honesty** — Recommend JavaScript trong Python skill. Bold but correct.
7. **Version-aware** — Documents v1.1.0 unified API including activateWorkflow, deploy_template.

## Gaps (Không covered ở bất kỳ skill nào)
- Credential management
- Sub-workflow patterns (executeWorkflow node)
- Binary data handling ($binary)
- Error trigger workflows
- n8n versioning and migration
- Testing strategies
- Performance at scale (concurrency, timeouts, memory)

## So sánh Methodology với Marketing-Skills (8/10)

| Dimension | n8n-skills | marketing-skills |
|-----------|-----------|-----------------|
| Format quality | Structured frontmatter, consistent sections, tables | Good but less rigorous |
| Depth | 1,110 lines deepest, telemetry data | 357 lines deepest |
| Modularity | 7 thick skills + 30 files | 26 thin skills |
| Unique patterns | Telemetry-driven, anti-patterns, error frequencies | Product context file |
| **Score** | **9/10** | **8/10** |

n8n-skills vượt trội ở depth, technical precision, integration design. Yếu hơn ở breadth (7 vs 26) và có redundancy giữa skills.
