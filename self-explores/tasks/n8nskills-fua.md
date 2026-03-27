---
date: 2026-03-26
type: task-worklog
task: n8nskills-fua
title: "Deep Dive: n8n-mcp-tools-expert"
status: open
detailed_at: 2026-03-26
detail_score: ready-for-dev
tags: [n8n, skills, research]
---

# Deep Dive: n8n-mcp-tools-expert [P0] — Detailed Design

## 1. Objective
Perform a comprehensive deep-dive analysis of the n8n-mcp-tools-expert skill — the highest-priority skill that teaches AI assistants how to use n8n-mcp MCP tools effectively. Analyze the unified API design, tool selection logic, 6 common mistakes, and performance characteristics.

## 2. Scope
**In-scope:**
- Full analysis of SKILL.md (~643 lines) — structure, content density, guidance quality
- SEARCH_GUIDE.md — node discovery patterns, search strategies, result interpretation
- VALIDATION_GUIDE.md — validation modes, profiles, error handling
- WORKFLOW_GUIDE.md — workflow CRUD, partial updates, deployment, versioning
- Unified API analysis: `get_node`, `validate_node`, `search_nodes` parameter design
- Tool selection decision tree: when to use which tool
- The 6 documented mistakes and their prevention patterns
- Performance analysis using telemetry (38,287 uses, 18s patterns)
- Scoring on completeness/usability/modularity/safety (1-10)

**Out-of-scope:**
- Testing actual MCP tool calls against a live n8n instance
- Modifying the skill content
- Comparing with other MCP server implementations
- Analyzing the n8n-mcp server source code

## 3. Input / Output
**Input:**
- `skills/n8n-mcp-tools-expert/SKILL.md` (~643 lines)
- `skills/n8n-mcp-tools-expert/SEARCH_GUIDE.md`
- `skills/n8n-mcp-tools-expert/VALIDATION_GUIDE.md`
- `skills/n8n-mcp-tools-expert/WORKFLOW_GUIDE.md`
- `self-explores/learn/overview/analysis.md` (overview context from n8nskills-10p)
- `CLAUDE.md` (telemetry: 38,287 update_partial uses, search->get_node 18s, validate loop 7,841x)

**Output:**
- `self-explores/learn/n8n-mcp-tools-expert/analysis.md` — deep-dive analysis with: Score 1-10, Uu/Nhuoc, Tich hop, Technical Decisions

## 4. Dependencies
- **n8nskills-10p** (provides overview context, but this P0 task can start reading files in parallel — only needs 10p for cross-referencing scores)

## 5. Flow xu ly

### Step 1: Read SKILL.md Structure Analysis (~10 min)
Read `skills/n8n-mcp-tools-expert/SKILL.md` completely. Document:
- Section hierarchy (H1, H2, H3 headings)
- Frontmatter: name, description, triggers, version
- Tool coverage: which MCP tools are documented, which are missing
- Decision trees or flowcharts for tool selection
- The 6 documented common mistakes (list each with severity)
- Cross-references to other skills
- Code examples count and quality

**Verify:** Section outline matches expected structure. Count total tools documented vs total available in n8n-mcp (from CLAUDE.md tool list: get_node, validate_node, search_nodes, n8n_update_partial_workflow, n8n_create_workflow, n8n_validate_workflow, n8n_autofix_workflow, n8n_deploy_template, n8n_workflow_versions, search_templates, get_template, tools_documentation, ai_agents_guide, n8n_test_workflow, n8n_executions).

### Step 2: Read Reference Files (~15 min)
Read all 3 reference files:

**SEARCH_GUIDE.md:**
- Search strategies (keyword, category, capability-based)
- Result interpretation patterns
- `search_nodes` parameter usage
- `get_node` detail levels (minimal, standard, full) and modes (info, docs, search_properties, versions)
- Common search mistakes

**VALIDATION_GUIDE.md:**
- Validation modes (minimal, full)
- Validation profiles (runtime, ai-friendly, strict)
- `validate_node` vs `validate_workflow` decision
- Error interpretation patterns
- Integration with auto-fix

**WORKFLOW_GUIDE.md:**
- `n8n_create_workflow` patterns
- `n8n_update_partial_workflow` — 17 operation types including `activateWorkflow`
- Incremental update strategy
- `n8n_deploy_template` usage
- `n8n_workflow_versions` for history and rollback
- Testing and execution management

**Verify:** All 3 files read completely. Note line counts and content density for each.

### Step 3: Analyze Unified API Design (~10 min)
Examine how the skill teaches the unified API pattern:
- `get_node` unification: How does it teach switching between detail levels and modes?
- `validate_node` unification: How does it teach choosing between profiles?
- `search_nodes` unification: How does it teach multi-mode search?
- Parameter documentation completeness: Are all parameters explained with examples?
- Error response handling: Does the skill teach how to interpret MCP error responses?

Map the API surface:
```
get_node(name, detail_level=[minimal|standard|full], mode=[info|docs|search_properties|versions])
validate_node(name, config, mode=[minimal|full], profile=[runtime|ai-friendly|strict])
search_nodes(query, mode=[keyword|...])
```

**Verify:** API parameter matrix is complete. No undocumented parameters or modes found.

### Step 4: Score and Document Analysis (~15 min)
Score the skill 1-10 on:
- **Completeness (target: 8+):** Does it cover all 15 MCP tools? All parameter combinations?
- **Usability (target: 8+):** Can an AI follow the tool selection logic without ambiguity?
- **Modularity (target: 7+):** Does it cleanly interface with validation-expert and node-configuration skills?
- **Safety (target: 8+):** Does it prevent the 6 common mistakes? Are there guardrails?

Document:
- **Uu (Strengths):** Best aspects of the skill design
- **Nhuoc (Weaknesses):** Gaps, ambiguities, missing coverage
- **Tich hop (Integration):** How it connects to other 6 skills
- **Technical Decisions:** Why the unified API? Why these 6 mistakes? Why this tool selection order?

**Verify:** All 4 scores assigned with evidence. Uu/Nhuoc each have 3+ items.

### Step 5: Write Deep-Dive Output (~10 min)
Create `self-explores/learn/n8n-mcp-tools-expert/analysis.md` with:
- Metadata header
- Executive summary (skill purpose, why P0)
- Structural analysis (section map, line distribution)
- Unified API deep-dive (parameter matrices, mode explanations)
- Tool selection decision tree (reconstructed from SKILL.md)
- The 6 mistakes analysis (severity, prevention, detection)
- Performance analysis (telemetry mapping)
- Scoring table with evidence
- Uu / Nhuoc / Tich hop sections
- Technical Decisions section
- Recommendations

**Verify:** Output file exists, scores present, all 3 reference files cited, telemetry data incorporated.

## 6. Edge Cases & Error Handling
| Case | Trigger | Expected | Recovery |
|------|---------|----------|----------|
| SKILL.md documents fewer than 6 mistakes | Mistake count changed since CLAUDE.md written | Document actual count found | Note discrepancy in analysis |
| Reference file contradicts SKILL.md | Different parameter names or modes | Flag contradiction with file:line references | Recommend which source is authoritative |
| Unified API has undocumented modes | New modes added since skill written | List as "discovered undocumented" | Score completeness lower |
| Telemetry doesn't match skill guidance | Skill recommends pattern X but telemetry shows pattern Y is more common | Document gap between theory and practice | Recommend skill update |
| 10p overview not available yet | Dependency partially unmet | Proceed with file analysis, skip cross-referencing | Add cross-references in review pass |

## 7. Acceptance Criteria
- **Happy 1:** Given SKILL.md and 3 reference files are read, When analysis completes, Then output contains unified API parameter matrix for all 3 unified tools (get_node, validate_node, search_nodes)
- **Happy 2:** Given telemetry shows 38,287 update_partial uses and 18s search->get_node pattern, When performance section is written, Then it cites both data points with interpretation
- **Negative:** Given a reference file is missing or empty, When reading fails, Then analysis notes the gap and scores completeness accordingly (max 6/10 with missing reference)

## 8. Technical Notes
- This is the P0 (highest priority) skill because it teaches the foundational MCP tool usage that all other skills build upon
- The 38,287 uses of `n8n_update_partial_workflow` at 99% success rate suggests the WORKFLOW_GUIDE.md content is battle-tested
- The 18s average between `search_nodes` and `get_node` calls indicates this is a well-established pattern
- The v1.1.0 commit (d9c2872) updated skills for "n8n-mcp unified tool API" — this is the current version being analyzed
- 17 operation types in `n8n_update_partial_workflow` is a significant API surface — check if all are documented

## 9. Risks
- **Most complex skill to analyze**: 643 lines + 3 reference files = highest content volume in the collection. Mitigation: Structured section-by-section reading with notes.
- **Unified API nuance**: The detail levels and modes create a parameter matrix that may be hard to fully catalog. Mitigation: Build explicit parameter table during analysis.
- **Telemetry interpretation**: Usage counts don't indicate whether the skill guidance led to those patterns. Mitigation: Note correlation vs causation in analysis.

## Worklog
*(Chua bat dau)*
