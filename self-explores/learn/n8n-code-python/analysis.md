---
date: 2026-03-26
type: learning
task: n8nskills-be6
tags: [n8n, skills, deep-dive, n8n-code-python]
---

# n8n Code Python

## Danh gia (Score)
| Criteria | Score | Justification |
|----------|:-----:|---------------|
| Completeness | 8/10 | Covers both execution modes, 4 data access patterns (_input.all(), _input.first(), _input.item, _node), return format requirements, 5 common patterns (data transformation, filtering/aggregation, regex, data validation, statistical analysis), top 5 errors, standard library reference (9 modules), Beta vs Native mode comparison, and Python vs JavaScript decision guide. Delegates to 4 sub-files (DATA_ACCESS.md, COMMON_PATTERNS.md, ERROR_PATTERNS.md, STANDARD_LIBRARY.md). Gap: limited coverage of complex data structures and no async patterns. |
| Usability | 8/10 | Opens with the critical "JavaScript First" recommendation, immediately setting expectations. Quick-start template is clear. Beta vs Native mode comparison helps users choose. Standard library reference is immediately useful. However, the "no external libraries" limitation could be more prominent -- it is the single biggest surprise for Python developers. |
| Modularity | 9/10 | Clean 5-file structure parallel to JavaScript skill: SKILL.md, DATA_ACCESS.md, COMMON_PATTERNS.md, ERROR_PATTERNS.md, STANDARD_LIBRARY.md. Naming consistency with the JavaScript counterpart makes the skills easy to navigate in parallel. Each file is independently useful. |
| Safety | 9/10 | The #1 error (importing external libraries) is Python-specific and gets top billing with a clear "ModuleNotFoundError" warning. Safe dictionary access via `.get()` is emphasized throughout (best practice #1). Return format enforcement mirrors JavaScript skill. Workaround section for missing libraries (HTTP Request node, JavaScript $helpers, manual calculations) prevents users from getting stuck. |
| **Average** | **8.5** | |

## Uu diem
1. **"JavaScript First" framing is the most important design decision** -- opening with "Use JavaScript for 95% of use cases" and listing 4 reasons (full helper functions, Luxon DateTime, no library limitations, better documentation) sets the correct mental model. This prevents users from choosing Python by default and then hitting the external library wall.
2. **Standard library documentation is comprehensive and immediately practical** -- lists 9 available modules (json, datetime, re, base64, hashlib, urllib.parse, math, random, statistics) with usage examples for each. More importantly, lists 6 NOT available modules (requests, pandas, numpy, scipy, BeautifulSoup, lxml) with workarounds for each. This dual list (available + not available) is more useful than either alone.
3. **Beta vs Native mode comparison fills a unique gap** -- documents two Python execution modes: Python (Beta) with `_input`, `_json`, `_node`, `_now`, `_today`, `_jmespath()` helpers, and Python (Native) with only `_items`/`_item` variables. Recommends Beta for better n8n integration. This distinction is not documented elsewhere.

## Nhuoc diem
1. **No equivalent to $helpers.httpRequest()** -- this is the biggest functional gap between Python and JavaScript Code nodes. Python cannot make HTTP requests from within Code nodes (no `requests` library, no built-in HTTP helper). The workaround (use HTTP Request node before Code node) adds workflow complexity.
2. **Statistical analysis pattern is limited to `statistics` module** -- shows mean, median, stdev, min, max but cannot do anything that requires pandas, numpy, or scipy. For users who chose Python specifically for data analysis, this limitation is severe. The skill acknowledges it but the workaround ("manual calculations with lists and dictionaries") is insufficient for real statistical work.
3. **Underscore prefix convention (`_input`, `_json`, `_node`) is not explained** -- Python Code nodes use `_input` instead of JavaScript's `$input`, `_json` instead of `$json`, `_node` instead of `$node`. The skill uses these consistently but never explains WHY the prefix changes from `$` to `_` or confirms that this is intentional (Python reserves `$` for string formatting).

## Kha nang tich hop vao he thong hien tai
This skill is the **alternative code layer** -- it provides a Python option for users who need specific standard library functions or prefer Python syntax.

**Depends on:**
- **n8n-code-javascript** is the primary reference -- this skill explicitly defers to JavaScript for 95% of use cases and provides migration guidance
- **n8n-expression-syntax** for understanding the Code node vs expression boundary (same as JavaScript)
- **n8n-mcp-tools-expert** for `search_nodes({query: "code"})` and validation
- **n8n-node-configuration** for mode and language selection

**Depended on by:**
- No other skill depends on this -- it is a leaf node in the dependency graph
- **n8n-code-javascript** references this for comparison purposes

This is the most "optional" skill in the ecosystem. If a user only had 6 of the 7 skills, this would be the one to drop. However, for Python-native developers or specific standard library needs (statistics, hashlib, base64, regex), it provides essential guidance.

## Ghi chu ky thuat (Technical Decisions)
- **Two Python execution modes**: Python (Beta) with full helpers (`_input`, `_json`, `_node`, `_now`, `_today`, `_jmespath()`) and Python (Native) with minimal variables (`_items`, `_item`). Beta is recommended.
- **Underscore prefix convention**: Python uses `_input`, `_json`, `_node` instead of JavaScript's `$input`, `$json`, `$node`. This is because `$` is not a valid Python identifier prefix.
- **4 data access patterns**: `_input.all()`, `_input.first()`, `_input.item`, `_node["Name"]["json"]`. Mirror JavaScript patterns with Python syntax.
- **Return format**: Must be `[{"json": {...}}, ...]`. Same structure as JavaScript but with Python dict syntax.
- **9 available standard library modules**: json, datetime, re, base64, hashlib, urllib.parse, math, random, statistics.
- **6 unavailable external libraries**: requests, pandas, numpy, scipy, BeautifulSoup, lxml.
- **Workarounds for missing libraries**:
  - HTTP requests: Use HTTP Request node before Code node, or switch to JavaScript `$helpers.httpRequest()`
  - Data analysis: Use `statistics` module or manual calculations
  - Web scraping: Use HTTP Request node + HTML Extract node
- **Top 5 errors** (ordered): (1) Importing external libraries (Python-specific!), (2) Empty code/missing return, (3) Incorrect return format, (4) KeyError on dict access, (5) Webhook body nesting.
- **Safe dictionary access**: `.get()` method is best practice #1. `_json.get("body", {}).get("name", "Unknown")` pattern for nested access.
- **List comprehensions** are the Pythonic equivalent of JavaScript's map/filter and are recommended over manual loops.
- **print()** output appears in browser console (F12) -- same debugging mechanism as JavaScript's console.log().
- **statistics module** provides: mean, median, stdev, min, max -- sufficient for basic statistical analysis.

## Key Takeaways
- This skill exists for the 5% of use cases where Python is genuinely needed -- the "JavaScript First" recommendation is its most important guidance
- The external library limitation (no requests, pandas, numpy) is the single biggest Python Code node surprise and should be the first thing any Python developer learns about n8n
- Beta mode (with `_input`, `_now`, `_jmespath()` helpers) is strictly superior to Native mode and should always be the recommendation
- Safe dictionary access via `.get()` is more critical in Python than JavaScript because Python raises `KeyError` (crash) while JavaScript returns `undefined` (silent failure)
- The parallel file structure with the JavaScript skill (same sub-file names, same section organization) makes it easy to compare capabilities and migrate between languages
