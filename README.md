# Agent Sandbox

Welcome to my experimental sandbox for CLI-based tools, AI agents, and workflow automations. This repository is dedicated to exploring the capabilities and scaling possibilities of various command-line interfaces and their associated "skills" or "mcp" integrations.

## Core Focus

This repository explores the intersection of AI and command-line interfaces. Key areas of experimentation include:

- **Claude Code:** Harnessing agentic AI for automated code generation, refactoring, and complex problem-solving directly within the terminal.
- **Gemini CLI:** Automating complex workflows and data processing tasks by scripting interactions with Google's Gemini models.
- **n8n Workflows:** Building and documenting robust, event-driven automations to connect APIs and services with minimal code.
- **Excalidraw Diagrams:** Creating clear, maintainable diagrams of system architecture, workflows, and infrastructure for planning and documentation.
- **Open Code:** Exploring the potential of open-source, CLI-driven development tools.
- **DESIGN.md System:** Brand design documentation for consistent UI generation across projects.

## Goals

The primary goal of this repository is to serve as a personal knowledge base and a testbed for cutting-edge CLI and AI-driven development techniques. Key objectives include:

- **Mastery:** Deepen expertise in using agentic CLI tools.
- **Automation:** Document scalable patterns for automating repetitive software development tasks.
- **Integration:** Explore seamless integrations between different AI services and development tools.
- **Visualization:** Develop clear and effective methods for documenting complex systems.
- **Design Consistency:** Create reusable brand design documentation that AI agents can reference.
- **Memory:** Enable persistent context across sessions via Claude Code memory system.
- **Standards:** Enforce consistent code quality through documented conventions.

## Skills

This repository includes specialized AI agent skills for various tasks. Each skill is designed to be invoked through the Claude Code Skill system.

| Skill | Description | Use Case Example |
|-------|-------------|-----------------|
| **mcp-builder** | Guide for creating high-quality MCP (Model Context Protocol) servers that enable LLMs to interact with external services. | Building integrations with external APIs like GitHub, Slack, or Notion. A developer creating a custom MCP server to let an AI agent manage GitHub issues and pull requests. |
| **md-to-pdf-converter** | Converts Markdown (.md) files into professional, high-fidelity PDF reports with tables, formatting, and Unicode support. | Generating client deliverables, technical documentation, or policy documents from Markdown. Creating a polished PDF report from design specifications written in Markdown. |
| **excalidraw-diagram-generator** | Generate Excalidraw diagrams from natural language descriptions. Supports flowcharts, mind maps, architecture diagrams, and more. | Visualizing system architecture, workflows, or conceptual diagrams. Creating a cloud architecture diagram for AWS/GCP deployment planning. |
| **web-design-reviewer** | Visual inspection of websites to identify and fix design issues including responsive design, accessibility, and layout problems. | Auditing a landing page for mobile responsiveness, fixing alignment issues, or improving accessibility compliance. Reviewing a React app's UI and fixing CSS conflicts. |
| **linkedin-profile-optimizer** | Audits and rewrites LinkedIn profiles using a strategist framework to maximize visibility, credibility, and conversions. | Job search optimization, personal branding for recruiters, or building professional thought leadership. Optimizing a senior engineer's profile to attract FAANG opportunities. |
| **design-doc-mermaid** | Create Mermaid diagrams (activity, deployment, sequence, architecture) from text descriptions or source code. | Documenting API workflows, creating architecture diagrams, or generating technical documentation. Creating deployment diagrams for Kubernetes infrastructure. |
| **writing-linkedin-posts** | Create engaging, authentic LinkedIn posts like a Top Voice. Focuses on thought leadership, authentic voice, and meaningful engagement. | Building personal brand, sharing industry insights, or establishing expertise. Writing a viral post about leadership lessons learned from a product launch. |
| **linkedin-comment-generator** | Generate authentic, non-generic LinkedIn comments that spark real micro-conversations. Multiple tone variants for different post types. | Engaging with industry leaders, building network connections, or starting meaningful discussions. Commenting on a CTO's post about engineering culture with a thoughtful response. |

### Invoking Skills

To use a skill, invoke it through Claude Code's Skill tool:

```
/skill-name  (using slash command)
```

Or use the Skill tool directly with the skill name.

This enables long-term memory of project decisions and user preferences.

## Documentation System

The `.claude/docs/` directory contains Claude-specific documentation:
- **ARCHITECTURE.md**: System design and component relationships
- **GLOSSARY.md**: Terminology definitions
- **QUICK_START.md**: Getting started guide for new contexts

## Coding Standards

Project-wide standards in `.claude/rules/`:
- **coding_standards.md**: Universal principles + language-specific guides (Python, JS/TS, Go, Java, C)
- **naming_conventions.md**: File, class, function, variable, database, and API naming patterns

## Design System

Inspired by [VoltAgent/awesome-design-md](https://github.com/VoltAgent/awesome-design-md), this repository uses the DESIGN.md concept. Each brand/project gets its own `DESIGN.md` file containing:

- Visual theme and atmosphere
- Color palette with semantic roles
- Typography rules and hierarchy
- Component styles and specifications
- Layout principles
- Depth and elevation guidelines
- Do's and don'ts
- Responsive behavior
- Agent prompt references

These files serve as a single source of truth for AI agents to generate brand-consistent UI.

## Experimental Nature

Everything in this repository is highly experimental. I use this space to push the boundaries of what CLI tools can do in a modern software engineering environment.

---

*Created and maintained by [dhyan2815](https://github.com/dhyan2815).*
