---
date: 2026-03-26
type: learning
task: n8nskills-6xb
tags: [n8n, skills, deep-dive, n8n-workflow-patterns]
---

# n8n Workflow Patterns

## Danh gia (Score)
| Criteria | Score | Justification |
|----------|:-----:|---------------|
| Completeness | 8/10 | Covers 5 core patterns (Webhook Processing, HTTP API Integration, Database Operations, AI Agent Workflow, Scheduled Tasks) with dedicated sub-files for each. Includes pattern selection guide, data flow patterns (linear, branching, parallel, loop, error handler), workflow creation checklist, and real template examples. Gap: no coverage of error-first or event-driven patterns. |
| Usability | 9/10 | Outstanding pattern selection guide with clear "Use when" criteria and concrete examples for each pattern. Quick start examples (5 workflows from 3-5 nodes) give immediate starting points. The 4-phase workflow creation checklist (Planning, Implementation, Validation, Deployment) is actionable. Statistics section (trigger distribution, transformation frequency) grounds decisions in data. |
| Modularity | 9/10 | Excellent separation: SKILL.md is the pattern catalog and selection guide, while 5 dedicated files (webhook_processing.md, http_api_integration.md, database_operations.md, ai_agent_workflow.md, scheduled_tasks.md) contain deep implementation details. Each pattern file is independently useful. |
| Safety | 7/10 | Documents 5 common gotchas (webhook data structure, multiple input items, authentication issues, node execution order, expression errors). Includes error handling as a workflow component. However, lacks specific error handling patterns per workflow type -- e.g., no guidance on webhook timeout handling, API retry strategies, or database transaction rollback patterns within the pattern descriptions. |
| **Average** | **8.3** | |

## Uu diem
1. **Pattern statistics provide evidence-based decision making** -- trigger distribution (Webhook 35%, Schedule 28%, Manual 22%, Service 15%), transformation usage (Set 68%, Code 42%, IF 38%, Switch 18%), output distribution (HTTP Request 45%, Slack 32%, Database 28%, Email 24%), and complexity distribution (Simple 42%, Medium 38%, Complex 20%). These numbers help users understand what is "normal" and choose accordingly.
2. **5 data flow patterns with visual diagrams** -- Linear, Branching, Parallel Processing, Loop (Split in Batches), and Error Handler patterns each with ASCII flow diagrams and clear "Use when" criteria. The Loop pattern with `Split in Batches` is particularly valuable as it is a non-obvious n8n-specific construct.
3. **AI Agent Workflow pattern captures the langchain architecture** -- documents the `ai_languageModel`, `ai_tool`, and `ai_memory` connection types used by n8n's AI agent nodes, with a concrete 4-node example (Webhook -> AI Agent with Model + Tools + Memory -> Webhook Response). This is the fastest-growing pattern category.

## Nhuoc diem
1. **Pattern sub-files vary in depth** -- the ai_agent_workflow.md is the most comprehensive while other patterns may have less implementation detail. The SKILL.md lists all 5 but the depth of guidance for each varies.
2. **No pattern composition guidance** -- while the 5 patterns are well-documented individually, there is no explicit guidance on combining patterns (e.g., a Webhook-triggered workflow that calls APIs and writes to a database). Real workflows often combine 2-3 patterns.
3. **Execution order documentation is minimal** -- mentions v0 (top-to-bottom, legacy) vs v1 (connection-based, recommended) but does not explain the practical implications, especially for parallel branches or merge nodes where execution order matters significantly.

## Kha nang tich hop vao he thong hien tai
This skill serves as the **architectural planning layer** -- it is the first skill users should consult when starting a new workflow, before touching any tools or configurations.

**Depends on:**
- **n8n-mcp-tools-expert** for `search_nodes`, `n8n_create_workflow`, `n8n_deploy_template`, `search_templates`, and `ai_agents_guide()`
- **n8n-expression-syntax** for data mapping within patterns (especially webhook `$json.body` access)
- **n8n-node-configuration** for configuring specific operations within each pattern

**Depended on by:**
- **n8n-mcp-tools-expert** references this for workflow building guidance
- **n8n-validation-expert** references this through the workflow creation checklist validation phase
- **n8n-code-javascript** and **n8n-code-python** fit into the "transformation" building block of patterns

The workflow creation checklist (Planning -> Implementation -> Validation -> Deployment) creates a natural flow through the other skills: Patterns for planning, MCP Tools Expert for implementation, Node Configuration for setup, Validation Expert for verification.

## Ghi chu ky thuat (Technical Decisions)
- **5 core patterns cover 90%+ of use cases** -- this claim is supported by the trigger distribution data (Webhook + Schedule + Manual + Service = 100% of triggers, each mapping to one of the 5 patterns).
- **Webhook Processing is the most common pattern** at 35% of triggers. Critical gotcha: data nested under `$json.body`.
- **AI Agent Workflow uses 3 AI connection types**: `ai_languageModel` (for LLM), `ai_tool` (for tools), `ai_memory` (for conversation memory). These are distinct from the standard `main` connection type.
- **Template #2947** (Weather to Slack) is the canonical example: Schedule -> HTTP Request -> Set -> Slack (4 nodes, Simple complexity).
- **5 building block categories**: Triggers (Webhook, Schedule, Manual, Polling), Data Sources (HTTP, DB, Service, Code), Transformation (Set, Code, IF/Switch, Merge), Outputs (HTTP, DB, Communication, Storage), Error Handling (Error Trigger, IF, Stop and Error, Continue On Fail).
- **Average workflow complexity**: Simple 3-5 nodes (42%), Medium 6-10 nodes (38%), Complex 11+ nodes (20%).
- **Workflow activation** is via `activateWorkflow` operation in `n8n_update_partial_workflow`, referenced in the deployment phase checklist.
- **Iterative building** is reinforced: "avg 56s between edits" -- consistent with the MCP Tools Expert telemetry.

## Key Takeaways
- Start every workflow project by identifying which of the 5 core patterns applies, then use the detailed pattern file for implementation guidance
- The pattern statistics reveal that simple workflows (3-5 nodes) are the most common at 42%, reinforcing the "start simple, iterate" philosophy
- AI Agent Workflows are the fastest-growing pattern and have unique connection types (`ai_languageModel`, `ai_tool`, `ai_memory`) that differ from standard `main` connections
- The workflow creation checklist is the most actionable artifact in this skill -- it provides a repeatable process that naturally integrates all 7 skills
- Pattern composition (combining multiple patterns in one workflow) is an important gap that could be addressed in a future update
