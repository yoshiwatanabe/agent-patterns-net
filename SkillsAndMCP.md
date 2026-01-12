# Claude Code Plugin Patterns - Learning TODO List

## Plugin-Based Patterns (Marketplace Distribution)

### 1. Skills-Only Plugin
**What:** Pure workflow/process knowledge, no MCP servers
**Use case:** Code patterns, documentation templates, deployment procedures
**Example:** React component generator, API design guidelines, code review checklist
```
□ Create repo: react-patterns-plugin
  └── skills/ (component-generator, hook-patterns, etc.)
```

---

### 2. MCP-Only Plugin
**What:** Pure data/tool access layer, no skills
**Use case:** Database connections, API clients, company data lookup
**Example:** Azure subscription lookup, Salesforce data access, PostgreSQL query tools
```
□ Create repo: data-sources-mcp-plugin
  └── .mcp.json + servers/ (postgres, redis, azure-data)
```

---

### 3. Bundled Plugin (Skills + MCP Together)
**What:** Tightly coupled workflow + data layer
**Use case:** Complete solution where skills need specific MCP tools
**Example:** Azure deployment (skills reference bundled Azure data MCP)
```
□ Create repo: azure-complete-plugin
  ├── .mcp.json (bundled Azure MCP)
  ├── skills/ (azure-deployment)
  └── servers/ (azure-data)
```

---

### 4. Skills Plugin Referencing External MCP
**What:** Skills that use separately-installed MCP servers
**Use case:** Skills leverage common third-party MCPs (GitHub, Slack, etc.)
**Example:** PR automation skill that uses GitHub MCP (installed separately)
```
□ Create repo: github-workflow-plugin
  └── skills/ (pr-automation, issue-tracker)
      # References external github MCP tools
```

---

### 5. Private Company Marketplace
**What:** Internal-only plugins (private GitHub repo)
**Use case:** Proprietary company standards, internal tools, sensitive data
**Example:** Company coding standards, internal API clients, deployment procedures
```
□ Create repo: company-internal-plugins (private)
  ├── .claude-plugin/marketplace.json
  └── plugins/
      ├── company-standards/
      ├── internal-apis-mcp/
      └── deployment-sop/
```

---

### 6. Public Community Marketplace
**What:** Open source plugins for public use
**Use case:** Share general-purpose utilities with the community
**Example:** Web scraping utilities, data visualization, testing frameworks
```
□ Create repo: my-public-plugins (public)
  ├── .claude-plugin/marketplace.json
  └── plugins/
      ├── web-scraper/
      ├── data-viz/
      └── test-helpers/
```

---

### 7. Hybrid Marketplace (Public + Private Split)
**What:** Two repos - one public, one private
**Use case:** Share generic utilities publicly, keep proprietary stuff private
**Example:** Generic React patterns (public), company-specific implementation (private)
```
□ Create repo: my-public-plugins
  └── Generic, reusable utilities

□ Create repo: company-private-plugins
  └── Company-specific implementations
```

---

### 8. Plugin with Templates/Assets
**What:** Skills with bundled files (logos, templates, boilerplate)
**Use case:** Brand guidelines, document templates, code scaffolding
**Example:** Company presentation template, branded invoice generator, project boilerplate
```
□ Create repo: brand-guidelines-plugin
  └── skills/
      └── presentation-generator/
          ├── SKILL.md
          └── assets/
              ├── logo.png
              ├── template.pptx
              └── colors.json
```

---

### 9. Multi-Server MCP Plugin
**What:** One plugin bundling multiple MCP servers
**Use case:** Unified data access layer across multiple systems
**Example:** Company data hub (Azure + Salesforce + Confluence + Internal Wiki)
```
□ Create repo: company-data-hub-plugin
  └── .mcp.json
      └── servers/
          ├── azure-mcp/
          ├── salesforce-mcp/
          ├── confluence-mcp/
          └── wiki-mcp/
```

---

### 10. Language/Framework-Specific Plugin Collection
**What:** Focused plugins for specific tech stack
**Use case:** Team working with specific technologies
**Example:** Python data science (pandas helpers, ML workflows, viz templates)
```
□ Create repo: python-datascience-plugins
  └── plugins/
      ├── pandas-analysis/
      ├── ml-training/
      └── data-viz/
```

---

### 11. Plugin with Reference Documentation
**What:** Skills with large reference docs in references/ folder
**Use case:** Complex standards, extensive API docs, detailed procedures
**Example:** Company API standards, security compliance docs, architecture patterns
```
□ Create repo: api-standards-plugin
  └── skills/
      └── api-design/
          ├── SKILL.md
          └── references/
              ├── rest-guidelines.md
              ├── graphql-standards.md
              └── authentication.md
```

---

### 12. Plugin with Executable Scripts
**What:** Skills with bundled Python/Bash scripts
**Use case:** Complex transformations, external tool integration
**Example:** Database migration, code generation, deployment automation
```
□ Create repo: devops-automation-plugin
  └── skills/
      └── database-migration/
          ├── SKILL.md
          └── scripts/
              ├── migrate.py
              ├── rollback.sh
              └── validate.py
```

---

### 13. Team-Specific Marketplace
**What:** Marketplace auto-configured in project settings
**Use case:** Onboard new team members with zero manual setup
**Example:** Project .claude/settings.json pre-configures team marketplace + plugins
```
□ Create repo: team-a-plugins
□ Add to project: .claude/settings.json
  {
    "extraKnownMarketplaces": {...},
    "enabledPlugins": {...}
  }
```

---

### 14. Graduated Plugin Pattern
**What:** Move skills from personal → private → public as they mature
**Use case:** Validate utility before sharing, sanitize before open-sourcing
**Example:** Prototype locally → test in company → publish to community
```
□ Workflow pattern (not a repo):
  1. Prototype in ~/.claude/skills/
  2. Move to company-private-plugins/
  3. Sanitize and move to my-public-plugins/
```

---

### 15. Monorepo with Multiple Plugins
**What:** Single repo containing many independent plugins
**Use case:** Easier maintenance, shared documentation, related utilities
**Example:** All your personal utilities in one repo, cataloged in marketplace.json
```
□ Create repo: my-plugins-monorepo
  ├── .claude-plugin/marketplace.json (lists all plugins)
  └── plugins/
      ├── plugin-a/
      ├── plugin-b/
      └── plugin-c/
```

---

## Non-Plugin Patterns (Quick Reference)

### 16. Project Skills (No Plugin)
**What:** Skills committed directly to project repo
**Location:** `project/.claude/skills/`
**Use case:** Project-specific conventions, team-shared patterns
```
□ Add to existing project:
  project/.claude/skills/project-conventions/SKILL.md
```

---

### 17. Personal Skills (No Plugin)
**What:** Skills in your home directory
**Location:** `~/.claude/skills/`
**Use case:** Personal productivity, machine-specific configs, experiments
```
□ Create on local machine:
  ~/.claude/skills/my-personal-automation/SKILL.md
```

---

### 18. Manual MCP Configuration (No Plugin)
**What:** MCP servers configured manually, not via plugin
**Location:** `project/.claude/mcp.json` or `~/.claude/mcp.json`
**Use case:** Third-party MCPs, custom one-off servers
```
□ Configure manually:
  /mcp add my-server --command python server.py
```

---

## Recommended Learning Path

**Phase 1: Basics (Start Here)**
1. → Skills-Only Plugin (#1)
2. → MCP-Only Plugin (#2)
3. → Bundled Plugin (#3)

**Phase 2: Real-World**
4. → Private Company Marketplace (#5)
5. → Plugin with Templates/Assets (#8)
6. → Team-Specific Marketplace (#13)

**Phase 3: Advanced**
7. → Hybrid Marketplace (#7)
8. → Multi-Server MCP Plugin (#9)
9. → Plugin with Executable Scripts (#12)

**Phase 4: Community**
10. → Public Community Marketplace (#6)
11. → Graduated Plugin Pattern (#14)

---

## Quick Start Commands Reference

```bash
# Create marketplace
mkdir my-marketplace/.claude-plugin
echo '{"name":"my-marketplace","plugins":[...]}' > marketplace.json

# Add your marketplace
/plugin marketplace add https://github.com/you/my-marketplace.git

# Install plugin
/plugin install plugin-name@my-marketplace

# Update marketplace
/plugin marketplace update

# Browse available plugins
/plugin
```

---

## Repository Naming Conventions

**Marketplaces:**
- `{name}-plugins` or `{name}-marketplace`
- Examples: `company-plugins`, `my-claude-marketplace`

**Individual Plugins (in monorepo):**
- `{purpose}-plugin` or just `{purpose}`
- Examples: `azure-deployment`, `react-patterns-plugin`

**MCP-Only:**
- `{source}-mcp` or `{source}-data-mcp`
- Examples: `azure-data-mcp`, `company-apis-mcp`

---

## Next Steps

Pick a pattern from above, then ask Claude:
- "Help me implement pattern #3 (Bundled Plugin)"
- "Show me the detailed file structure for pattern #5"
- "Walk me through creating pattern #1 step by step"

Each pattern is a standalone learning exercise you can implement and carry with you throughout your career!