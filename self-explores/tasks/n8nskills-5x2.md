---
date: 2026-03-26
type: task-worklog
task: n8nskills-5x2
title: "Deep Dive: n8n-node-configuration"
status: open
detailed_at: 2026-03-26
detail_score: ready-for-dev
tags: [n8n, skills, research]
---

# Deep Dive: n8n-node-configuration [P2] — Detailed Design

## 1. Objective
Perform a deep-dive analysis of the n8n-node-configuration skill — the operational skill that teaches AI assistants how to configure n8n nodes with operation-aware guidance, detail levels, property dependencies, and common configuration patterns.

## 2. Scope
**In-scope:**
- Full analysis of SKILL.md (~786 lines) — the longest SKILL.md in the collection
- DEPENDENCIES.md — property dependency chains, conditional fields, cascading configurations
- OPERATION_PATTERNS.md — operation-specific configuration templates, common patterns per node type
- Operation-aware configuration analysis: how configuration changes based on selected operation
- Detail level guidance: minimal vs standard vs full configuration approaches
- Property dependency mapping: which fields appear/change based on other field values
- Node type pattern library assessment
- Scoring on completeness/usability/modularity/safety (1-10)

**Out-of-scope:**
- Configuring actual n8n nodes against live instance
- Exhaustive testing of all 800+ n8n node types
- Modifying configuration guidance
- Building new operation patterns

## 3. Input / Output
**Input:**
- `skills/n8n-node-configuration/SKILL.md` (~786 lines)
- `skills/n8n-node-configuration/DEPENDENCIES.md`
- `skills/n8n-node-configuration/OPERATION_PATTERNS.md`
- `self-explores/learn/overview/analysis.md` (overview context from n8nskills-10p)

**Output:**
- `self-explores/learn/n8n-node-configuration/analysis.md` — deep-dive analysis with: Score 1-10, Uu/Nhuoc, Tich hop, Technical Decisions

## 4. Dependencies
- **n8nskills-10p** (provides overview context and initial scoring baseline)

## 5. Flow xu ly

### Step 1: Read SKILL.md Full Analysis (~12 min)
Read `skills/n8n-node-configuration/SKILL.md` completely (~786 lines — longest in collection). Document:
- Section hierarchy and content distribution by section
- Frontmatter metadata (triggers, activation patterns)
- Operation-aware configuration concept: how the skill teaches that node config depends on the selected operation
- Detail level guidance: when to use minimal/standard/full from `get_node`
- Node configuration workflow (discover -> get_node -> configure -> validate)
- Common configuration patterns across node types
- Property dependency introduction (links to DEPENDENCIES.md)
- Node types explicitly covered vs generalized guidance
- Cross-references to n8n-mcp-tools-expert (especially `get_node` usage)

**Verify:** Operation-aware concept clearly documented. At least 3 node types have explicit examples.

### Step 2: Read DEPENDENCIES.md (~10 min)
Read `skills/n8n-node-configuration/DEPENDENCIES.md` completely. Analyze:
- Dependency types documented (conditional fields, cascading, mutual exclusion)
- Dependency chain examples (field A -> reveals field B -> enables field C)
- How to discover dependencies using `get_node` with `search_properties` mode
- Common dependency patterns across node types
- Edge cases: circular dependencies, optional dependency chains
- Guidance for handling unknown dependencies in new/unfamiliar nodes

**Verify:** At least 3 dependency types identified. Discovery workflow documented.

### Step 3: Read OPERATION_PATTERNS.md (~10 min)
Read `skills/n8n-node-configuration/OPERATION_PATTERNS.md` completely. Analyze:
- Number of operation patterns documented
- Pattern structure (operation name -> required fields -> optional fields -> example config)
- Node types covered (Slack, Google Sheets, HTTP Request, etc.)
- Pattern completeness per node type (all operations vs subset)
- Common patterns that apply across multiple node types
- Pattern template usability: can an AI directly use these as configuration blueprints?

**Verify:** At least 5 distinct operation patterns documented. Patterns include both required and optional fields.

### Step 4: Analyze Integration with get_node API (~8 min)
Map how this skill integrates with n8n-mcp-tools-expert's `get_node` tool:
- `get_node(name, detail_level="minimal")` -> basic node info for discovery
- `get_node(name, detail_level="standard")` -> operation list and common config
- `get_node(name, detail_level="full")` -> complete property schema with dependencies
- `get_node(name, mode="search_properties")` -> find specific properties by name

Assess whether the skill teaches this progressive disclosure pattern effectively:
1. Search for node -> get basic info -> select operation -> get full config -> configure
2. Is each step in this chain clear and actionable?

**Verify:** Progressive disclosure pattern identified in SKILL.md. All 4 get_node modes referenced.

### Step 5: Score, Analyze, and Write Output (~12 min)
Score the skill 1-10:
- **Completeness**: Given 800+ n8n nodes, does the pattern library cover enough? Are dependencies thorough?
- **Usability**: Can an AI configure a node by following the operation-aware guidance without ambiguity?
- **Modularity**: Clean interface with tools-expert (get_node) and validation-expert (validate_node)?
- **Safety**: Does it prevent misconfiguration? Are required fields clearly marked?

Write `self-explores/learn/n8n-node-configuration/analysis.md` with:
- Metadata header
- Executive summary (operation-aware configuration, why 786 lines)
- SKILL.md structural analysis (section map, line distribution)
- Property dependency deep-dive (types, chains, discovery)
- Operation patterns assessment (coverage, quality, usability)
- get_node integration analysis (progressive disclosure)
- Scoring table with evidence
- Uu / Nhuoc / Tich hop sections
- Technical Decisions (why operation-aware? why these detail levels?)
- Recommendations

**Verify:** Output file exists, all sections present, property dependency analysis included, scores assigned.

## 6. Edge Cases & Error Handling
| Case | Trigger | Expected | Recovery |
|------|---------|----------|----------|
| SKILL.md is long but shallow | 786 lines with lots of boilerplate | Assess content density, not just length | Use concepts-per-line metric |
| DEPENDENCIES.md covers few node types | Only 2-3 nodes with dependency examples | Note limited coverage | Score completeness lower, recommend expansion |
| OPERATION_PATTERNS don't match current n8n | Node operations changed since patterns written | Flag as version concern | Note in Technical Decisions |
| get_node modes not all referenced | SKILL.md only uses some detail levels | Gap in progressive disclosure | Score modularity lower |
| 800+ nodes but only 10 patterns | Pattern library covers <2% of nodes | Assess if patterns are generalizable | Score based on generalizability, not raw count |

## 7. Acceptance Criteria
- **Happy 1:** Given SKILL.md (786L) and 2 reference files are read, When analysis completes, Then output documents operation-aware configuration concept with at least 3 concrete node examples
- **Happy 2:** Given DEPENDENCIES.md exists, When dependency analysis runs, Then output contains at least 3 dependency types with chain examples
- **Negative:** Given OPERATION_PATTERNS.md has patterns for fewer than 3 node types, When coverage assessment runs, Then analysis flags limited coverage and provides specific expansion recommendations

## 8. Technical Notes
- At 786 lines, this is the longest SKILL.md — suggests node configuration is the most complex topic
- The operation-aware approach is a key design decision: n8n nodes change their available properties based on the selected operation (e.g., Google Sheets "Read" vs "Write" have completely different fields)
- Property dependencies are a tree structure: operation -> sub-operation -> fields -> sub-fields
- This skill bridges n8n-mcp-tools-expert (discovery) and n8n-validation-expert (validation) — it's the "configuration" step in the discover -> configure -> validate pipeline
- The 800+ node count means this skill MUST teach generalizable patterns, not exhaustive node-by-node guides

## 9. Risks
- **Information density**: 786 lines may contain redundant content or could be refactored. Mitigation: Assess content density explicitly.
- **Node version drift**: n8n updates frequently; operation patterns may become outdated. Mitigation: Note which patterns are version-sensitive.
- **Generalizability challenge**: Patterns for 5-10 nodes must apply to 800+. Mitigation: Evaluate whether patterns teach principles or just examples.

## Worklog
*(Chua bat dau)*
