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

## Project Structure

```
.
├── designs/                      # Brand design documentation (DESIGN.md system)
│   ├── DESIGN.md               # Guide explaining the DESIGN.md concept
│   ├── apple/DESIGN.md         # Brand-specific design files
│   ├── claude/DESIGN.md
│   ├── nike/DESIGN.md
│   ├── notion/DESIGN.md
│   └── ...
├── docs/                        # Project documentation
│   └── superpowers/            # Agent superpowers & capabilities
│       ├── plans/              # Implementation plans
│       └── specs/             # Design specifications
├── visuals/                    # Excalidraw diagrams & workflow visuals
├── workflows/                  # n8n workflow definitions (JSON)
├── memories/                   # Session memories & knowledge base
├── content/                    # Content assets (carousels, templates)
├── interfaces/                # Interface specifications
├── claude structures/         # Claude Code structure guides
├── .claude/                    # Claude Code knowledge base
│   ├── memory/                 # Persistent memory system
│   │   ├── decision_log.md    # Key decisions & rationale
│   │   ├── issues_tracker.md  # Bug tracking
│   │   ├── progress.md        # Project progress
│   │   └── session_context.md # Current session context
│   ├── docs/                   # Claude Code documentation
│   │   ├── ARCHITECTURE.md     # System architecture
│   │   ├── GLOSSARY.md         # Terminology
│   │   └── QUICK_START.md     # Quick start guide
│   ├── rules/                  # Coding standards
│   │   ├── coding_standards.md # Multi-language standards
│   │   └── naming_conventions.md # Naming patterns
│   └── commands/              # Custom CLI commands
├── CLAUDE.md                   # Claude Code instructions
├── GEMINI.md                   # Gemini CLI instructions
└── n8n_MCP_GUIDE.md           # n8n MCP integration guide
```

## Memory System

Claude Code maintains a persistent memory system in `.claude/memory/`:
- **decision_log.md**: Records architectural decisions and their rationale
- **issues_tracker.md**: Tracks bugs and technical debt
- **progress.md**: Monitors project milestones and goals
- **session_context.md**: Preserves context across sessions

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
