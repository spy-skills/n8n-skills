---
date: 2026-03-26
type: task-worklog
task: n8nskills-ykj
title: "Xep hang uu tien Skills (1-5)"
status: open
detailed_at: 2026-03-26
detail_score: ready-for-dev
tags: [n8n, skills, research]
---

# Xep hang uu tien Skills (1-5) [P1] — Detailed Design

## 1. Objective
Rank all 7 skills on a 1-5 priority scale based on practical usage frequency, tool telemetry data, and cross-skill dependency depth, from the perspective of an AI assistant learning to build n8n workflows.

## 2. Scope
**In-scope:**
- Ranking 7 skills on 1-5 scale across 3 dimensions: practical usage, tool frequency (telemetry), cross-skill dependency
- Calculating composite priority score with weighted dimensions
- Identifying the "critical path" — minimum skill set needed for basic workflow building
- Mapping which skills are prerequisites for others
- Learning-order recommendation for an AI assistant

**Out-of-scope:**
- Re-reading all skill files from scratch (depends on n8nskills-10p analysis)
- Modifying skill content or priority
- User persona analysis beyond "AI assistant"
- Comparison with alternative skill frameworks

## 3. Input / Output
**Input:**
- `self-explores/learn/overview/analysis.md` (output of n8nskills-10p — scores, categories, integration notes)
- `CLAUDE.md` (telemetry data: tool usage counts, patterns, timing)
- All 7 SKILL.md frontmatter sections (for activation triggers and descriptions)

**Output:**
- `self-explores/learn/priority-ranking/analysis.md` — ranked list with composite scores, dimension breakdowns, dependency graph, and learning-order recommendation

## 4. Dependencies
- **n8nskills-10p** (must be completed first — provides the overview analysis with per-skill scores and integration mapping)

## 5. Flow xu ly

### Step 1: Load Overview Analysis (~3 min)
Read `self-explores/learn/overview/analysis.md` from the 10p task output. Extract:
- Per-skill scores (completeness, usability, modularity, safety)
- Category assignments
- Cross-skill integration notes (Tich hop sections)
- Identified strengths and weaknesses

**Verify:** Confirm all 7 skills are present in the loaded analysis. Note any gaps.

### Step 2: Extract Telemetry Mapping (~5 min)
Map CLAUDE.md telemetry data to specific skills:

| Telemetry Data Point | Maps To Skill |
|---|---|
| `n8n_update_partial_workflow` 38,287 uses, 99% success | n8n-mcp-tools-expert (WORKFLOW_GUIDE.md) |
| `search_nodes -> get_node` most common pattern, 18s avg | n8n-mcp-tools-expert (SEARCH_GUIDE.md) |
| Validation loop 7,841 occurrences, 23s thinking, 58s fixing | n8n-validation-expert |
| `n8n_update_partial_workflow -> n8n_validate_workflow` pattern | n8n-mcp-tools-expert + n8n-validation-expert |

Also read each SKILL.md frontmatter (first 30 lines) to extract activation trigger keywords:
```
skills/n8n-mcp-tools-expert/SKILL.md (lines 1-30)
skills/n8n-validation-expert/SKILL.md (lines 1-30)
skills/n8n-workflow-patterns/SKILL.md (lines 1-30)
skills/n8n-node-configuration/SKILL.md (lines 1-30)
skills/n8n-expression-syntax/SKILL.md (lines 1-30)
skills/n8n-code-javascript/SKILL.md (lines 1-30)
skills/n8n-code-python/SKILL.md (lines 1-30)
```

**Verify:** Each telemetry data point mapped to exactly one or two skills. Activation triggers extracted for all 7.

### Step 3: Score 3 Dimensions per Skill (~10 min)
For each of the 7 skills, assign a 1-5 score on:

**Dimension A — Practical Usage (weight 40%):**
How often would an AI assistant invoke this skill when building n8n workflows?
- 5: Every single workflow build
- 4: Most workflow builds (>70%)
- 3: Many builds (~50%)
- 2: Specialized builds only
- 1: Rarely needed

**Dimension B — Tool Frequency / Telemetry (weight 35%):**
How heavily used are the MCP tools this skill teaches?
- 5: Top tool by usage count (>10,000 uses)
- 4: High usage (5,000-10,000)
- 3: Moderate usage (1,000-5,000)
- 2: Low usage (<1,000)
- 1: No direct tool mapping

**Dimension C — Cross-Skill Dependency (weight 25%):**
How many other skills depend on this one? Is it a prerequisite?
- 5: Required by 5+ other skills (foundational)
- 4: Required by 3-4 other skills
- 3: Required by 2 other skills
- 2: Required by 1 other skill
- 1: Standalone, no dependents

Calculate composite: `(A * 0.40) + (B * 0.35) + (C * 0.25)`

**Verify:** All 7 skills have 3 dimension scores and 1 composite score. Rankings are consistent with telemetry evidence.

### Step 4: Build Dependency Graph and Learning Order (~8 min)
Create a text-based dependency graph showing which skills feed into others:
```
n8n-mcp-tools-expert (foundational)
  |-> n8n-node-configuration (uses get_node)
  |-> n8n-validation-expert (uses validate_node/workflow)
  |-> n8n-workflow-patterns (uses create/update workflow)
      |-> n8n-expression-syntax (data mapping within patterns)
      |-> n8n-code-javascript (code nodes within patterns)
      |-> n8n-code-python (code nodes within patterns)
```

Derive recommended learning order (numbered sequence 1-7).

**Verify:** Learning order is a valid topological sort of the dependency graph. No circular dependencies.

### Step 5: Write Priority Ranking Output (~10 min)
Create `self-explores/learn/priority-ranking/analysis.md` with:
- Header with methodology explanation
- Dimension weight justification
- Ranked table (7 skills sorted by composite score, highest first)
- Per-skill breakdown: 3 dimension scores, composite, justification paragraph
- Dependency graph (text-based)
- Recommended learning order with time estimates
- "Critical path" — minimum 3-4 skills for basic workflow building
- Comparison with overview scores from n8nskills-10p

**Verify:** File exists, contains all 7 skills ranked, composite scores are mathematically correct, learning order is logically sound.

## 6. Edge Cases & Error Handling
| Case | Trigger | Expected | Recovery |
|------|---------|----------|----------|
| 10p analysis not yet available | Dependency not met | Block execution, return to queue | Wait for n8nskills-10p completion |
| Two skills tie on composite score | Same weighted average | Break tie by practical usage (Dimension A) | If still tied, use telemetry (Dimension B) |
| Telemetry maps to no skill | Tool has no corresponding skill | Note as "uncovered MCP tool" gap | Recommend new skill in analysis |
| Circular dependency found | Skill A needs B, B needs A | Flag as mutual dependency | Recommend learning both together as a unit |
| Skill has zero telemetry | No usage data available | Score Dimension B as 1 | Justify with note "no direct telemetry" |

## 7. Acceptance Criteria
- **Happy 1:** Given the 10p overview analysis is complete, When ranking runs, Then all 7 skills have composite scores between 1.0 and 5.0, sorted descending
- **Happy 2:** Given telemetry shows n8n_update_partial_workflow at 38,287 uses, When mapping to skills, Then n8n-mcp-tools-expert scores 5/5 on Dimension B (tool frequency)
- **Negative:** Given 10p analysis is missing or incomplete, When task starts, Then execution halts with explicit "BLOCKED: dependency n8nskills-10p not met" message

## 8. Technical Notes
- Dimension weights (40/35/25) chosen because: practical usage is most important for an AI assistant's workflow, telemetry validates real-world frequency, dependency depth ensures foundational skills are prioritized
- Expected top-ranked skill: n8n-mcp-tools-expert (highest telemetry, most dependencies, used in every workflow)
- Expected lowest-ranked: n8n-code-python (Beta status, least common use case, no external libraries)
- The 18s average between search_nodes and get_node suggests these are tightly coupled — the tools-expert skill teaching this pattern is high value

## 9. Risks
- **Telemetry bias**: Usage counts reflect past usage, not optimal usage. Mitigation: Balance with qualitative practical-usage assessment.
- **Dependency over-weighting**: A skill could score high just because others reference it. Mitigation: Cap dependency score at 5 even if 6+ skills depend on it.
- **AI assistant perspective**: Ranking is specific to AI usage, not human developers. Mitigation: Document this assumption explicitly in output.

## Worklog
*(Chua bat dau)*
