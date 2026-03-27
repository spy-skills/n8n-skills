---
date: 2026-03-26
type: learning
task: n8nskills-ykj
tags: [n8n, skills, priority, ranking, learning-path]
---

# Xếp hạng ưu tiên Skills (1-5)

## Bảng xếp hạng

| Rank | Skill | Priority | Practical | Frequency | Cross-dep | Rationale |
|------|-------|----------|-----------|-----------|-----------|-----------|
| 1 | n8n-mcp-tools-expert | 5/5 | 5/5 | 5/5 | 5/5 | Layer 0 — mọi skill đều phụ thuộc. 38,287 uses (tool phổ biến nhất). Không biết MCP tools = không làm được gì. |
| 2 | n8n-workflow-patterns | 5/5 | 4/5 | 4/5 | 5/5 | Layer 1 — structural foundation. 35% webhook + 28% schedule = 63% workflows cần patterns này. Định hình mọi quyết định sau đó. |
| 3 | n8n-validation-expert | 4/5 | 5/5 | 5/5 | 4/5 | 7,841 validation cycles (23s thinking + 58s fixing). Mỗi workflow đều validate. False positive handling tránh lãng phí thời gian. |
| 4 | n8n-node-configuration | 4/5 | 4/5 | 4/5 | 3/5 | Mỗi node cần configure đúng. Phụ thuộc nặng vào MCP tools (`get_node` detail levels). 525+ nodes trong ecosystem. |
| 5 | n8n-expression-syntax | 3/5 | 5/5 | 3/5 | 3/5 | Cross-cutting concern (xuất hiện 5/7 skills). Webhook `.body` gotcha = CRITICAL. Hẹp nhưng sai = workflow hỏng hoàn toàn. |
| 6 | n8n-code-javascript | 3/5 | 4/5 | 2/5 | 2/5 | Score cao nhất (9.0) nhưng chỉ cần khi Code node được dùng. 38% lỗi = empty code/missing return — skill này phòng tránh hiệu quả. |
| 7 | n8n-code-python | 2/5 | 3/5 | 1/5 | 2/5 | Skill tự thừa nhận "95% use JavaScript". Standard library only. Chỉ cần khi user yêu cầu Python explicitly. |

## Learning Path (Recommended Sequence)

### Phase 1: Foundation (~45 min)

**Goal**: Biết cách tìm nodes, gọi tools, và chọn kiến truc workflow.

1. **n8n-mcp-tools-expert** (~25 min) — Nắm unified tool API: `search_nodes`, `get_node` (detail levels), `validate_node`, `n8n_update_partial_workflow`. Hiểu nodeType format (`n8n-nodes-base.slack` vs `@n8n/n8n-nodes-langchain.agent`). Đây là prerequisite cho mọi thứ còn lại.

2. **n8n-workflow-patterns** (~20 min) — Nắm 5 patterns và khi nào dùng cái nào. Focus vào webhook (35%) và schedule (28%) vì chiếm 63% real-world workflows. Pattern selection guide + per-pattern checklists.

**Checkpoint**: Có thể search node, chọn pattern, tạo workflow skeleton bằng MCP tools.

### Phase 2: Building (~40 min)

**Goal**: Configure nodes chính xác, validate liên tục, viết expressions đúng.

3. **n8n-validation-expert** (~15 min) — Nắm validation profiles (runtime, ai-friendly, strict), false positive framework, 4 recovery strategies. Pattern quan trọng nhất: `n8n_update_partial_workflow` -> `n8n_validate_workflow` (7,841 lần, avg 23s+58s).

4. **n8n-node-configuration** (~15 min) — Operation-aware configuration, `displayOptions` system, progressive discovery decision tree. Kết hợp với `get_node` detail levels đã học ở Phase 1.

5. **n8n-expression-syntax** (~10 min) — `{{ }}` patterns, `$json`, `$node`, `$now`. CRITICAL: webhook data nằm ở `$json.body`. 15 common mistakes. "When NOT to use expressions."

**Checkpoint**: Có thể build complete workflow: đúng pattern, nodes configured, expressions đúng, validation pass.

### Phase 3: Advanced (~30 min)

**Goal**: Custom logic khi built-in nodes không đủ.

6. **n8n-code-javascript** (~20 min) — `$input` API, return format `[{json: {...}}]`, `$helpers` (HTTP, binary), DateTime (Luxon). 10 production patterns. Focus error prevention (38% = empty code/missing return).

7. **n8n-code-python** (~10 min) — Chỉ khi cần. `_input` API (underscore prefix), standard library only, `.get()` safety pattern. Biết khi nào recommend JavaScript thay vì Python.

**Checkpoint**: Có thể handle mọi scenario: built-in nodes, expressions, Code node JS/Python.

## Chi tiết đánh giá từng skill

### 1. n8n-mcp-tools-expert (Rank 1, Priority 5/5)
Layer 0 trong dependency graph -- literally mọi skill khac reference MCP tools. Telemetry cho thay `n8n_update_partial_workflow` co 38,287 uses voi 99.0% success rate, va pattern `search_nodes -> get_node` la common nhat (18s avg gap). Khong nắm skill nay thi khong the thực hien bat ky thao tac workflow nao. Diem yếu duy nhat: 643 lines hoi dai va co lap khái niem nodeType o 4+ cho, nhung day la trade-off chap nhan duoc cho completeness. Priority tuyệt doi.

### 2. n8n-workflow-patterns (Rank 2, Priority 5/5)
Layer 1 — moi workflow can mot pattern. Du lieu thuc te cho thay 35% webhook + 28% schedule = 63% workflows thuoc 2 patterns chinh. Skill nay cung cap pattern selection guide, per-pattern checklists (planning -> implementation -> validation -> deployment), va statistics grounding. Nhuoc diem la security guidance mong (webhook signature, IP whitelisting) va AI agent pattern chua complete, nhung cho foundation thi day du. Hoc ngay sau MCP tools vi no dinh hinh structure cho moi quyet dinh tiep theo.

### 3. n8n-validation-expert (Rank 3, Priority 4/5)
7,841 validation cycles trong telemetry — moi workflow deu phai validate. Skill nay co false positive decision framework (flowchart "co nen fix warning nay?"), 4 recovery strategies (start fresh, binary search, clean stale, auto-fix), va documents known n8n issues (#304, #306, #338). Critical vi validation loop (23s thinking + 58s fixing) la constant companion khi build. Dat truoc node-configuration vi bạn can biet cach interpret errors truoc khi di sau vao configuration.

### 4. n8n-node-configuration (Rank 4, Priority 4/5)
Moi node can configure dung, nhung skill nay phu thuoc nang vao MCP tools (`get_node` detail levels) — nen phai hoc sau Layer 0. Co `displayOptions` deep dive (field visibility system), Slack operation matrix, va progressive discovery decision tree. Diem yeu lon nhat: chi cover ~20 nodes / 525+ trong ecosystem, nen phan lon van phai dua vao `get_node` dynamic lookup. Score thap nhat (7.8) la hop ly.

### 5. n8n-expression-syntax (Rank 5, Priority 3/5)
Cross-cutting concern xuat hien 5/7 skills. Webhook `.body` gotcha duoc elevate thanh CRITICAL status la dung — sai cho nay = workflow hỏng am tham (nhan `undefined` thay vi data). 15 common mistakes voi exact error messages rat thuc te. Tuy nhien, scope hep (chi expressions), advanced patterns (multi-line, conditional chains) mong, va Luxon/DateTime underserved. Priority 3 vi ban co the hoc incrementally khi gap — khong can master truoc khi bat dau build.

### 6. n8n-code-javascript (Rank 6, Priority 3/5)
Score cao nhat toan bo (9.0) nhung priority thap hon vi Code node chi can khi built-in nodes khong du. Error frequency percentages (38% empty code/missing return) la insight cuc ky huu ich. 10 production patterns voi 3-4 variations moi cai la comprehensive. Nhung COMMON_PATTERNS.md 1110 lines qua dai va thieu async/await patterns. Hoc khi bat dau can custom logic, khong can truoc do.

### 7. n8n-code-python (Rank 7, Priority 2/5)
Skill tu thua nhan "95% use JavaScript" — va dieu nay dung. Standard library only (no external packages) la constraint lon. Chi can khi user explicitly yeu cau Python hoac khi co python-specific logic (regex-heavy, math-heavy). `.get()` safety pattern va standard library workarounds table la huu ich nhung niche. Dat cuoi cung la hop ly.

## Key Takeaways

- **MCP tools expert la non-negotiable** — Layer 0, 38K uses, moi thu phu thuoc. Hoc dau tien, khong co ngoai le.
- **Workflow patterns truoc configuration** — Chon kien truc dung (Layer 1) truoc khi di vao chi tiet nodes (Layer 2). Sai pattern = refactor toan bo.
- **Validation la companion, khong phai afterthought** — 7,841 cycles cho thay validate lien tuc, khong phai validate 1 lan cuoi. Hoc false positive framework som de tranh validation loops.
- **Expressions la cross-cutting concern** — Xuat hien 5/7 skills. Webhook `.body` gotcha alone justify hoc skill nay. Nhung scope du hep de hoc nhanh (~10 min).
- **Code skills la last resort, khong phai default** — Score cao (9.0 JS, 8.5 Python) nhung priority thap vi n8n philosophy la no-code first. Chi dung khi built-in nodes that su khong lam duoc.
- **Python skill chi khi explicitly needed** — "JavaScript First" recommendation trong chinh skill Python la honest va dung. Khong can invest thoi gian tru khi co use case cu the.
- **Total learning time ~115 min** — Chia 3 phases, moi phase co checkpoint. Khong can hoc het 1 luc — Phase 1 (45 min) du de bat dau build workflows.
