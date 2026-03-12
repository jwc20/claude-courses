# Notes

## What are skills?

---

This video explains what skills are in the context of using Claude, focusing on how they automate repetitive tasks (0:03). A skill is a markdown file that teaches Claude how to perform a specific action once, allowing it to apply that knowledge automatically whenever relevant (0:18).

Key Aspects of Skills:

Folders of Instructions: Agent skills consist of instructions, scripts, and resources that Claude can discover and utilize for higher accuracy and efficiency (0:32).
Matching System: Claude matches your requests against available skill descriptions to activate the relevant ones (0:49).
Storage Locations: Skills can be personal (saved in your home directory to follow you across projects) or project-specific (saved in the repository to share with your team) (1:05-1:27).
Context Management: Unlike general configuration files that load every time, skills load on demand based on your request, ensuring the context window isn't filled unnecessarily (2:00).
Skills are best suited for specialized knowledge, such as code review standards, commit message formats, and company brand guidelines (2:27).

---

### Problem

- When using LLM tools you are likely to repeat yourself, redescribe how you want the output/feedback, or remind the LLM on how you want the format.
  - A skill is a markdown file that fix this.

### What is a skill?

- A **Skill** teaches Claude to do something once and Claude automatically applies this knowledge whenever it is relevant.

![alt text](https://everpath-course-content.s3-accelerate.amazonaws.com/instructor%2Fa46l9irobhg0f5webscixp0bs%2Fpublic%2F1771527257%2FSkills1_05.1771527257795.png)

Here's what a skill's frontmatter looks like:

```
---
name: pr-review
description: Reviews pull requests for code quality. Use when reviewing PRs or checking code changes.
---
```

- **Skills** are folders of instructions, scripts, and resources that Claude can discover and utilize for higher accuracy and efficiency.

```
.claude
├── agents
├── skills
│   ├── commit-message
│   └── pr-review
│       └── SKILLS.md
```

- Claude matches your requests against all available **skill descriptions** to activate the relevant ones.

### Where Skills live

- Skills can be personal
  - (saved in your home directory to follow you across projects)
- or project-specific
  - (saved in the repository to share with your team)

### What makes Skills different?

- Context Management
- Unlike general configuration files (e.g. CLAUDE.md, project.toml, ...) that load every time, skills load on demand based on your request (it only loads name and description), ensuring the context window isn't filled unnecessarily.

  - (CLAUDE.md files load into every conversation. If you want Claude to always use TypeScript's strict mode, that goes in CLAUDE.md.)

- In Claude Code, you dont have to use a slash command to do some task, skills will do them automatically.

### When to use Skills

Skills work best for specialized knowledge that applies to specific tasks:

- Code review standards your team follows
- Commit message formats you prefer
- Brand guidelines for your organization
- Documentation templates for specific types of docs
- Debugging checklists for particular frameworks

The rule of thumb is simple: if you find yourself explaining the same thing to Claude repeatedly, that's a skill waiting to be written.

---

## Creating skills

Claude loads only skill **names and descriptions** at startup, then matches incoming requests against those descriptions.

Priority for name conflicts: Enterprise `managed-settings.json` → Personal `~/.claude/skills` → Project `project/.claude/skills` → Plugins `project/.claude-plugin/plugin.json`

Enterprise — managed settings, highest priority
Personal — your home directory (~/.claude/skills)
Project — the .claude/skills directory inside a repository
Plugins — installed plugins, lowest priority

```bash
mkdir -p ~/.claude/skills/pr-description
```

```md
---
name: pr-description
description: Writes pull request descriptions. Use when creating a PR, writing a PR, or when the user asks to summarize changes for a pull request.
---

When writing a PR description:

1. Run `git diff main...HEAD` to see all changes on this branch
2. Write a description following this format:

## What

One sentence explaining what this PR does.

## Why

Brief context on why this change is needed

## Changes

- Bullet points of specific changes made
- Group related changes together
- Mention any files deleted or renamed
```

---

## Configuration and multi-file skills

### SKILLS.md metadata

- **name** (required)
- **description** (required)
- **allowed-tools** (optional) - restricts which tools Claude can use when the skill is active — useful for read-only or security-sensitive workflows
- **model** (optional) - which model to use

### Writing Effective Descriptions

A good description answers two questions:

- What does the skill do?
- When should Claude use it?

If your skill isn't triggering when you expect it to, try adding more keywords that match how you actually phrase your requests.

### Restricting Tools with allowed-tools

```md
---
name: codebase-onboarding
description: Helps new developers understand the system works.
allowed-tools: Read, Grep, Glob, Bash
model: sonnet
---
```

In this example, the allowed-tools field is set to Read, Grep, Glob, Bash. When this skill is active, Claude can only use those tools without asking permission — no editing, no writing.

### Progressive Disclosure

We dont want `SKILL.md` to be huge, it will cause two problems: it takes up a lot of context window space, and it's not fun to maintain.

**Keep SKILL.md under 500 lines.**

Progressive disclosure solves this. Keep essential instructions in SKILL.md and put detailed reference material in separate files that Claude reads only when needed.

The open standard suggests organizing your skill directory with:

- **scripts**/ — Executable code
- **references**/ — Additional documentation
- **assets**/ — Images, templates, or other data files

![alt text](https://everpath-course-content.s3-accelerate.amazonaws.com/instructor%2Fa46l9irobhg0f5webscixp0bs%2Fpublic%2F1771527265%2FSkills3_13.1771527265100.png)

Example: add line like this

```md
**Only load when user requests more detail.** See [architectural-guide.md](reference/architectural-guide.md)
```

### Using Scripts Efficiently

This is particularly useful for:

- Environment validation
- Data transformations that need to be consistent
- Operations that are more reliable as tested code than generated code

---

## Skills vs. other Claude Code features

- CLAUDE.md — always-on project standards
- Skills — task-specific expertise that loads on demand
- Hooks — automated operations triggered by events
- Subagents — isolated execution contexts for delegated work
- MCP servers — external tools and integrations

### CLAUDE.md vs Skills

#### Use CLAUDE.md for

- Project-wide standards that always apply **(e.g. use TypeScript strict mode in your project)**
- Constraints like "never modify the database schema"
- Framework preferences and coding style

#### Use Skills for

- Task-specific expertise
- Knowledge that's only relevant sometimes **(eg. Your PR review checklist doesn't need to be in context when you're writing new code)**
- Detailed procedures that would clutter every conversation

### Skills vs Subagents

Skills add knowledge to your current conversation. When a skill activates, its instructions join the existing context.

Subagents run in a **separate context**. They receive a task, work on it independently, and return results. They're isolated from the main conversation.

#### Use Subagents when

You want to delegate a task to a separate execution context
You need different tool access than the main conversation
You want isolation between delegated work and your main context

#### Use Skills when

You want to enhance Claude's knowledge for the current task
The expertise applies throughout a conversation

### Skills vs Hooks

Hooks fire on events. A hook might run a **linter** every time Claude saves a file, or validate input before certain tool calls. They're event-driven.

Skills are request-driven. They activate based on what you're asking.

#### Use Hooks for

- Operations that should run on every file save
- Validation before specific tool calls
- Automated side effects of Claude's actions

#### Use Skills for

- Knowledge that informs how Claude handles requests
- Guidelines that affect Claude's reasoning

---

## Sharing skills

### Sharing methods

1. Committing Skills to Your Repository
2. Distributing Skills Through Plugins
3. Enterprise Deployment Through Managed Settings (`managed-settings.json`)

### Skills and Subagents

Subagents don't automatically load your skills.

There are important distinctions to understand:

- Built-in agents (like Explorer, Plan, and Verify) can't access skills at all
- Custom subagents you define can use skills, but only when you explicitly list them
- Skills are loaded when the subagent starts, not on demand like in the main conversation

To create a custom subagent with skills, add an agent markdown file in `.claude/agents` or create using `/agents` in the cli.

When you delegate to this subagent, it has both skills loaded and applies them to every review. First make sure the skills exist in your `.claude/skills directory`

```md
---
name: frontend-security-accessibility-reviewer
description: "Use this agent when you need to review frontend code for accessibility..."
tools: Bash, Glob, Grep, Read, WebFetch, WebSearch, Skill...
model: sonnet
color: blue
skills: accessibility-audit, performance-check
---
```

---

## Troubleshooting skills

Key takeaways

- Start with the skills validator tool — it catches structural problems before you spend time debugging other things
- If a skill doesn't trigger, the cause is almost always the description — add trigger phrases that match how you actually phrase requests
- If a skill doesn't load, check that SKILL.md is inside a named directory (not at the skills root) and the file name is exactly SKILL.md
- If the wrong skill gets used, your descriptions are too similar — make them more distinct
- For runtime errors, check dependencies, file permissions (chmod +x), and path separators (use forward slashes everywhere)

---

## things to know

### What are skills?

- Define what Claude Code skills are and how they work
- Explain where skills live (personal vs. project directories)
- Distinguish between skills, CLAUDE.md, and slash commands
- Identify scenarios where skills are the right customization tool

### Creating your first skill

- Create a skill from scratch with proper frontmatter structure
- Test and verify that a skill loads correctly in Claude Code
- Explain how Claude Code matches incoming requests to available skills
- Describe the skill priority hierarchy (Enterprise, Personal, Project, Plugins)

### Configuration and multi-file skills

- Configure advanced skill metadata fields including allowed-tools and model
- Write effective skill descriptions that reliably trigger on the right requests
- Use allowed-tools to restrict what Claude can do when a skill is active
- Organize complex skills using progressive disclosure and multi-file structures

### Skills vs. other Claude Code features

- Compare skills to CLAUDE.md, subagents, hooks, and MCP servers
- Choose the right Claude Code customization feature for a given use case
- Design a complementary setup that combines multiple features effectively

### Sharing skills

- Share skills with your team by committing them to a Git repository
- Distribute skills across projects through plugins and marketplaces
- Deploy skills organization-wide using enterprise managed settings
- Configure custom subagents to use specific skills

### Troubleshooting skills

- Use the skills validator to catch structural issues before debugging
- Diagnose and fix common skill triggering and loading problems
- Resolve skill priority conflicts between enterprise, personal, project, and plugin skills
- Debug runtime errors including missing dependencies, permissions, and path issues

