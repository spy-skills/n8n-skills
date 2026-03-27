---
date: 2026-03-26
type: task-worklog
task: n8nskills-be6
title: "Deep Dive: n8n-code-python"
status: open
detailed_at: 2026-03-26
detail_score: ready-for-dev
tags: [n8n, skills, research]
---

# Deep Dive: n8n-code-python [P3] — Detailed Design

## 1. Objective
Perform a deep-dive analysis of the n8n-code-python skill — the Python Code node skill that teaches AI assistants how to write Python in n8n's Python Code nodes, covering Beta vs Native status, severe limitations (no external libraries), standard library coverage, and comparison with the JavaScript Code node skill.

## 2. Scope
**In-scope:**
- Full analysis of SKILL.md (~750 lines) — Beta status, data access, limitations, standard library
- DATA_ACCESS.md — Python-specific input/output data patterns
- COMMON_PATTERNS.md — reusable Python patterns for n8n tasks
- ERROR_PATTERNS.md — Python-specific errors in n8n context
- STANDARD_LIBRARY.md — available Python standard library modules in n8n sandbox
- Beta vs Native status assessment and implications
- **Critical limitation**: no external libraries (no pandas, requests, numpy, etc.)
- Standard library coverage: which stdlib modules are available?
- JavaScript vs Python comparison (feature parity, performance, use cases)
- Scoring on completeness/usability/modularity/safety (1-10)

**Out-of-scope:**
- Executing Python code in live n8n instance
- Testing stdlib module availability
- Modifying Python code patterns
- Lobbying for external library support

## 3. Input / Output
**Input:**
- `skills/n8n-code-python/SKILL.md` (~750 lines)
- `skills/n8n-code-python/DATA_ACCESS.md`
- `skills/n8n-code-python/COMMON_PATTERNS.md`
- `skills/n8n-code-python/ERROR_PATTERNS.md`
- `skills/n8n-code-python/STANDARD_LIBRARY.md`
- `self-explores/learn/overview/analysis.md` (overview context from n8nskills-10p)
- `skills/n8n-code-javascript/SKILL.md` (for JS comparison, relevant sections)

**Output:**
- `self-explores/learn/n8n-code-python/analysis.md` — deep-dive analysis with: Score 1-10, Uu/Nhuoc, Tich hop, Technical Decisions

## 4. Dependencies
- **n8nskills-10p** (provides overview context and initial scoring baseline)

## 5. Flow xu ly

### Step 1: Read SKILL.md Beta Status and Limitations (~10 min)
Read `skills/n8n-code-python/SKILL.md` completely (~750 lines). Document:
- Section hierarchy and content distribution
- Frontmatter metadata (triggers, activation patterns)
- **Beta vs Native status**: What exactly is Beta? What limitations does it impose?
  - Stability guarantees (or lack thereof)
  - Feature completeness compared to JavaScript Code node
  - Known bugs or limitations
- **No external libraries limitation**: How prominently is this warned?
  - Cannot use: pandas, numpy, requests, beautifulsoup4, etc.
  - Available: only Python standard library
  - Workarounds documented
- Execution modes (if applicable — same "Run Once for All/Each" as JS?)
- Data access model: `_input`, items structure, output format
- Cross-references to n8n-code-javascript skill

**Verify:** Beta status clearly documented. No-external-libraries warning found with prominence assessment.

### Step 2: Read DATA_ACCESS.md (~8 min)
Read `skills/n8n-code-python/DATA_ACCESS.md` completely. Analyze:
- Python-specific data access patterns (compare with JS equivalents):
  - Input data: `_input.all()`, `_input.first()`, `_input.last()`, `_input.item`
  - JSON data: item access patterns
  - Binary data: access and manipulation
  - Cross-node data: referencing other nodes' output
  - Environment variables
- Output format: return structure (list of dicts?)
- Data type mapping: JavaScript types -> Python types
- Serialization considerations (JSON-compatible types only)

**Verify:** All data access patterns documented. Python-JS equivalent mapping identifiable.

### Step 3: Read COMMON_PATTERNS.md and ERROR_PATTERNS.md (~12 min)
Read both files completely.

**COMMON_PATTERNS.md — analyze:**
- Total number of patterns
- Pattern categories (data transformation, string processing, date handling, etc.)
- Stdlib-only patterns: do all patterns use only standard library?
- Pattern quality: Pythonic code? Error handling? Type safety?
- Workarounds for missing external libraries (e.g., JSON parsing instead of pandas, urllib instead of requests)
- Comparison with JavaScript COMMON_PATTERNS.md — which tasks are easier/harder in Python?

**ERROR_PATTERNS.md — analyze:**
- Total number of error patterns
- Python-specific errors (indentation, type errors, import errors for unavailable libs)
- n8n-specific Python errors (data structure, serialization, sandbox limitations)
- Most dangerous errors: attempting to import external libraries, returning non-serializable data
- Beta-related errors: known bugs or instabilities

**Verify:** At least 5 common patterns and 5 error patterns. Import-unavailable-library error is documented.

### Step 4: Read STANDARD_LIBRARY.md and Assess Coverage (~10 min)
Read `skills/n8n-code-python/STANDARD_LIBRARY.md` completely. Analyze:
- Complete list of available stdlib modules
- Categorize by usefulness for n8n tasks:
  - **High value**: json, re, datetime, collections, itertools, math, hashlib, base64, urllib
  - **Medium value**: csv, io, string, functools, operator
  - **Low value**: for n8n context
- Modules explicitly unavailable or blocked
- Module usage examples provided
- Coverage assessment: what percentage of common data processing tasks can be done with available stdlib?
- Comparison with JavaScript built-ins: which language has better tooling for common tasks?

**Verify:** Stdlib module list complete. High/medium/low categorization done. At least 5 high-value modules identified.

### Step 5: Score, JS Comparison, and Write Output (~15 min)
Build JavaScript vs Python comparison table:

| Aspect | JavaScript | Python |
|--------|-----------|--------|
| Status | Stable/Native | Beta |
| External libs | None (sandbox) | None (sandbox) |
| Stdlib/built-ins | Luxon, $helpers | Python stdlib |
| Data access | $input, $json | _input, items |
| Mode support | Both modes | ? |
| Error handling | try/catch | try/except |
| Performance | Faster (native) | Slower (Pyodide?) |
| Use case fit | General n8n | Data processing |

Score the skill 1-10:
- **Completeness**: Beta limitations clearly documented? Stdlib coverage comprehensive? All patterns stdlib-only?
- **Usability**: Can an AI write correct Python Code node by following guidance? Is Beta status warning unmissable?
- **Modularity**: Clean boundary with code-javascript? When to recommend Python over JS?
- **Safety**: Does it prevent import errors? Warn about serialization? Handle Beta instability?

Write `self-explores/learn/n8n-code-python/analysis.md` with:
- Metadata header
- Executive summary (Beta status, no-external-libs, when to use Python)
- SKILL.md structural analysis
- Beta vs Native deep-dive (limitations, implications, timeline if known)
- No-external-libraries analysis (impact, workarounds)
- Data access patterns (from DATA_ACCESS.md, with JS comparison)
- Common patterns assessment (from COMMON_PATTERNS.md)
- Error patterns taxonomy (from ERROR_PATTERNS.md)
- Standard library assessment (from STANDARD_LIBRARY.md)
- JavaScript vs Python comparison table
- Scoring table with evidence
- Uu / Nhuoc / Tich hop sections
- Technical Decisions (why include Python if Beta? when to recommend Python over JS?)
- Recommendations

**Verify:** Output file exists, Beta warning prominent, JS comparison table present, stdlib assessment included, scores assigned.

## 6. Edge Cases & Error Handling
| Case | Trigger | Expected | Recovery |
|------|---------|----------|----------|
| Beta status has changed to stable | SKILL.md outdated | Note version concern | Check against n8n release notes if available |
| STANDARD_LIBRARY.md is incomplete | Missing available modules | Note as potentially incomplete | Recommend verification against live n8n |
| Patterns use external libraries | Code examples import pandas/requests | Critical quality bug | Flag immediately, score completeness at max 3/10 |
| No Python execution mode documentation | Modes not applicable to Python | Document difference from JS | Note in comparison table |
| Python sandbox differs from JS sandbox | Different available APIs | Must be documented | Compare sandbox limitations between skills |

## 7. Acceptance Criteria
- **Happy 1:** Given SKILL.md and 4 reference files are read, When analysis completes, Then output contains Beta limitation section and no-external-libraries warning with workaround catalog
- **Happy 2:** Given both JS and Python skills exist, When comparison runs, Then output contains side-by-side table with at least 8 comparison dimensions
- **Negative:** Given COMMON_PATTERNS.md contains patterns using external libraries, When quality check runs, Then analysis flags "PATTERNS USE UNAVAILABLE LIBRARIES" as critical defect

## 8. Technical Notes
- At 750 lines, SKILL.md is the second-longest — Beta limitations require extensive documentation
- Python support in n8n likely uses Pyodide (WebAssembly-based Python) — this explains stdlib-only limitation
- The no-external-libraries limitation makes Python significantly less useful than JavaScript for most tasks
- Primary Python advantage: cleaner syntax for data transformation, better string formatting, native dict comprehensions
- This is P3 (lowest priority) because: Beta status, limited functionality, JS covers same use cases
- 4 reference files (same as JS) suggests the skill was designed as a parallel to the JavaScript skill

## 9. Risks
- **Beta deprecation**: n8n could change or remove Python support. Mitigation: Note stability concern in analysis.
- **Misleading completeness**: 750 lines may suggest comprehensive coverage, but much content may be limitation warnings. Mitigation: Assess content density (useful guidance vs warnings).
- **AI over-recommending Python**: An AI assistant might choose Python when JS is more appropriate. Mitigation: Ensure the analysis clearly documents when Python is genuinely better.

## Worklog
*(Chua bat dau)*
