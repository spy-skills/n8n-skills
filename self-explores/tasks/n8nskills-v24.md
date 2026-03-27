---
date: 2026-03-26
type: task-worklog
task: n8nskills-v24
title: "Deep Dive: n8n-expression-syntax"
status: open
detailed_at: 2026-03-26
detail_score: ready-for-dev
tags: [n8n, skills, research]
---

# Deep Dive: n8n-expression-syntax [P2] — Detailed Design

## 1. Objective
Perform a deep-dive analysis of the n8n-expression-syntax skill — the data mapping skill that teaches AI assistants correct n8n expression format, variable references, the critical webhook data gotcha, common mistakes catalog, and helper functions.

## 2. Scope
**In-scope:**
- Full analysis of SKILL.md (~517 lines) — expression format, variables, helpers, gotchas
- COMMON_MISTAKES.md — catalog of expression errors with fixes
- EXAMPLES.md — practical expression examples for common scenarios
- Expression format analysis: `{{ }}` patterns, variable scoping, nested access
- The critical webhook gotcha: data under `$json.body` not `$json`
- Variable reference system: `$json`, `$item`, `$node`, `$input`, `$binary`, etc.
- Helper functions coverage: `$helpers`, DateTime, type conversion
- Common mistakes taxonomy and prevention patterns
- Cross-reference with n8n-code-javascript (expression vs code node)
- Scoring on completeness/usability/modularity/safety (1-10)

**Out-of-scope:**
- Testing expressions in live n8n instance
- Modifying expression syntax documentation
- Comparing with other automation platform expression languages
- Analyzing n8n expression parser source code

## 3. Input / Output
**Input:**
- `skills/n8n-expression-syntax/SKILL.md` (~517 lines)
- `skills/n8n-expression-syntax/COMMON_MISTAKES.md`
- `skills/n8n-expression-syntax/EXAMPLES.md`
- `self-explores/learn/overview/analysis.md` (overview context from n8nskills-10p)

**Output:**
- `self-explores/learn/n8n-expression-syntax/analysis.md` — deep-dive analysis with: Score 1-10, Uu/Nhuoc, Tich hop, Technical Decisions

## 4. Dependencies
- **n8nskills-10p** (provides overview context and initial scoring baseline)

## 5. Flow xu ly

### Step 1: Read SKILL.md Expression Format Analysis (~10 min)
Read `skills/n8n-expression-syntax/SKILL.md` completely (~517 lines). Document:
- Section hierarchy and content distribution
- Frontmatter metadata (triggers, activation patterns)
- Expression format specification: `{{ }}` wrapper, JavaScript subset, limitations
- Variable reference system: complete list of `$` variables
  - `$json` — current item data
  - `$item` — item index
  - `$node["name"]` — reference other nodes
  - `$input` — input data
  - `$binary` — binary data access
  - `$env` — environment variables
  - Others documented
- **The webhook gotcha**: `$json.body` vs `$json` — how prominently is this warned?
- Helper functions: `$helpers.httpRequest`, DateTime helpers, etc.
- Type coercion rules
- Cross-references to other skills

**Verify:** All `$` variables cataloged. Webhook gotcha found with severity/prominence assessment.

### Step 2: Read COMMON_MISTAKES.md (~8 min)
Read `skills/n8n-expression-syntax/COMMON_MISTAKES.md` completely. Analyze:
- Total number of documented mistakes
- Mistake categories (syntax errors, reference errors, type errors, scope errors)
- Per-mistake structure: mistake description, incorrect example, correct example, explanation
- Severity classification (if present)
- The webhook `$json.body` gotcha — is it listed as #1 or most critical?
- Mistakes that map to validation-expert error catalog entries
- Prevention strategies vs detection strategies

**Verify:** At least 5 mistakes documented. Each has incorrect + correct example pair.

### Step 3: Read EXAMPLES.md (~8 min)
Read `skills/n8n-expression-syntax/EXAMPLES.md` completely. Analyze:
- Total number of examples
- Example categories (data access, transformation, conditional, aggregation)
- Complexity progression (simple -> intermediate -> advanced)
- Practical scenario coverage (webhook data, API responses, database results, etc.)
- Helper function usage examples
- Edge case examples (null handling, missing fields, empty arrays)
- Code quality of examples (correct syntax, runnable, well-commented)

**Verify:** At least 10 examples found. Examples cover at least 3 different data source types.

### Step 4: Cross-Reference with Code Skills (~5 min)
Compare expression syntax with n8n-code-javascript to identify:
- When to use expression (`{{ }}`) vs Code node
- Overlapping capabilities (both can do data transformation)
- Expression limitations that push to Code node
- Shared variable references (`$json`, `$input` available in both contexts?)
- Helper functions available in expressions but not Code nodes (or vice versa)

Read relevant sections from `skills/n8n-code-javascript/SKILL.md` (first 50 lines + data access section) for comparison.

**Verify:** Decision boundary documented: "use expression for X, use code node for Y."

### Step 5: Score, Analyze, and Write Output (~12 min)
Score the skill 1-10:
- **Completeness**: All `$` variables documented? All common scenarios covered?
- **Usability**: Can an AI write correct expressions by following the guidance? Is the webhook gotcha unmissable?
- **Modularity**: Clean boundary with code-javascript skill? Standalone for simple data mapping?
- **Safety**: Does the mistakes catalog prevent the most common errors? Are edge cases covered?

Write `self-explores/learn/n8n-expression-syntax/analysis.md` with:
- Metadata header
- Executive summary (expression system, why it's error-prone)
- SKILL.md structural analysis
- Variable reference system deep-dive (complete `$` variable catalog)
- Webhook gotcha analysis (prominence, prevention effectiveness)
- Common mistakes taxonomy (from COMMON_MISTAKES.md)
- Examples assessment (coverage, quality, progression)
- Expression vs Code Node boundary analysis
- Scoring table with evidence
- Uu / Nhuoc / Tich hop sections
- Technical Decisions (why `{{ }}`? why webhook data structure?)
- Recommendations

**Verify:** Output file exists, webhook gotcha prominently analyzed, variable catalog complete, scores assigned.

## 6. Edge Cases & Error Handling
| Case | Trigger | Expected | Recovery |
|------|---------|----------|----------|
| Webhook gotcha not prominently warned | Buried in middle of SKILL.md | Critical safety gap for most common use case | Score safety at max 5/10, recommend elevation to top |
| COMMON_MISTAKES has few entries | Less than 5 mistakes documented | Insufficient mistake catalog | Score completeness lower, recommend expansion |
| EXAMPLES lack error/null handling | Only happy-path examples | Safety gap for edge cases | Note absence, recommend null-handling examples |
| Expression syntax conflicts with JS | n8n expression quirks differ from standard JS | Must be clearly documented | Verify skill warns about differences |
| Helper functions underdocumented | Referenced but not explained | Usability gap | Score usability lower for helper section |

## 7. Acceptance Criteria
- **Happy 1:** Given SKILL.md and 2 reference files are read, When analysis completes, Then output contains complete `$` variable catalog with at least 5 variables and their access patterns
- **Happy 2:** Given the webhook gotcha is critical, When safety is assessed, Then output has dedicated webhook gotcha section with prominence rating and prevention effectiveness score
- **Negative:** Given COMMON_MISTAKES.md is empty, When reading completes, Then analysis flags "EMPTY MISTAKES CATALOG" and scores safety at max 3/10

## 8. Technical Notes
- The `{{ }}` expression syntax is n8n-specific — not standard JavaScript, though it uses JS internally
- The webhook gotcha (`$json.body` vs `$json`) is called out in CLAUDE.md as "Critical gotcha" — this is the #1 pain point
- Expressions are used in nearly every node's parameter fields — this skill has very high practical usage frequency
- The 517-line SKILL.md is mid-range in size, but expressions are a dense topic
- Expression errors are often silent (wrong value returned instead of error) — this makes safety scoring particularly important

## 9. Risks
- **Silent failures**: Expression mistakes often produce wrong data rather than errors, making them hard to detect. Mitigation: Assess whether the skill teaches validation of expression results.
- **Webhook gotcha prominence**: If this critical gotcha is buried, it will be missed by AI assistants. Mitigation: Explicitly rate its prominence in the analysis.
- **Expression vs Code boundary**: Unclear boundary leads to over-use of Code nodes or under-use of expressions. Mitigation: Document the decision boundary explicitly.

## Worklog
*(Chua bat dau)*
