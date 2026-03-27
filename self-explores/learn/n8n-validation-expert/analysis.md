---
date: 2026-03-26
type: learning
task: n8nskills-5fe
tags: [n8n, skills, deep-dive, n8n-validation-expert]
---

# n8n Validation Expert

## Danh gia (Score)
| Criteria | Score | Justification |
|----------|:-----:|---------------|
| Completeness | 9/10 | Covers all 5 error types (missing_required, invalid_value, type_mismatch, invalid_expression, invalid_reference), 3 severity levels (errors/warnings/suggestions), 4 validation profiles, auto-sanitization system (binary/unary operators, IF/Switch metadata), 4 recovery strategies, and false positive guidance. Delegates deep catalogs to ERROR_CATALOG.md and FALSE_POSITIVES.md. |
| Usability | 8/10 | Excellent validation loop documentation with telemetry (7,841 occurrences, 23s thinking, 58s fixing). Step-by-step iteration example with Slack node across 3 cycles. However, the document is 690 lines and could benefit from a quick-reference card at the top for experienced users. |
| Modularity | 8/10 | Clean separation: SKILL.md covers interpretation and process, ERROR_CATALOG.md covers exhaustive error types, FALSE_POSITIVES.md covers acceptable warnings. Each file has a distinct responsibility. Cross-references to n8n-mcp-tools-expert and n8n-expression-syntax are precise. |
| Safety | 9/10 | Strongest safety-oriented skill. Explicit "must fix before activation" guidance for errors. Documents auto-sanitization limitations (cannot fix broken connections, branch count mismatches, paradoxical corrupt states). Preview-before-apply pattern for `n8n_autofix_workflow` (`applyFixes: false` then `true`). Warns against deploying with unresolved errors. |
| **Average** | **8.5** | |

## Uu diem
1. **Auto-sanitization documentation is the most valuable section** -- precisely documents what gets auto-fixed (binary operators lose `singleValue`, unary operators gain `singleValue: true`, IF v2.2+/Switch v3.2+ get `conditions.options` metadata) AND what cannot be fixed (broken connections, branch count mismatches, paradoxical corrupt states). This prevents users from fighting auto-sanitization.
2. **4 recovery strategies provide escalation paths** -- Start Fresh (for severely broken configs), Binary Search (for execution-correct-but-wrong-output), Clean Stale Connections (`cleanStaleConnections` operation), and Auto-fix (`n8n_autofix_workflow` with preview). Each strategy has clear "when to use" criteria.
3. **False positive categorization is production-ready** -- identifies 4 common false positives (missing error handling, no retry logic, missing rate limiting, unbounded query) with "when acceptable" vs "when to fix" guidance, plus the `ai-friendly` profile as a systematic solution.

## Nhuoc diem
1. **Validation result structure documented but not all edge cases** -- shows the standard `{valid, errors, warnings, suggestions, summary}` response but does not document what happens when validation itself fails (e.g., invalid nodeType passed to validate_node, network errors).
2. **Workflow-level validation checks listed but not deeply explored** -- mentions node configurations, connections, expressions, and flow validation but the examples focus heavily on node-level validation. Workflow-level issues like circular dependencies and disconnected nodes get only brief mention.
3. **No quantitative guidance on profile selection** -- documents 4 profiles qualitatively but does not show how many additional warnings `strict` produces vs `runtime`, or what percentage of false positives `ai-friendly` eliminates. Numbers would help users choose.

## Kha nang tich hop vao he thong hien tai
This skill is the **quality gate** for the entire workflow building process. It sits between configuration (n8n-node-configuration) and deployment (n8n-mcp-tools-expert):

**Depends on:**
- **n8n-mcp-tools-expert** for `validate_node`, `validate_workflow`, and `n8n_autofix_workflow` tool usage
- **n8n-node-configuration** for understanding what fields are required (to interpret `missing_required` errors)
- **n8n-expression-syntax** for fixing `invalid_expression` errors

**Depended on by:**
- **n8n-workflow-patterns** references this for the "Validation Phase" in the workflow creation checklist
- **n8n-mcp-tools-expert** references this for interpreting validation results
- All skills indirectly depend on this for ensuring configuration correctness

The validation loop pattern (`configure -> validate -> fix -> repeat`) is the most commonly observed workflow (7,841 occurrences in telemetry), making this skill the most frequently invoked in practice.

## Ghi chu ky thuat (Technical Decisions)
- **5 error types with structured responses**: Each error includes `type`, `property`, `message`, and `fix` (for errors) or `suggestion` (for warnings). This structured format enables programmatic error handling.
- **Validation profiles**: `minimal` (required fields only), `runtime` (values + types, recommended), `ai-friendly` (reduced false positives), `strict` (maximum validation including best practices and security).
- **Auto-sanitization triggers**: Runs on `n8n_create_workflow`, `n8n_update_partial_workflow`, and any save operation. Affects ALL nodes in the workflow, not just the one being edited.
- **Binary operators** (equals, notEquals, contains, notContains, greaterThan, lessThan, startsWith, endsWith): `singleValue` property is removed.
- **Unary operators** (isEmpty, isNotEmpty, true, false): `singleValue: true` is added.
- **IF/Switch version thresholds**: IF v2.2+ and Switch v3.2+ get automatic metadata injection in `conditions.options`.
- **`n8n_autofix_workflow`** supports preview mode (`applyFixes: false`) before committing changes -- critical safety feature.
- **`cleanStaleConnections`** operation in `n8n_update_partial_workflow` removes references to non-existent nodes.
- **Validation loop telemetry**: 23 seconds average thinking about errors, 58 seconds average fixing them, usually 2-3 iterations.

## Key Takeaways
- Validation is iterative by design -- expecting 2-3 cycles is normal, not a sign of failure
- Auto-sanitization is a double-edged sword: it silently fixes operator structures but cannot handle structural issues like broken connections or branch count mismatches
- The `ai-friendly` validation profile exists specifically to reduce noise when AI systems are generating configurations, reducing false positive friction
- The preview-before-apply pattern (`n8n_autofix_workflow` with `applyFixes: false`) is the safest approach to automated fixing and should be the default recommendation
- Understanding the 3 severity levels (errors block execution, warnings are optional, suggestions are nice-to-have) prevents over-engineering validation responses
