---
date: 2026-03-26
type: learning
task: n8nskills-v24
tags: [n8n, skills, deep-dive, n8n-expression-syntax]
---

# n8n Expression Syntax

## Danh gia (Score)
| Criteria | Score | Justification |
|----------|:-----:|---------------|
| Completeness | 8/10 | Covers core variables ($json, $node, $now, $env), the critical webhook data structure gotcha, validation rules (4 rules), common mistakes quick-fix table, data type handling (arrays, objects, strings, numbers), advanced patterns (conditionals, date manipulation, string manipulation), expression helpers, and debugging guidance. Delegates to COMMON_MISTAKES.md and EXAMPLES.md. Gap: no coverage of `$execution`, `$workflow`, `$prevNode`, or `$runIndex` variables. |
| Usability | 9/10 | The webhook data structure section is marked as "CRITICAL" with clear wrong/correct examples -- this is the single most common expression error and it gets top billing. Quick-fix table maps 6 common mistakes to their solutions in a scannable format. "When NOT to Use Expressions" section (Code nodes, webhook paths, credential fields) prevents misuse. 3 working examples with realistic data structures. |
| Modularity | 9/10 | Clean 3-file structure: SKILL.md (syntax reference + rules), COMMON_MISTAKES.md (error catalog), EXAMPLES.md (real workflow examples). Each file has a distinct purpose. SKILL.md at 517 lines is well-scoped as a reference document. Cross-references to other skills are precise and bidirectional. |
| Safety | 8/10 | 4 explicit validation rules (always use {{}}, quotes for spaces, match exact node names, no nested {{}}). "When NOT to Use Expressions" prevents misuse in Code nodes, webhook paths, and credential fields. Debugging section with common error messages and their causes. However, no guidance on expression injection risks or handling user-provided data in expressions. |
| **Average** | **8.5** | |

## Uu diem
1. **Webhook data structure documentation is the single highest-value section across all 7 skills** -- the fact that webhook data is nested under `$json.body` (not `$json` directly) is the #1 most common mistake in n8n development. This skill marks it as "CRITICAL" with an explicit JSON structure showing `headers`, `params`, `query`, and `body` fields, plus 3 wrong/correct pairs. Every other skill that handles webhooks cross-references this section.
2. **"When NOT to Use Expressions" section prevents a class of errors** -- explicitly documents that Code nodes use direct JavaScript (not `{{ }}`), webhook paths must be static, and credential fields should use n8n's credential system. These negative rules prevent users from applying expression syntax in contexts where it does not apply.
3. **Comprehensive helper method reference** -- documents String methods (toLowerCase, toUpperCase, trim, replace, substring, split, includes), Array methods (length, map, filter, find, join, slice), DateTime/Luxon methods (toFormat, toISO, toLocal, plus, minus, set), and Number methods (toFixed, toString, math operators). This makes the SKILL.md a complete reference for expression authoring.

## Nhuoc diem
1. **Missing coverage of advanced variables** -- `$execution` (execution metadata), `$workflow` (workflow info), `$prevNode` (previous node reference), `$runIndex` (current run index in loops), and `$itemIndex` are not documented. These are needed for advanced workflows.
2. **No expression performance guidance** -- expressions are evaluated at runtime for every item; complex expressions with method chains or conditional logic can impact performance on large datasets. No guidance on when to move complex logic from expressions to Code nodes.
3. **DateTime examples use Luxon syntax without explaining it** -- references `$now.toFormat('yyyy-MM-dd')`, `$now.plus({days: 7})`, `DateTime.fromISO()` etc. but does not explain that n8n uses Luxon (not native JavaScript Date) or where to find Luxon documentation. The Luxon dependency is only visible if the user already knows to look for it.

## Kha nang tich hop vao he thong hien tai
This skill is the **data mapping layer** -- it is needed whenever data flows between nodes, which is essentially every workflow.

**Depends on:**
- **n8n-mcp-tools-expert** for expression validation through `validate_node` (which can catch `invalid_expression` errors)
- No other skill dependencies -- this is a foundational reference

**Depended on by:**
- **n8n-validation-expert** references this for fixing `invalid_expression` validation errors
- **n8n-workflow-patterns** references this for data mapping within every pattern (especially the webhook gotcha)
- **n8n-node-configuration** references this for configuring expression-based fields
- **n8n-code-javascript** and **n8n-code-python** both contrast their direct-access syntax with expression syntax, creating a clear boundary

This skill has the widest horizontal impact: it is referenced by 5 of the other 6 skills. Only n8n-mcp-tools-expert has comparable reach.

## Ghi chu ky thuat (Technical Decisions)
- **Expression format**: `{{expression}}` -- double curly braces required. Single braces, no braces, and triple braces are all invalid.
- **Core variables**: `$json` (current node output), `$node["Name"]` (specific node reference, case-sensitive), `$now` (Luxon DateTime), `$env` (environment variables).
- **Webhook data structure**: `$json.headers`, `$json.params`, `$json.query`, `$json.body` -- user payload is always under `.body`.
- **Bracket notation required for spaces**: `$json['field name']`, `$node["HTTP Request"]` -- dot notation fails silently for field names with spaces.
- **Node name references are case-sensitive**: `$node["HTTP Request"]` works, `$node["http request"]` does not.
- **Code node distinction**: Expressions use `{{$json.field}}`, Code nodes use `$json.field` or `$input.item.json.field` directly. Mixing syntaxes is the #2 most common mistake.
- **Prefix for field values**: When setting field values in node configurations, expressions use `=` prefix: `"name": "={{$json.body.name}}"`. The `=` tells n8n to evaluate the expression rather than treating it as literal text.
- **DateTime uses Luxon library**: `$now.toFormat('yyyy-MM-dd')`, `$now.plus({days: 7})`, `DateTime.fromISO()`. Standard JavaScript Date methods are not available.
- **String concatenation is automatic**: `Hello {{$json.body.name}}!` -- literal text and expressions can be mixed freely.
- **Debugging**: Expression editor shows live preview, errors highlighted in red. Common error messages: "Cannot read property 'X' of undefined" (wrong path), "X is not a function" (wrong type).

## Key Takeaways
- The webhook `$json.body` nesting is the single most impactful piece of knowledge in the entire n8n-skills ecosystem -- getting this wrong causes silent failures where fields return undefined
- The "no expressions in Code nodes" rule is the second most important boundary -- Code nodes use direct JavaScript/Python, not `{{ }}` syntax
- This skill serves as the universal reference that all other skills depend on for data mapping guidance
- Luxon DateTime is the date library (not native JavaScript Date), and this should be made more explicit in the documentation
- Expression syntax is deceptively simple (`{{ }}`) but the interaction with node references, bracket notation, and the `=` prefix creates enough complexity to warrant a dedicated skill
