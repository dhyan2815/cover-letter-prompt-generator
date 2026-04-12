---
name: Dependency Graph Feature Design
description: Design specification for an interactive dependency‑graph skill supporting JavaScript/TypeScript, Python, and HTML files. Includes scope, components, data flow, and implementation roadmap.
type: project
---

# Dependency Graph Feature Design

**Date:** 2026-04-10

## Goal
Create an interactive visual tool that shows file‑level imports and symbol references (functions, classes) for JavaScript/TypeScript, Python, and HTML files. The UI will render a shaded tree with zoom/pan and optionally export a Graphviz DOT file.

## Scope
- **Languages:** JavaScript, TypeScript, Python, HTML
- **Dependency Level:**
  - Direct imports (`import`, `require`, `from … import`)
  - Exported symbols (functions, classes, variables)
  - References to those symbols within the same file
- **No cross‑repo call‑graph** at this stage.

## Core Components
| Component | Responsibility |
|-----------|----------------|
| **Parser Layer** | Language‑specific AST parsing (Esprima/Acorn for JS/TS, Python `ast` module, HTML parser) to extract imports and symbols. |
| **Graph Builder** | Builds a directed graph where nodes represent files (or symbols) and edges represent "imports/uses" relationships. |
| **Renderer** | Generates an interactive D3 shaded‑tree visualization; optional Graphviz DOT export. |
| **CLI / API Wrapper** | `claude dependency-graph <file>` command that launches a local server, builds the graph, and opens the UI URL. |

## Data Flow
1. User runs the CLI command with a target file path.
2. CLI invokes the Parser Layer → extracts imports & symbol references.
3. Parsed data is fed to Graph Builder → creates in‑memory graph.
4. Graph is passed to Renderer → HTML page with D3 tree (or Graphviz SVG) is generated.
5. CLI starts a lightweight HTTP server (`http://localhost:3000/graph?...`) and opens the browser.

## Visualization Details
- **Shaded Tree Layout**: Hierarchical view, colour‑coded per language, hover tooltips showing import paths and symbol signatures.
- **Graphviz Export**: Option to download a `.dot` file for external layout tools.
- **Interactivity**: Click nodes to expand/collapse, view symbol details, copy import strings.

## Implementation Roadmap
1. **Prototype Parsers** – Build minimal parsers for each language and verify extraction on a sample repo.
2. **Graph Builder Prototype** – Convert parser output into a simple adjacency list.
3. **Renderer Scaffold** – Set up a D3 tree component and feed it static data.
4. **CLI Integration** – Wire the command, start a local Express/Koa server, and auto‑open the URL.
5. **Polish & Extras** – Add zoom/pan, tooltips, Graphviz export, error handling, and basic tests.

## Success Criteria
- Running `claude dependency-graph src/app.ts` opens a browser window showing a correct shaded‑tree of that file’s imports and symbol references.
- The UI correctly distinguishes JavaScript/TypeScript, Python, and HTML files.
- Users can export a functional Graphviz `.dot` representation.
- No cross‑repo analysis is performed by default.

---

*Design documented per superpowers brainstorming workflow. Ready for review and subsequent spec‑to‑plan transition.*