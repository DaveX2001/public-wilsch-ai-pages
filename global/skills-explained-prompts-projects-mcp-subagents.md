---
publish: true
---
# Skills Explained: How Skills Compares to Prompts, Projects, MCP, and Subagents
[[claude-code-architecture]]

**Source:** https://claude.com/blog/skills-explained
**Author:** Anthropic
**Date:** November 13, 2025

---

Skills are an increasingly powerful tool for creating custom AI workflows and agents, but where do they fit in the Claude stack? This guide breaks down each building block, explains when to use what, and shows you how to combine them for powerful agentic workflows.

## Understanding Your Agentic Building Blocks

### What are Skills?

Skills are folders containing instructions, scripts, and resources that Claude discovers and loads dynamically when relevant to a task. Think of them as specialized training manuals that give Claude expertise in specific domains—from working with Excel spreadsheets to following your organization's brand guidelines.

**How Skills work:** When Claude encounters a task, it scans available Skills to find relevant matches. Skills use progressive disclosure: metadata loads first (~100 tokens), providing just enough information for Claude to know when a Skill is relevant. Full instructions load when needed (<5k tokens), and bundled files or scripts load only as required.

**When to use Skills:** Choose Skills when you need Claude to perform specialized tasks consistently and efficiently. They're ideal for:
- **Organizational workflows**: Brand guidelines, compliance procedures, document templates
- **Domain expertise:** Excel formulas, PDF manipulation, data analysis
- **Personal preferences:** Note-taking systems, coding patterns, research methods

**Example:** Create a brand guidelines Skill that includes your company's color palette, typography rules, and layout specifications. When Claude creates presentations or documents, it automatically applies these standards without you needing to explain them each time.

### What are Prompts?

Prompts are the instructions you provide to Claude in natural language during a conversation. They're ephemeral, conversational, and reactive—you provide context and direction in the moment.

**When to use prompts:** Use prompts for:
- One-off requests: "Summarize this article"
- Conversational refinement: "Make that tone more professional"
- Immediate context: "Analyze this data and identify trends"
- Ad-hoc instructions: "Format this as a bulleted list"

**Pro-tip:** Prompts are your primary way of interacting with Claude, but they don't persist across conversations. For repeated workflows or specialized knowledge, consider capturing prompts as Skills or project instructions.

**When to use a Skill instead:** If you find yourself typing the same prompt repeatedly across multiple conversations, it's time to create a Skill. Transform recurring instructions like "review this code for security vulnerabilities using OWASP standards" into Skills. This saves you from re-explaining procedures each time and ensures consistent execution.

### What are Projects?

Available on all paid Claude plans, Projects are self-contained workspaces with their own chat histories and knowledge bases. Each project includes a 200K context window where you can upload documents, provide context, and set custom instructions that apply to all conversations within that project.

**How Projects work:** Everything you upload to a project's knowledge base becomes available across all chats within that project. Claude automatically uses this context to provide more informed, relevant responses. When your project knowledge approaches context limits, Claude seamlessly enables Retrieval Augmented Generation (RAG) mode to expand capacity by up to 10x.

**When to use Projects:** Choose Projects when you need:
- **Persistent context:** Background knowledge that should inform every conversation
- **Workspace organization:** Separate contexts for different initiatives
- **Team collaboration:** Shared knowledge and conversation history (on Team and Enterprise plans)
- **Custom instructions:** Project-specific tone, perspective, or approach

**When to use a Skill instead:** Projects give Claude persistent context for a specific body of work. Skills teach Claude how to do something. A Project might contain all the background on your product launch, while a skill could teach Claude your team's writing standards. If you find yourself copying the same instructions across multiple Projects, that's a signal to create a skill instead.

### What are Subagents?

Subagents are specialized AI assistants with their own context windows, custom system prompts, and specific tool permissions. Available in Claude Code and the Claude Agent SDK, subagents handle discrete tasks independently and return results to the main agent.

**How subagents work:** Each subagent operates with its own configuration—you define what it does, how it approaches problems, and which tools it can access. Claude automatically delegates tasks to appropriate subagents based on their descriptions, or you can explicitly request a specific subagent.

**When to use subagents:** Use subagents for:
- **Task specialization:** Code review, test generation, security audits
- **Context management:** Keep the main conversation focused while offloading specialized work
- **Parallel processing:** Multiple subagents can work on different aspects simultaneously
- **Tool restriction:** Limit specific subagents to safe operations (e.g., read-only access)

**When to use a Skill instead:** If multiple agents or conversations need the same expertise, create a Skill rather than building that knowledge into individual subagents. Skills are portable and reusable, while subagents are purpose-built for specific workflows.

### What is MCP?

The Model Context Protocol (MCP) is an open standard for connecting AI assistants to external systems where data lives—content repositories, business tools, databases, and development environments.

**How MCP works:** MCP provides a standardized way to connect Claude to your tools and data sources. Instead of building custom integrations for each data source, you build against a single protocol. MCP servers expose data and capabilities; MCP clients (like Claude) connect to these servers.

**When to use MCP:** Choose MCP when you need Claude to:
- Access external data: Google Drive, Slack, GitHub, databases
- Use business tools: CRM systems, project management platforms
- Connect to development environments: Local files, IDEs, version control
- Integrate with custom systems: Your proprietary tools and data sources

**When to use a Skill instead:** MCP connects Claude to data; Skills teach Claude what to do with that data. If you're explaining _how_ to use a tool or follow procedures, that's a Skill. If you need Claude to _access_ the database or files in the first place, that's MCP. Use both together: MCP for connectivity, Skills for procedural knowledge.

## How They Work Together

The real power emerges when you combine these building blocks. Each serves a distinct purpose, and together they create sophisticated agentic workflows.

### Comparison: Choosing the Right Tool

| Feature | Skills | Prompts | Projects | Subagents | MCP |
|---------|--------|---------|----------|-----------|-----|
| **What it provides** | Procedural knowledge | Moment-to-moment instructions | Background knowledge | Task delegation | Tool connectivity |
| **Persistence** | Across conversations | Single conversation | Within project | Across sessions | Continuous connection |
| **Contains** | Instructions + code + assets | Natural language | Documents + context | Full agent logic | Tool definitions |
| **When it loads** | Dynamically, as needed | Each turn | Always in project | When invoked | Always available |
| **Can include code** | Yes | No | No | Yes | Yes |
| **Best for** | Specialized expertise | Quick requests | Centralized context | Specialized tasks | Data access |

### Example Agentic Workflow: Research Agent

**Step 1: Set up your Project**

Create a "Competitive Intelligence" project and upload:
- Industry reports and market analyses
- Competitor product documentation
- Customer feedback from your CRM
- Previous research summaries

Add project instructions:
> Analyze competitors through the lens of our product strategy. Focus on differentiation opportunities and emerging market trends. Present findings with specific evidence and actionable recommendations.

**Step 2: Connect data sources via MCP**

Enable MCP servers for:
- Google Drive (to access shared research documents)
- GitHub (to review competitor open-source repositories)
- Web search (for real-time market information)

**Step 3: Create specialized Skills**

Create a "competitive-analysis" skill with your GDrive navigation strategy, naming conventions, search best practices, and research workflow.

**Step 4: Configure subagents (Claude Code/SDK only)**

Create specialized subagents:
- `market-researcher` — Research market trends, industry reports, competitive landscape data
- `technical-analyst` — Analyze technical architecture, implementation approaches, engineering decisions

**Step 5: Activate your research agent**

When you ask: "Analyze how our top three competitors are positioning their new AI features"

1. **Project context loads**: Claude accesses uploaded research documents
2. **MCP connections activate**: Claude searches Google Drive and GitHub
3. **Skills engage**: The competitive-analysis Skill provides the analytical framework
4. **Subagents execute**: market-researcher gathers industry data, technical-analyst reviews implementations
5. **Prompts refine**: You provide guidance: "Focus especially on enterprise customers in healthcare"

## Common Questions

### Skills vs. subagents: when to use what

**Use Skills when:** You want capabilities that any Claude instance can load and use. Skills are like training materials—they make Claude better at specific tasks across all conversations.

**Use subagents when:** You need complete, self-contained agents for specific purposes that handle workflows independently.

**Use them together when:** You want subagents with specialized expertise. A code-review subagent can use Skills for language-specific best practices.

### Skills vs. prompts: when to use what

**Use prompts when:** You're giving one-time instructions, providing immediate context, or having a conversational back-and-forth. Prompts are reactive and ephemeral.

**Use Skills when:** You have procedures or expertise that you'll need repeatedly. Skills are proactive—Claude knows when to apply them—and persistent across conversations.

### Skills vs. Projects: when to use what

**Use Projects when:** You need background knowledge and context that should inform all conversations about a specific initiative.

**Use Skills when:** You need procedural knowledge and executable code that activates only when relevant.

**Key difference:** Projects say "here's what you need to know." Skills say "here's how to do things." Projects provide a knowledge base. Skills provide capabilities that work everywhere.

### Can subagents use Skills?

Yes. In Claude Code and the Agent SDK, subagents can access and use Skills just like the main agent. Your python-developer subagent can use the pandas-analysis Skill, while your documentation-writer subagent uses the technical-writing skill.

## Getting Started

**Claude.ai users:**
- Enable Skills in Settings → Features
- Create your first project at claude.ai/projects

**API developers:**
- Explore the Skills endpoint in documentation
- Check out the skills cookbook

**Claude Code users:**
- Install Skills via plugin marketplaces
- Check out the skills cookbook

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

