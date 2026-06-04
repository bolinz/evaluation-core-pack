# Cross-Platform Tool Mapping

The evaluator SKILL.md files in this pack reference tool names specific to certain agent platforms. Below are the equivalents across supported platforms.

## File Operations

| Task | opencode | Copilot CLI / VS Code | Claude Code | Gemini CLI |
|------|----------|----------------------|-------------|------------|
| Read file | `Read` | `read` file content via context | `Read` | `read` |
| Search file content | `Grep` | shell: `grep`, `rg` | `Grep` | shell: `grep` |
| Find files by pattern | `Glob` | shell: `find`, `ls` | `Glob` | shell: `find` |
| Write file | `Write` | `write` tool (edit) | `Write` | `write` |
| Edit file | `Edit` | edit via chat response | `Edit` | `edit` |

## Execution

| Task | opencode | Copilot CLI / VS Code | Claude Code | Gemini CLI |
|------|----------|----------------------|-------------|------------|
| Run command | `Bash` | shell execution | `Bash` | `bash` |
| Execute script | `Bash` | shell approval | `Bash` | `bash` |

## Agent/Skill Management

| Task | opencode | Copilot CLI / VS Code | Claude Code | Gemini CLI |
|------|----------|----------------------|-------------|------------|
| Load skill | `Skill` tool | auto-discovered from `~/.copilot/skills/` | `Skill` tool | `activate_skill` |
| Delegate to agent | `Task` subagent | built-in agents (explore, task, research) | `Task` subagent | N/A |
| Web fetch | `webfetch` | MCP server / shell `curl` | `WebFetch` | `web` |

## Best Practices

- **Prefer shell commands** (`grep`, `find`, `cat`) when writing cross-platform instructions, as shell is available on all platforms
- **Use file read/write commands** that work across all environments
- **Mention platform alternatives** in parentheses where relevant, e.g., "Use `Read` (or `cat` on Copilot CLI) to load the file"
