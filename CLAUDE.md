# General rules
- Keep the answer short and concise, no yapping
- Before you answer, tell me what additional context would make your response better.
- You don't have to be fully agree with me on all topics, feel free to feedback if my approach is wrong. Keep the conversation constructive
 
## Context Management
 
Context is the most important resource in this project.
 
Proactively use subagents (Task tool) to keep large exploration, research, and verbose operations out of the main conversation. The main thread should remain focused, clean, and summary-driven.

---

### Git Workflow

- **Remote**: https://github.com/baona1109/gitops-observability.git
- **Branch**: main
- **Commit frequency**: Commit and push to GitHub regularly after completing meaningful work to preserve progress and maintain a clear work history
- **Commit message**: Use clean, descriptive commit messages in format `<type>: <description>` with Claude co-author attribution
  - Example types: `feat:` (new feature), `fix:` (bug fix), `refactor:` (code reorganization), `docs:` (documentary)
  - Always include co-author line: `Co-Authored-By: Claude Haiku 4.5`
- **Push after commits**: Always push commits to GitHub immediately after committing to ensure work is backed up

---
 
### Default: Spawn a Subagent For
 
Use a subagent when the task involves:
 
- Codebase exploration (reading 3+ files to answer a question)
- Research tasks (web searches, documentation lookups, understanding unfamiliar systems)
- Code review or deep analysis
- Any investigation where only the summary matters
- Generating verbose intermediate output the user does not need to see
 
Subagents should:
- Work in isolation
- Perform all necessary reads/searches
- Return a concise, structured summary
- Avoid dumping raw file contents unless explicitly requested
 
---
 
### Stay in Main Context For
 
Do NOT spawn a subagent when:
 
- The user requests direct file edits
- The task requires reading only 1–2 targeted files
- The task involves back-and-forth iteration
- The user needs to see intermediate reasoning or steps
- The task is small and self-contained
 
---
 
### Rule of Thumb
 
If a task will:
- Read more than ~3 files, OR
- Produce output the user does not need verbatim
 
Delegate it to a subagent and return only a summary.
 
---
 
### Goal
 
Keep the main conversation:
 
- Concise
- High-signal
- Summary-focused
- Efficient in token usage
 
Subagents are disposable investigators. 
The main thread is the decision-making layer.

---

### Code Review Standards
After completing any implementation, review the code for:
- Functions longer than 30 lines (likely doing too much)
- Logic duplicated more than twice (extract to utility)
- Any `any` type usage in TypeScript (replace with real types)
- Components with more than 3 props that could be grouped into an object
- Missing error handling on async operations

Run /simplify before presenting code to the user.

<!-- code-review-graph MCP tools -->
## MCP Tools: code-review-graph

**IMPORTANT: This project has a knowledge graph. ALWAYS use the
code-review-graph MCP tools BEFORE using Grep/Glob/Read to explore
the codebase.** The graph is faster, cheaper (fewer tokens), and gives
you structural context (callers, dependents, test coverage) that file
scanning cannot.

### When to use graph tools FIRST

- **Exploring code**: `semantic_search_nodes` or `query_graph` instead of Grep
- **Understanding impact**: `get_impact_radius` instead of manually tracing imports
- **Code review**: `detect_changes` + `get_review_context` instead of reading entire files
- **Finding relationships**: `query_graph` with callers_of/callees_of/imports_of/tests_for
- **Architecture questions**: `get_architecture_overview` + `list_communities`

Fall back to Grep/Glob/Read **only** when the graph doesn't cover what you need.

### Key Tools

| Tool | Use when |
|------|----------|
| `detect_changes` | Reviewing code changes — gives risk-scored analysis |
| `get_review_context` | Need source snippets for review — token-efficient |
| `get_impact_radius` | Understanding blast radius of a change |
| `get_affected_flows` | Finding which execution paths are impacted |
| `query_graph` | Tracing callers, callees, imports, tests, dependencies |
| `semantic_search_nodes` | Finding functions/classes by name or keyword |
| `get_architecture_overview` | Understanding high-level codebase structure |
| `refactor_tool` | Planning renames, finding dead code |

### Workflow

1. The graph auto-updates on file changes (via hooks).
2. Use `detect_changes` for code review.
3. Use `get_affected_flows` to understand impact.
4. Use `query_graph` pattern="tests_for" to check coverage.
