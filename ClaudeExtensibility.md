# Claude Code Complete Extensibility Model

## The 7 Core Extensibility Mechanisms

### 1. CLAUDE.md
**What:** Project memory file - "constitution" for your project
**Location:** `project-root/CLAUDE.md`
**Activation:** Always loaded, Claude reads it at session start
**Use case:** Project conventions, commands, architecture, coding standards
**Characteristics:**
- Plain markdown file
- Always in context (not progressive)
- Highest priority project context
- Team-shared via Git

**Example:**
```markdown
# Project: E-Commerce Platform

## Tech Stack
- Next.js 14, TypeScript, Tailwind
- PostgreSQL via Prisma
- Deploy: Vercel

## Commands
- `npm run dev` - Start dev server
- `npm run db:migrate` - Run migrations

## Conventions
- Use server actions, not API routes
- All components in /components
- Tests required for business logic
```

---

### 2. Skills
**What:** Auto-invoked workflow/process knowledge
**Location:** `.claude/skills/` or `~/.claude/skills/` or via plugin
**Activation:** Automatic when description matches task
**Use case:** Repeatable workflows, standards, procedures
**Characteristics:**
- Progressive disclosure (metadata → full content → resources)
- Can include scripts, references, assets
- Auto-triggered by Claude
- ~100 tokens overhead per skill

**Already covered in detail!**

---

### 3. MCP Servers
**What:** Data/tool access layer - connect to external systems
**Location:** `.mcp.json` or via plugin
**Activation:** Tools available when server is running
**Use case:** Database access, API integration, external services
**Characteristics:**
- Standardized protocol (stdio, SSE, HTTP, WebSocket)
- Provides tools/resources/prompts
- Can be bundled in plugins
- Requires server process

**Already covered in detail!**

---

### 4. Slash Commands
**What:** User-triggered shortcuts - saved prompts
**Location:** `.claude/commands/` (project) or `~/.claude/commands/` (personal)
**Activation:** Manual - you type `/command-name`
**Use case:** Frequently-used prompts, workflows you want to trigger explicitly
**Characteristics:**
- Single markdown file per command
- Can accept arguments via `$ARGUMENTS`
- Great terminal autocomplete
- Can invoke subagents or reference skills

**Example:**
```markdown
// .claude/commands/fix-issue.md
Please analyze and fix the GitHub issue: $ARGUMENTS

Steps:
1. Use `gh issue view` to get details
2. Search codebase for relevant files
3. Implement fix
4. Write tests
5. Create PR
```

**Usage:** `/fix-issue 123`

**Differences from Skills:**
- Skills = Auto-triggered, complex (multiple files)
- Commands = Manual trigger, simple (single file prompt)

---

### 5. Subagents
**What:** Specialized AI agents for isolated tasks
**Location:** `.claude/agents/` (project) or `~/.claude/agents/` (personal)
**Activation:** Invoked by main Claude or Task tool
**Use case:** Context isolation, parallel work, specialized expertise
**Characteristics:**
- Separate context from main conversation
- Can have different tool permissions
- Can use different models
- Can reference skills

**Example:**
```markdown
// .claude/agents/code-reviewer/AGENT.md
---
name: code-reviewer
description: Expert code review specialist
tools: Read, Grep, Glob
model: claude-sonnet-4-5-20250929
skills: security-patterns, code-standards
---

You are a senior code reviewer ensuring:
- Security best practices
- Performance optimization
- Code maintainability
- Test coverage
```

**Invocation:**
- Automatic: Claude decides to use Task tool
- Manual: "Use the code-reviewer subagent"

**Benefits:**
- Keeps main context clean
- Specialized expertise
- Parallel execution
- Different tool access per agent

---

### 6. Hooks
**What:** Automatic event-driven shell commands
**Location:** `.claude/settings.json` (configured via `/hooks` command)
**Activation:** Triggered by lifecycle events (PreToolUse, PostToolUse, etc.)
**Use case:** Enforcement, automation, validation, notifications
**Characteristics:**
- Deterministic (always runs, not LLM-decided)
- Shell commands that execute at specific events
- Can block/allow/modify Claude's actions
- Security-sensitive (runs with your credentials)

**Hook Events:**
- `PreToolUse` - Before tool calls (can block)
- `PostToolUse` - After tool calls
- `PermissionRequest` - When permissions needed (can allow/deny)
- `PreCompact` - Before context compaction
- `SessionStart` - Session initialization
- `SessionEnd` - Session cleanup
- `SubagentStop` - When subagent finishes

**Example: Auto-format on file edits**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write ${file}"
          }
        ]
      }
    ]
  }
}
```

**Use cases:**
- Auto-format code after edits
- Validate before writes
- Require approval for bash commands
- Desktop notifications when Claude needs input
- Log all commands for compliance
- Block modifications to production files

**Differences from Skills:**
- Skills = LLM decides to apply
- Hooks = Always runs deterministically at event

---

### 7. Plugins
**What:** Bundled distribution package containing any combination above
**Location:** Git repository with `.claude-plugin/` directory
**Activation:** Installed via `/plugin install`
**Use case:** Package and share complete setups
**Characteristics:**
- Can bundle: Skills, MCP, Commands, Subagents, Hooks
- Marketplace-based distribution
- Version controlled
- Team sharing

**Already covered in detail!**

---

## Complete Extensibility Stack

```
┌─────────────────────────────────────────────────┐
│  CLAUDE.md (Always-on project context)          │
├─────────────────────────────────────────────────┤
│  Skills (Auto-triggered workflows)              │
├─────────────────────────────────────────────────┤
│  Slash Commands (Manual shortcuts)              │
├─────────────────────────────────────────────────┤
│  Subagents (Specialized agents)                 │
├─────────────────────────────────────────────────┤
│  Hooks (Event-driven automation)                │
├─────────────────────────────────────────────────┤
│  MCP Servers (External tool/data access)        │
└─────────────────────────────────────────────────┘
            ↑
      All bundled in
         Plugins
```

---

## When to Use Each

| Mechanism | When to Use | Example |
|-----------|-------------|---------|
| **CLAUDE.md** | Project context that's always relevant | Tech stack, commands, conventions |
| **Skills** | Repeatable workflows you want auto-applied | Code review process, API design standards |
| **MCP** | Connect to external data/tools | Database, GitHub, company APIs |
| **Slash Commands** | Quick prompts you trigger manually | `/fix-issue`, `/deploy`, `/review-pr` |
| **Subagents** | Isolated/parallel work, context separation | Code reviewer, test generator, researcher |
| **Hooks** | Enforce rules deterministically | Auto-format, require approvals, logging |
| **Plugins** | Package & share complete setups | Team standards, company tools, public utilities |

---

## Real-World Combination Example

**Scenario:** Azure deployment workflow

```
Plugin: company-azure-deployment
├── CLAUDE.md (not in plugin, in project)
│   "Use Azure deployment standards from the skill"
│
├── .mcp.json
│   └── azure-data server (subscription lookup)
│
├── skills/
│   └── azure-deployment/
│       ├── SKILL.md (workflow process)
│       ├── references/ (ARM templates)
│       └── scripts/ (validation scripts)
│
├── commands/
│   └── deploy.md
│       "/deploy staging api-service"
│
├── agents/
│   └── cost-estimator/
│       AGENT.md (estimates deployment costs)
│
└── hooks/
    └── PreToolUse
        Block deployments to prod without approval
```

**How they work together:**

1. **CLAUDE.md** says "We use Azure deployment standards"
2. **Skill** auto-activates when you mention "deploy to Azure"
3. **Skill** calls **MCP tool** to get subscription ID
4. **Skill** uses **script** to validate ARM template
5. User types **/deploy** **slash command** for quick deployments
6. **Subagent** (cost-estimator) runs in parallel to estimate costs
7. **Hook** (PreToolUse) blocks prod deployments without approval
8. All bundled in **Plugin** for team distribution

---

## Additional Mechanisms (Beyond Core 7)

### 8. Settings & Configuration
- `.claude/settings.json` - Project settings
- `~/.claude/settings.json` - Global settings
- Environment variables
- Managed settings (enterprise)

### 9. Extended Thinking
- Not a "mechanism" but a capability
- Trigger with "think", "think hard", "think harder", "ultrathink"
- Gives Claude more compute time for complex reasoning

### 10. Headless Mode
- Run Claude Code non-interactively
- For CI/CD, automation, GitHub Actions
- `claude -p "prompt" --output-format stream-json`

### 11. Git Worktrees
- Not Claude-specific but integrated
- Allows parallel work in isolated directories
- Prevents context pollution

---

## Distribution Layers

### Project-Level (Committed to Git)
- `CLAUDE.md`
- `.claude/skills/`
- `.claude/commands/`
- `.claude/agents/`
- `.claude/mcp.json`
- `.claude/settings.json`

### User-Level (Personal Machine)
- `~/.claude/skills/`
- `~/.claude/commands/`
- `~/.claude/agents/`
- `~/.claude/settings.json`

### Plugin-Level (Marketplace)
- All of the above bundled
- Distributed via Git
- Versioned and updated

---

## Pattern: Progression Path

```
Experiment → Solidify → Share

1. Start in CLAUDE.md (quick project notes)
2. Extract to Skill (repeatable workflow)
3. Add Slash Command (manual trigger)
4. Add Subagent (context isolation)
5. Add Hook (enforce deterministically)
6. Bundle in Plugin (share with team)
7. Publish to Marketplace (share with community)
```

---

## Security Considerations

**Most Dangerous (Execute Code):**
- Hooks (runs shell commands automatically)
- MCP Servers (network access, external tools)
- Subagents (can have different permissions)

**Moderately Dangerous:**
- Skills (can include executable scripts)
- Slash Commands (saved prompts, manual trigger)

**Safest:**
- CLAUDE.md (just text/context)

**Always:**
- Only install from trusted sources
- Review code before using
- Understand what permissions you're granting

---

## Summary Table

| # | Mechanism | Type | Activation | Scope | Can Bundle In Plugin |
|---|-----------|------|------------|-------|---------------------|
| 1 | CLAUDE.md | Context | Always | Project | No (project file) |
| 2 | Skills | Workflow | Auto | Project/User/Plugin | Yes |
| 3 | MCP | Data/Tools | Manual config | Project/User/Plugin | Yes |
| 4 | Slash Commands | Prompts | Manual `/cmd` | Project/User/Plugin | Yes |
| 5 | Subagents | Agents | Auto/Manual | Project/User/Plugin | Yes |
| 6 | Hooks | Automation | Event-driven | Project/User/Plugin | Yes |
| 7 | Plugins | Package | Install once | Marketplace | N/A (is the bundle) |

---

## Next Steps

To explore each mechanism:

1. **CLAUDE.md**: Create one in your project root
2. **Skills**: Start with pattern #1 from previous artifact
3. **MCP**: Try pattern #2 (MCP-only plugin)
4. **Slash Commands**: Add `.claude/commands/hello.md`
5. **Subagents**: Create `.claude/agents/reviewer/AGENT.md`
6. **Hooks**: Run `/hooks` in Claude Code
7. **Plugins**: Bundle everything together

Each mechanism solves different problems - use them together for powerful workflows!