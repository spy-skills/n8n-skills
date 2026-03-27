---
date: 2026-03-26
type: task-worklog
task: n8nskills-qt8
title: "Deep Dive: n8n-code-javascript"
status: open
detailed_at: 2026-03-26
detail_score: ready-for-dev
tags: [n8n, skills, research]
---

# Deep Dive: n8n-code-javascript [P2] — Detailed Design

## 1. Objective
Perform a deep-dive analysis of the n8n-code-javascript skill — the Code node skill that teaches AI assistants how to write JavaScript in n8n Code nodes, covering execution modes, data access patterns, built-in functions, error patterns, and cross-references with the expression syntax skill.

## 2. Scope
**In-scope:**
- Full analysis of SKILL.md (~700 lines) — execution modes, data access, built-ins, error handling
- DATA_ACCESS.md — input/output data patterns, item structure, binary data
- COMMON_PATTERNS.md — reusable JavaScript patterns for common tasks
- ERROR_PATTERNS.md — common JavaScript errors in n8n context and fixes
- BUILTIN_FUNCTIONS.md — n8n-provided functions and helpers (`$helpers`, DateTime, etc.)
- Execution modes: "Run Once for All Items" vs "Run Once for Each Item"
- Data access patterns: `$input`, `$json`, items array manipulation
- Built-in function coverage: `$helpers.httpRequest`, DateTime, type utilities
- Error pattern taxonomy and prevention
- Cross-reference with n8n-expression-syntax (when to use Code vs Expression)
- Cross-reference with n8n-code-python (JS vs Python comparison)
- Scoring on completeness/usability/modularity/safety (1-10)

**Out-of-scope:**
- Executing JavaScript code in live n8n instance
- Modifying code patterns
- Testing edge cases against n8n runtime
- Analyzing n8n Code node source code / sandbox implementation

## 3. Input / Output
**Input:**
- `skills/n8n-code-javascript/SKILL.md` (~700 lines)
- `skills/n8n-code-javascript/DATA_ACCESS.md`
- `skills/n8n-code-javascript/COMMON_PATTERNS.md`
- `skills/n8n-code-javascript/ERROR_PATTERNS.md`
- `skills/n8n-code-javascript/BUILTIN_FUNCTIONS.md`
- `self-explores/learn/overview/analysis.md` (overview context from n8nskills-10p)
- `skills/n8n-expression-syntax/SKILL.md` (for cross-reference, first 30 lines + relevant sections)

**Output:**
- `self-explores/learn/n8n-code-javascript/analysis.md` — deep-dive analysis with: Score 1-10, Uu/Nhuoc, Tich hop, Technical Decisions

## 4. Dependencies
- **n8nskills-10p** (provides overview context and initial scoring baseline)

## 5. Flow xu ly

### Step 1: Read SKILL.md Modes and Structure (~10 min)
Read `skills/n8n-code-javascript/SKILL.md` completely (~700 lines). Document:
- Section hierarchy and content distribution
- Frontmatter metadata (triggers, activation patterns)
- **Execution modes**: "Run Once for All Items" vs "Run Once for Each Item"
  - When to use each mode
  - Data structure differences between modes
  - Performance implications
  - Common mistakes per mode
- Data input/output model: how items flow in and out of Code node
- Built-in reference summary (links to BUILTIN_FUNCTIONS.md)
- Error handling guidance
- Sandbox limitations (what JavaScript features are NOT available)
- Cross-references to other skills

**Verify:** Both execution modes documented with use cases. Sandbox limitations listed.

### Step 2: Read DATA_ACCESS.md (~8 min)
Read `skills/n8n-code-javascript/DATA_ACCESS.md` completely. Analyze:
- Input data access patterns:
  - `$input.all()` — all items from previous node
  - `$input.first()` / `$input.last()` — specific items
  - `$input.item` — current item (in each-item mode)
  - `$json` — current item's JSON data
  - `$binary` — binary data access
- Output data patterns:
  - Return format: `[{json: {...}}, {json: {...}}]`
  - Adding binary data to output
  - Filtering items (returning subset)
- Cross-node data access:
  - `$node["NodeName"].json` — access other nodes' output
  - `$("NodeName").all()` — alternative syntax
- Environment and workflow context:
  - `$env` — environment variables
  - `$workflow` — workflow metadata
  - `$execution` — execution context

**Verify:** All data access patterns cataloged. Input and output patterns both documented.

### Step 3: Read COMMON_PATTERNS.md and ERROR_PATTERNS.md (~12 min)
Read both files completely.

**COMMON_PATTERNS.md — analyze:**
- Total number of patterns
- Pattern categories (data transformation, API calls, file handling, etc.)
- Pattern structure (name, use case, code example, explanation)
- Code quality (modern JS, error handling, performance)
- Reusability assessment (can patterns be copy-pasted directly?)
- Coverage of common tasks (filtering, mapping, merging, splitting, aggregating)

**ERROR_PATTERNS.md — analyze:**
- Total number of error patterns
- Error categories (syntax, runtime, data structure, sandbox limitation)
- Per-error structure: error message, cause, fix, prevention
- Most dangerous errors (data loss, silent failures, infinite loops)
- Errors specific to n8n context vs generic JavaScript errors
- Errors related to execution mode confusion

**Verify:** At least 5 common patterns and 5 error patterns documented. Error patterns include n8n-specific errors.

### Step 4: Read BUILTIN_FUNCTIONS.md and Cross-Reference (~10 min)
Read `skills/n8n-code-javascript/BUILTIN_FUNCTIONS.md` completely. Analyze:
- Complete list of built-in functions
- `$helpers` object: httpRequest, other utilities
- DateTime helpers (Luxon-based in n8n)
- Type checking/conversion utilities
- Crypto/hash functions (if available)
- Function signatures, parameters, return types
- Example usage for each function

Cross-reference with expression syntax:
- Which built-ins are available in both expressions and Code nodes?
- Which are Code-node-only?
- Which are expression-only?
- Read `skills/n8n-expression-syntax/SKILL.md` relevant sections for comparison

**Verify:** Built-in function catalog complete. Cross-reference table showing expression vs code availability.

### Step 5: Score, Analyze, and Write Output (~12 min)
Score the skill 1-10:
- **Completeness**: Both modes covered? All data access patterns? All built-ins? Error patterns comprehensive?
- **Usability**: Can an AI write correct Code node JavaScript by following the guidance? Are mode selection criteria clear?
- **Modularity**: Clean boundary with expression-syntax? Clear handoff from node-configuration?
- **Safety**: Error patterns prevent common mistakes? Sandbox limitations clearly warned? Data loss prevention?

Write `self-explores/learn/n8n-code-javascript/analysis.md` with:
- Metadata header
- Executive summary (Code node role, two execution modes, 4 reference files)
- SKILL.md structural analysis
- Execution modes deep-dive (comparison table, decision criteria)
- Data access patterns catalog (from DATA_ACCESS.md)
- Common patterns assessment (from COMMON_PATTERNS.md)
- Error patterns taxonomy (from ERROR_PATTERNS.md)
- Built-in functions catalog (from BUILTIN_FUNCTIONS.md)
- Expression vs Code Node comparison (cross-reference)
- JavaScript vs Python comparison (preview for n8nskills-be6)
- Scoring table with evidence
- Uu / Nhuoc / Tich hop sections
- Technical Decisions (why two modes? why these built-ins?)
- Recommendations

**Verify:** Output file exists, all 4 reference files cited, both execution modes analyzed, scores assigned.

## 6. Edge Cases & Error Handling
| Case | Trigger | Expected | Recovery |
|------|---------|----------|----------|
| Mode selection guidance is unclear | No decision tree for choosing mode | Critical usability gap | Score usability at max 5/10, recommend decision tree |
| Built-ins list is incomplete | Missing functions available in n8n runtime | Note as "potentially incomplete" | Cross-reference with n8n docs if available |
| ERROR_PATTERNS misses sandbox errors | Sandbox limitations not in error catalog | Safety gap | Score safety lower, list known sandbox limitations |
| COMMON_PATTERNS lack error handling | Patterns don't show try/catch | Quality concern | Note in Nhuoc, recommend error handling additions |
| Cross-reference with expressions incomplete | No clear "use expression for X, code for Y" | Integration gap | Document the boundary in analysis |

## 7. Acceptance Criteria
- **Happy 1:** Given SKILL.md and 4 reference files are read, When analysis completes, Then output contains execution mode comparison table and decision criteria for choosing between modes
- **Happy 2:** Given 4 reference files exist, When data access analysis runs, Then output catalogs all input/output patterns with code examples
- **Negative:** Given ERROR_PATTERNS.md is empty, When reading completes, Then analysis flags "EMPTY ERROR CATALOG" and scores safety at max 3/10

## 8. Technical Notes
- 700-line SKILL.md + 4 reference files = substantial content volume (second most after node-configuration in total content)
- The "Run Once for All Items" vs "Run Once for Each Item" mode distinction is a frequent source of confusion for both humans and AI
- n8n Code node uses a sandboxed JavaScript environment — not all Node.js APIs are available
- `$helpers.httpRequest` is a key built-in that enables API calls from within Code nodes
- DateTime handling uses Luxon library internally — different from standard JavaScript Date
- This skill has the most reference files (4) tied with n8n-code-python — reflects topic complexity

## 9. Risks
- **Content volume**: 5 files to analyze is the joint-maximum. Mitigation: Structured reading with per-file notes.
- **Mode confusion propagation**: If the skill's mode guidance is unclear, AI assistants will misuse modes at scale. Mitigation: Rigorously assess mode selection criteria.
- **Sandbox limitation documentation**: Missing sandbox warnings lead to runtime errors. Mitigation: Cross-check known n8n sandbox limitations against documented ones.

## Worklog
*(Chua bat dau)*
