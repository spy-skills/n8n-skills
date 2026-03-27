---
date: 2026-03-26
type: learning
task: n8nskills-fua
tags: [n8n, skills, deep-dive, n8n-mcp-tools-expert]
---

# n8n MCP Tools Expert

## Danh gia (Score)
| Criteria | Score | Justification |
|----------|:-----:|---------------|
| Completeness | 9/10 | Covers all 3 tool categories (Node Discovery, Validation, Workflow Management) plus Templates and Self-Help. Documents 17 operation types for `n8n_update_partial_workflow`. Includes performance characteristics table with response times (<20ms for search, 50-200ms for updates). Only gap: limited coverage of `n8n_executions` and `n8n_test_workflow` tools. |
| Usability | 9/10 | Excellent progressive structure: Quick Reference table at top, then Tool Selection Guide with step-by-step workflows, then Common Mistakes section with 6 specific anti-patterns. Real code examples for every tool. Clear "Do/Don't" best practices section. |
| Modularity | 7/10 | Delegates to 3 sub-guides (SEARCH_GUIDE.md, VALIDATION_GUIDE.md, WORKFLOW_GUIDE.md) which is good separation. However, the SKILL.md itself is 643 lines -- substantial overlap with the sub-guides. Could be more modular by reducing SKILL.md to pure orchestration. |
| Safety | 8/10 | Documents nodeType format differences (`nodes-base.*` vs `n8n-nodes-base.*`) preventing format mismatches. Warns about `detail: "full"` token waste (3-8K tokens vs 1-2K for standard). Documents auto-sanitization behavior and its limitations. Missing: no guidance on API key security or rate limiting of n8n API calls. |
| **Average** | **8.3** | |

## Uu diem
1. **Dual nodeType format documentation is exceptional** -- explicitly shows the `nodes-base.*` (search/validate) vs `n8n-nodes-base.*` (workflow) split with conversion examples, including the `workflowNodeType` field returned by `search_nodes`. This is the single most confusing aspect of the n8n-mcp API and it is handled perfectly.
2. **Real telemetry data grounds the guidance** -- 38,287 uses of `n8n_update_partial_workflow` at 99.0% success rate, 18s average between search and get_node, 56s average between edits, 23s thinking + 58s fixing in validation loops. These numbers set realistic user expectations.
3. **Smart parameters documentation** (`branch: "true"/"false"` for IF nodes, `case: 0` for Switch) eliminates the need for manual `sourceIndex` calculations, which was a major source of connection errors.

## Nhuoc diem
1. **Tool availability split is buried at line 493** -- the critical distinction between "Always Available" tools (search, validate, templates) and "Requires n8n API" tools (create, update, deploy) should be more prominent, ideally near the top of the document.
2. **Template search modes documented but not deeply explained** -- lists 4 modes (keyword, by_nodes, by_task, by_metadata) with examples but no guidance on which mode produces best results or how result quality differs between modes.
3. **No error handling patterns for API failures** -- documents what tools do on success but lacks guidance on handling n8n API connection failures, timeout scenarios, or partial workflow update failures.

## Kha nang tich hop vao he thong hien tai
This is the **highest-priority skill** and serves as the central orchestrator for the entire skill ecosystem. It is the only skill that directly interfaces with the n8n-mcp MCP server tools. All other skills depend on it:
- **n8n-validation-expert** depends on this for `validate_node` and `validate_workflow` tool usage
- **n8n-workflow-patterns** depends on this for `n8n_create_workflow` and `n8n_update_partial_workflow`
- **n8n-node-configuration** depends on this for `get_node` and `search_nodes`
- **n8n-expression-syntax** uses `validate_node` through this skill's guidance
- **n8n-code-javascript** and **n8n-code-python** reference `search_nodes({query: "code"})` and `get_node_essentials`

The skill also references `tools_documentation()` and `ai_agents_guide()` as self-help tools, creating a meta-documentation layer.

## Ghi chu ky thuat (Technical Decisions)
- **get_node detail levels**: `minimal` (~200 tokens), `standard` (~1-2K tokens, recommended), `full` (~3-8K tokens, use sparingly). The 95% coverage claim for standard detail is the key design decision.
- **get_node operation modes**: `info` (default), `docs` (markdown), `search_properties` (with `propertyQuery`), `versions`, `compare`, `breaking`, `migrations` -- 7 distinct modes in a single unified tool.
- **validate_node profiles**: `minimal`, `runtime` (recommended), `ai-friendly`, `strict` -- 4 profiles with different strictness levels.
- **n8n_update_partial_workflow** supports 17 operation types including `activateWorkflow` and `deactivateWorkflow`, plus `cleanStaleConnections`.
- **n8n_deploy_template** has `autoFix: true` and `autoUpgradeVersions: true` defaults, making template deployment safe by default.
- **Performance targets**: search_nodes <20ms, get_node standard <10ms, validate_node full <100ms, n8n_update_partial_workflow 50-200ms.
- **`intent` parameter** on workflow updates provides context for better AI-generated responses -- a subtle but important API design choice.
- **search_nodes returns both formats**: `nodeType` for search/validate and `workflowNodeType` for workflow tools, eliminating manual conversion.

## Key Takeaways
- This skill is the "gateway" to the entire n8n-mcp ecosystem -- without it, the other 6 skills cannot effectively interface with the MCP server
- The iterative workflow building pattern (56s average between edits) is the single most important behavioral insight: workflows should NOT be built in one shot
- The nodeType format duality (`nodes-base.*` vs `n8n-nodes-base.*`) is the #1 source of "Node not found" errors and this skill is the only place it is comprehensively documented
- Smart parameters (`branch`, `case`) and the `intent` parameter represent v1.1.0 API improvements that significantly reduce connection configuration errors
