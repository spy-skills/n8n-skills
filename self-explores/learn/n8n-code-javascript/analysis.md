---
date: 2026-03-26
type: learning
task: n8nskills-qt8
tags: [n8n, skills, deep-dive, n8n-code-javascript]
---

# n8n Code JavaScript

## Danh gia (Score)
| Criteria | Score | Justification |
|----------|:-----:|---------------|
| Completeness | 9/10 | Covers both execution modes (Run Once for All Items, Run Once for Each Item), 4 data access patterns ($input.all(), $input.first(), $input.item, $node), return format requirements with 5 correct and 5 incorrect examples, 5 common patterns (multi-source aggregation, regex filtering, data transformation, top-N ranking, aggregation/reporting), top 5 error prevention patterns, 3 built-in functions ($helpers.httpRequest, DateTime/Luxon, $jmespath), 6 best practices, and a 10-item deployment checklist. Delegates to 4 sub-files (DATA_ACCESS.md, COMMON_PATTERNS.md, ERROR_PATTERNS.md, BUILTIN_FUNCTIONS.md). |
| Usability | 9/10 | Exceptional quick-start template (7 lines, immediately usable). Mode selection guide with clear "When to use" bullets and a decision shortcut ("Need to look at multiple items? -> All Items. Each item independent? -> Each Item. Not sure? -> All Items"). Top 5 mistakes each show wrong/correct pairs. Deployment checklist is copy-paste actionable. |
| Modularity | 9/10 | 5-file structure: SKILL.md (reference + quick start), DATA_ACCESS.md (data access patterns), COMMON_PATTERNS.md (10 production patterns), ERROR_PATTERNS.md (error solutions), BUILTIN_FUNCTIONS.md (built-in reference). Each file stands alone. SKILL.md at 700 lines is comprehensive but not bloated. |
| Safety | 9/10 | Explicit return format enforcement (array of `{json: {...}}` objects) with 5 incorrect format examples. Null/undefined protection via optional chaining (`item.json?.user?.email`). Input validation pattern (check `items.length === 0` and `items[0].json`). Try-catch for HTTP requests. "When to Use Code Node" vs other nodes decision guide prevents overuse. |
| **Average** | **9.0** | |

## Uu diem
1. **Return format documentation is the most thorough of any skill** -- shows 5 correct formats (single result, multiple results, transformed array, empty result, conditional return) and 5 incorrect formats (object without array, array without json wrapper, plain string, raw data, incomplete structure), with the explicit reason "Next nodes expect array format. Incorrect format causes workflow execution to fail." This prevents the single most common Code node runtime error.
2. **Built-in functions coverage fills critical gaps** -- `$helpers.httpRequest()` enables HTTP calls from Code nodes (with full headers, method, URL configuration), `DateTime` from Luxon provides date manipulation without external dependencies, and `$jmespath()` provides JSON path querying. These 3 built-ins cover the vast majority of what external libraries would be needed for.
3. **Mode selection decision shortcut is brilliantly simple** -- "Need to look at multiple items? -> All Items. Each item completely independent? -> Each Item. Not sure? -> All Items." This 3-line decision tree eliminates analysis paralysis for the mode selection that confuses most new users.

## Nhuoc diem
1. **No async/await patterns beyond $helpers.httpRequest** -- the skill shows `await $helpers.httpRequest()` but does not cover general async patterns, Promise handling, or concurrent API calls (e.g., `Promise.all()` for parallel HTTP requests). These are common needs in production Code nodes.
2. **Error handling limited to try-catch** -- shows basic try-catch for HTTP requests but does not cover error propagation strategies (throw to trigger error branch vs return error object vs empty return), custom error types, or integration with n8n's error workflow system.
3. **"When to Use Code Node" section could be more quantitative** -- lists when to use and when not to use but does not provide complexity thresholds (e.g., "if your Set node has more than 5 field mappings, consider a Code node" or "if your IF chain exceeds 3 levels, use a Code node").

## Kha nang tich hop vao he thong hien tai
This skill is the **custom logic layer** for workflows where built-in nodes are insufficient.

**Depends on:**
- **n8n-expression-syntax** for understanding the boundary: expressions use `{{ }}`, Code nodes use direct JavaScript. The "Expression Syntax Confusion" error (#2) explicitly documents this boundary.
- **n8n-mcp-tools-expert** for `search_nodes({query: "code"})` and `validate_node` for Code node configuration
- **n8n-node-configuration** for mode selection (All Items vs Each Item) and language selection

**Depended on by:**
- **n8n-code-python** cross-references this for JavaScript vs Python comparison and migration guidance
- **n8n-workflow-patterns** uses Code nodes in the "transformation" building block across all 5 patterns
- **n8n-validation-expert** references this for validating Code node configuration

This skill and n8n-code-python together form the "custom code" capability. JavaScript is explicitly recommended for 95% of use cases, making this the primary code skill.

## Ghi chu ky thuat (Technical Decisions)
- **Two execution modes**: "Run Once for All Items" (recommended, 95% of use cases, uses `$input.all()`) and "Run Once for Each Item" (specialized, uses `$input.item`).
- **4 data access patterns**: `$input.all()` (array of all items), `$input.first()` (first item only), `$input.item` (current item in Each Item mode), `$node["Name"]` (reference other nodes).
- **Return format**: Must be `[{json: {...}}, ...]`. Missing the array wrapper, the `json` key, or returning raw data are the top 3 return format errors.
- **3 built-in functions**:
  - `$helpers.httpRequest({method, url, headers})` -- async HTTP client, the only way to make HTTP calls from Code nodes
  - `DateTime` -- Luxon library for date/time operations (`DateTime.now()`, `.toFormat()`, `.plus()`, `.minus()`)
  - `$jmespath(data, expression)` -- JMESPath JSON querying for complex data extraction
- **Top 5 errors** (ordered by frequency): (1) Empty code/missing return, (2) Expression syntax confusion (`{{ }}` in code), (3) Incorrect return wrapper, (4) Missing null checks, (5) Webhook body nesting.
- **6 best practices**: Validate input data, try-catch for error handling, prefer array methods (map/filter) over loops, filter early/process late, descriptive variable names, console.log for debugging.
- **10-item deployment checklist**: Code not empty, return exists, proper format, data access correct, no n8n expressions, error handling, webhook data via .body, mode selected, performance (map/filter), consistent output.
- **console.log()** output appears in browser console (F12 developer tools) -- the primary debugging mechanism.
- **Code node alternatives**: Set (simple mapping), Filter (basic filtering), IF/Switch (conditionals), HTTP Request (API calls only).

## Key Takeaways
- The return format `[{json: {...}}]` is the single most critical rule: getting it wrong causes immediate workflow failure with a cryptic error
- "Run Once for All Items" mode should be the default choice; "Each Item" mode is only for truly independent per-item processing
- `$helpers.httpRequest()` is the bridge between Code nodes and external APIs, filling the gap that the Python skill cannot fill (no `requests` library)
- The 5 common patterns (aggregation, regex, transformation, ranking, reporting) cover the vast majority of real-world Code node use cases
- This is the highest-scored skill (9.0 average) because it has the most thorough error prevention, the clearest decision guides, and the best modularity of all 7 skills
