---
date: 2026-03-26
type: task-worklog
task: n8nskills-6xb
title: "Deep Dive: n8n-workflow-patterns"
status: open
detailed_at: 2026-03-26
detail_score: ready-for-dev
tags: [n8n, skills, research]
---

# Deep Dive: n8n-workflow-patterns [P1] — Detailed Design

## 1. Objective
Perform a deep-dive analysis of the n8n-workflow-patterns skill — the architectural skill that teaches AI assistants 5 proven workflow patterns, data flow design, creation checklists, and pattern selection decision trees.

## 2. Scope
**In-scope:**
- Full analysis of SKILL.md (~412 lines) — pattern overview, selection guidance, creation checklist
- 5 pattern reference files: webhook_processing, http_api_integration, database_operations, ai_agent_workflow, scheduled_tasks
- Per-pattern analysis: trigger type, node chain, data flow, error handling, testing approach
- Pattern selection decision tree reconstruction
- Creation checklist completeness assessment
- Cross-pattern comparison (shared elements, unique requirements)
- Scoring on completeness/usability/modularity/safety (1-10)

**Out-of-scope:**
- Building actual workflows from patterns
- Testing patterns against live n8n instance
- Creating new patterns beyond the documented 5
- Comparing with n8n official template library

## 3. Input / Output
**Input:**
- `skills/n8n-workflow-patterns/SKILL.md` (~412 lines)
- `skills/n8n-workflow-patterns/webhook_processing.md`
- `skills/n8n-workflow-patterns/http_api_integration.md`
- `skills/n8n-workflow-patterns/database_operations.md`
- `skills/n8n-workflow-patterns/ai_agent_workflow.md`
- `skills/n8n-workflow-patterns/scheduled_tasks.md`
- `self-explores/learn/overview/analysis.md` (overview context from n8nskills-10p)

**Output:**
- `self-explores/learn/n8n-workflow-patterns/analysis.md` — deep-dive analysis with: Score 1-10, Uu/Nhuoc, Tich hop, Technical Decisions

## 4. Dependencies
- **n8nskills-10p** (provides overview context and initial scoring baseline)

## 5. Flow xu ly

### Step 1: Read SKILL.md Architecture Analysis (~8 min)
Read `skills/n8n-workflow-patterns/SKILL.md` completely (~412 lines). Document:
- Section structure and content distribution
- Frontmatter metadata (triggers, activation patterns)
- Pattern overview/summary for all 5 patterns
- Pattern selection guidance (decision tree or criteria)
- Creation checklist (steps for building any workflow)
- Cross-references to other skills (node-configuration, expression-syntax, etc.)
- Quality of architectural guidance (clarity, completeness)

**Verify:** All 5 patterns mentioned in SKILL.md. Selection criteria documented. Checklist found.

### Step 2: Read All 5 Pattern Reference Files (~20 min)
Read each pattern file and document per-pattern:

**webhook_processing.md:**
- Trigger type (Webhook node)
- Typical node chain (webhook -> process -> respond)
- Data flow (request body, headers, query params)
- Authentication patterns
- Error handling (response codes, timeouts)
- Testing approach (manual trigger, curl examples)

**http_api_integration.md:**
- Trigger type (various: manual, schedule, webhook)
- HTTP Request node configuration
- Authentication (OAuth, API key, Bearer)
- Pagination handling
- Rate limiting and retry logic
- Error handling (HTTP status codes, timeouts)

**database_operations.md:**
- Supported database nodes (Postgres, MySQL, MongoDB, etc.)
- CRUD operation patterns
- Connection management
- Query building patterns
- Batch operations
- Error handling (connection failures, query errors)

**ai_agent_workflow.md:**
- AI Agent node configuration
- Tool integration patterns
- Memory/context management
- Prompt engineering within n8n
- Chain patterns (sequential, parallel)
- Error handling (model failures, token limits)

**scheduled_tasks.md:**
- Cron trigger configuration
- Interval patterns
- Timezone handling
- Idempotency patterns
- Error handling (missed executions, overlap prevention)
- Monitoring patterns

**Verify:** All 5 files read completely. Each pattern has: trigger, node chain, data flow, error handling documented.

### Step 3: Cross-Pattern Analysis (~10 min)
Build comparison matrix:

| Aspect | Webhook | HTTP API | Database | AI Agent | Scheduled |
|--------|---------|----------|----------|----------|-----------|
| Trigger type | | | | | |
| Avg node count | | | | | |
| Error handling depth | | | | | |
| Auth complexity | | | | | |
| Testing ease | | | | | |
| Depends on skills | | | | | |

Identify:
- Shared elements across all 5 patterns (common nodes, common error patterns)
- Unique requirements per pattern
- Pattern composability (can patterns be combined?)
- Gap analysis: what common workflow types are NOT covered? (e.g., file processing, email automation, multi-service orchestration)

**Verify:** Matrix complete for all 5 patterns. At least 2 gap patterns identified.

### Step 4: Score and Analyze (~8 min)
Score the skill 1-10:
- **Completeness**: Do 5 patterns cover the most common n8n use cases? Are gaps minor?
- **Usability**: Can an AI select the right pattern and follow the creation checklist?
- **Modularity**: Can patterns be used independently? Do they compose well?
- **Safety**: Does each pattern include error handling? Are failure modes documented?

Document Uu/Nhuoc/Tich hop/Technical Decisions for the overall skill and per-pattern highlights.

**Verify:** All scores assigned with evidence from specific pattern files.

### Step 5: Write Deep-Dive Output (~10 min)
Create `self-explores/learn/n8n-workflow-patterns/analysis.md` with:
- Metadata header
- Executive summary (5 patterns, architectural role)
- SKILL.md structural analysis
- Per-pattern deep-dive summaries (5 sections)
- Cross-pattern comparison matrix
- Pattern selection decision tree (reconstructed)
- Creation checklist assessment
- Gap analysis (missing patterns)
- Scoring table with evidence
- Uu / Nhuoc / Tich hop sections
- Technical Decisions (why these 5? why this structure?)
- Recommendations

**Verify:** Output file contains all 5 pattern analyses. Comparison matrix present. Scores assigned.

## 6. Edge Cases & Error Handling
| Case | Trigger | Expected | Recovery |
|------|---------|----------|----------|
| Pattern file is shallow/stub | Less than 50 lines, no code examples | Flag as "insufficient depth" | Score completeness lower for that pattern |
| Pattern lacks error handling section | No failure mode documentation | Critical safety gap | Score safety at max 5/10 for that pattern |
| Patterns overlap significantly | e.g., HTTP API and Webhook share 80% content | Note redundancy in analysis | Recommend consolidation |
| ai_agent_workflow is outdated | References deprecated AI nodes | Flag version concern | Note in Technical Decisions, recommend update |
| No pattern composability guidance | SKILL.md doesn't explain combining patterns | Note as architectural gap | Recommend addition of composition section |

## 7. Acceptance Criteria
- **Happy 1:** Given SKILL.md and 5 pattern files are read, When analysis completes, Then output contains per-pattern summaries for all 5 patterns with trigger type, node chain, and error handling documented
- **Happy 2:** Given the 5 patterns represent common n8n use cases, When gap analysis runs, Then at least 2 uncovered workflow types are identified with justification
- **Negative:** Given a pattern file is missing or empty, When reading fails, Then analysis notes "MISSING PATTERN: {name}" and adjusts completeness score accordingly

## 8. Technical Notes
- This is the shortest SKILL.md (412 lines) but has the most reference files (5) — content is distributed
- The 5 patterns map to n8n's most common use cases per community surveys
- `ai_agent_workflow` is the newest pattern and likely the least mature
- Pattern selection is a critical UX decision — if the decision tree is unclear, an AI may pick wrong patterns
- The creation checklist should integrate with n8n-mcp-tools-expert's `n8n_create_workflow` guidance

## 9. Risks
- **Pattern breadth vs depth**: 5 patterns may cover many use cases superficially rather than fewer deeply. Mitigation: Assess depth per pattern independently.
- **Outdated AI patterns**: AI agent workflows in n8n evolve rapidly. Mitigation: Check for deprecated node references.
- **Composition gap**: Real workflows often combine multiple patterns. Mitigation: Explicitly assess whether composition is taught.

## Worklog
*(Chua bat dau)*
