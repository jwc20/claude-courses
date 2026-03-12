# What are skills?

---

https://www.youtube.com/watch?v=bjdBVZa66oU

This video explains what skills are in the context of using Claude, focusing on how they automate repetitive tasks (0:03). A skill is a markdown file that teaches Claude how to perform a specific action once, allowing it to apply that knowledge automatically whenever relevant (0:18).

Key Aspects of Skills:

Folders of Instructions: Agent skills consist of instructions, scripts, and resources that Claude can discover and utilize for higher accuracy and efficiency (0:32).
Matching System: Claude matches your requests against available skill descriptions to activate the relevant ones (0:49).
Storage Locations: Skills can be personal (saved in your home directory to follow you across projects) or project-specific (saved in the repository to share with your team) (1:05-1:27).
Context Management: Unlike general configuration files that load every time, skills load on demand based on your request, ensuring the context window isn't filled unnecessarily (2:00).
Skills are best suited for specialized knowledge, such as code review standards, commit message formats, and company brand guidelines (2:27).


---

## Problem:

- When using LLM tools you are likely to repeat yourself, redescribe how you want the output/feedback, or remind the LLM on how you want the format.
  - A skill is a markdown file that fix this.


## What is a skill?

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

## Where Skills live

- Skills can be personal 
  - (saved in your home directory to follow you across projects) 
- or project-specific 
  - (saved in the repository to share with your team) 

## What makes Skills different?

- Context Management
  
- Unlike general configuration files (e.g. CLAUDE.md, project.toml, ...) that load every time, skills load on demand based on your request (it only loads name and description), ensuring the context window isn't filled unnecessarily.
  - (CLAUDE.md files load into every conversation. If you want Claude to always use TypeScript's strict mode, that goes in CLAUDE.md.)

- In Claude Code, you dont have to use a slash command to do some task, skills will do them automatically.


## When to use Skills

Skills work best for specialized knowledge that applies to specific tasks:

- Code review standards your team follows
- Commit message formats you prefer
- Brand guidelines for your organization
- Documentation templates for specific types of docs
- Debugging checklists for particular frameworks

The rule of thumb is simple: if you find yourself explaining the same thing to Claude repeatedly, that's a skill waiting to be written.