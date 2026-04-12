# Dependency Graph Implementation Plan

**Date:** 2026-04-10  
**Status:** Implementation Ready

---

## 1. Project Structure

```
dependency-graph/
├── src/
│   ├── parsers/
│   │   ├── base.ts           # Abstract parser interface
│   │   ├── javascript.ts     # JS/TS parser (Esprima/Acorn)
│   │   ├── python.ts         # Python parser (ast module)
│   │   └── html.ts           # HTML parser
│   ├── graph/
│   │   ├── builder.ts        # Graph building logic
│   │   └── types.ts          # Graph data structures
│   ├── renderer/
│   │   ├── tree.ts           # D3 tree visualization
│   │   └── graphviz.ts       # DOT file generator
│   ├── cli/
│   │   └── index.ts          # CLI command handler
│   └── server/
│       └── index.ts          # Local HTTP server
├── static/
│   └── index.html            # D3 visualization page
├── package.json
└── tsconfig.json
```

---

## 2. Implementation Tasks

### Phase 1: Core Parsers

#### 2.1 Base Parser Interface
```typescript
// src/parsers/base.ts
interface ParseResult {
  file: string;
  language: 'javascript' | 'typescript' | 'python' | 'html';
  imports: Import[];
  exports: Symbol[];
}

interface Import {
  path: string;
  symbols: string[];
  line: number;
}

interface Symbol {
  name: string;
  type: 'function' | 'class' | 'variable' | 'interface';
  line: number;
}

interface Parser {
  canParse(file: string): boolean;
  parse(content: string, filePath: string): ParseResult;
}
```

#### 2.2 JavaScript/TypeScript Parser
- Use **Acorn** for JS/TS parsing (smaller, faster than Esprima)
- Extract `import` statements and `require()` calls
- Track exported symbols via `export` keywords
- Handle TypeScript interfaces and type exports

#### 2.3 Python Parser
- Use built-in `ast` module
- Extract `import` and `from ... import` statements
- Track classes, functions, and module-level variables

#### 2.4 HTML Parser
- Extract `<script src="...">`, `<link href="...">` tags
- Track inline `<script>` tag content

---

### Phase 2: Graph Builder

#### 2.5 Graph Data Structures
```typescript
// src/graph/types.ts
interface GraphNode {
  id: string;
  file: string;
  language: string;
  symbols: Symbol[];
  imports: string[];  // Node IDs
}

interface DependencyGraph {
  nodes: Map<string, GraphNode>;
  rootId: string;
}
```

#### 2.6 Graph Builder Logic
- Build adjacency list from parser results
- Resolve relative imports to absolute paths
- Detect circular dependencies
- Collect transitive imports recursively

---

### Phase 3: Renderer

#### 2.7 D3 Tree Visualization
- Use D3.js v7 with `d3-hierarchy` and `d3-zoom`
- Color coding by language:
  - JavaScript: `#f7df1e` (yellow)
  - TypeScript: `#3178c6` (blue)
  - Python: `#3776ab` (green)
  - HTML: `#e34f26` (orange)
- Node styling:
  - Rounded rectangles with language color
  - Hover tooltip with file path and symbols
  - Click to expand/collapse children

#### 2.8 Interactive Features
- Zoom/pan controls
- Collapse/expand nodes
- Copy import path on click
- Search/filter nodes

#### 2.9 Graphviz Export
- Generate valid DOT language output
- Include node colors matching visualization
- Export as downloadable `.dot` file

---

### Phase 4: CLI Integration

#### 2.10 CLI Command
```bash
claude dependency-graph <file> [--depth=N] [--format=html|json|dot]
```

#### 2.11 Local Server
- Use Express.js or built-in Node `http` module
- Serve static HTML + D3 visualization
- API endpoints:
  - `GET /graph?file=<path>` - Build and return graph JSON
  - `GET /export/dot?file=<path>` - Return DOT file

---

### Phase 5: Polish

#### 2.12 Error Handling
- File not found errors
- Parse errors with line numbers
- Circular dependency warnings

#### 2.13 Testing
- Unit tests for each parser
- Integration tests for graph building
- E2E test for CLI command

---

## 3. Dependencies

```json
{
  "dependencies": {
    "acorn": "^8.11.0",
    "acorn-typescript": "^1.4.0",
    "d3": "^7.8.0",
    "express": "^4.18.0",
    "commander": "^11.0.0"
  },
  "devDependencies": {
    "@types/d3": "^7.4.0",
    "@types/express": "^4.17.0",
    "typescript": "^5.3.0",
    "vitest": "^1.0.0"
  }
}
```

---

## 3.1 Base Parser Implementation

```typescript
// src/parsers/base.ts
export interface Import {
  path: string;
  symbols: string[];
  line: number;
}

export interface Symbol {
  name: string;
  type: 'function' | 'class' | 'variable' | 'interface' | 'module';
  line: number;
}

export interface ParseResult {
  file: string;
  language: 'javascript' | 'typescript' | 'python' | 'html';
  imports: Import[];
  exports: Symbol[];
}

export abstract class BaseParser {
  abstract canParse(filePath: string): boolean;
  abstract parse(content: string, filePath: string): ParseResult;
  
  protected getLanguage(filePath: string): string {
    const ext = filePath.split('.').pop()?.toLowerCase();
    const langMap: Record<string, string> = {
      'js': 'javascript',
      'jsx': 'javascript',
      'ts': 'typescript',
      'tsx': 'typescript',
      'py': 'python',
      'html': 'html',
    };
    return langMap[ext || ''] || 'unknown';
  }
}
```

### 3.2 JavaScript/TypeScript Parser Implementation

```typescript
// src/parsers/javascript.ts
import { parse } from 'acorn';
import { BaseParser, ParseResult, Import, Symbol } from './base';

export class JavaScriptParser extends BaseParser {
  canParse(filePath: string): boolean {
    return /\.(js|jsx|ts|tsx)$/i.test(filePath);
  }

  parse(content: string, filePath: string): ParseResult {
    const imports: Import[] = [];
    const exports: Symbol[] = [];
    const ast = parse(content, { 
      ecmaVersion: 'latest', 
      sourceType: 'module',
      locations: true 
    });

    for (const node of ast.body) {
      // Handle: import x from 'module'
      if (node.type === 'ImportDeclaration') {
        imports.push({
          path: node.source.value as string,
          symbols: node.specifiers.map(s => 
            s.type === 'ImportDefaultSpecifier' ? 'default' : s.local.name
          ),
          line: node.loc?.start.line || 0,
        });
      }
      // Handle: const x = require('module')
      if (node.type === 'VariableDeclaration') {
        for (const decl of node.declarations) {
          if (decl.init?.type === 'CallExpression' && 
              decl.init.callee.name === 'require') {
            imports.push({
              path: decl.init.arguments[0].value as string,
              symbols: ['*'],
              line: node.loc?.start.line || 0,
            });
          }
        }
      }
      // Handle: export function/class/const
      if (node.type === 'ExportNamedDeclaration') {
        if (node.declaration) {
          exports.push({
            name: (node.declaration as any).id?.name || 'anonymous',
            type: this.getExportType(node.declaration.type),
            line: node.loc?.start.line || 0,
          });
        }
      }
    }

    return {
      file: filePath,
      language: this.getLanguage(filePath) as 'javascript' | 'typescript',
      imports,
      exports,
    };
  }

  private getExportType(nodeType: string): Symbol['type'] {
    const map: Record<string, Symbol['type']> = {
      'FunctionDeclaration': 'function',
      'ClassDeclaration': 'class',
      'VariableDeclaration': 'variable',
      'TSInterfaceDeclaration': 'interface',
    };
    return map[nodeType] || 'variable';
  }
}
```

### 3.3 Python Parser Implementation

```typescript
// src/parsers/python.ts
import * as ast from 'python-ast'; // Using esprima-python or custom AST visitor
import { BaseParser, ParseResult, Import, Symbol } from './base';

export class PythonParser extends BaseParser {
  canParse(filePath: string): boolean {
    return /\.py$/i.test(filePath);
  }

  parse(content: string, filePath: string): ParseResult {
    const imports: Import[] = [];
    const exports: Symbol[] = [];
    
    // Use Python AST via child_process spawn of python -c
    // Or use a pure JS Python parser like esprima-python
    const tree = this.parsePython(content);
    
    for (const node of tree) {
      if (node.type === 'Import') {
        imports.push({
          path: node.names[0].name,
          symbols: node.names.map(n => n.name),
          line: node.lineno,
        });
      }
      if (node.type === 'ImportFrom') {
        imports.push({
          path: node.module,
          symbols: node.names.map(n => n.name),
          line: node.lineno,
        });
      }
      if (node.type === 'FunctionDef') {
        exports.push({ name: node.name, type: 'function', line: node.lineno });
      }
      if (node.type === 'ClassDef') {
        exports.push({ name: node.name, type: 'class', line: node.lineno });
      }
    }

    return { file: filePath, language: 'python', imports, exports };
  }

  private parsePython(content: string): any[] {
    // Placeholder - implement via subprocess or WASM parser
    return [];
  }
}
```

### 3.4 HTML Parser Implementation

```typescript
// src/parsers/html.ts
import { BaseParser, ParseResult, Import, Symbol } from './base';

export class HtmlParser extends BaseParser {
  canParse(filePath: string): boolean {
    return /\.html?$/i.test(filePath);
  }

  parse(content: string, filePath: string): ParseResult {
    const imports: Import[] = [];
    
    // Extract script src
    const scriptRegex = /<script[^>]+src=["']([^"']+)["'][^>]*>/gi;
    let match;
    while ((match = scriptRegex.exec(content)) !== null) {
      imports.push({
        path: match[1],
        symbols: [],
        line: this.getLineNumber(content, match.index),
      });
    }

    // Extract link href (CSS)
    const linkRegex = /<link[^>]+href=["']([^"']+)["'][^>]*>/gi;
    while ((match = linkRegex.exec(content)) !== null) {
      imports.push({
        path: match[1],
        symbols: [],
        line: this.getLineNumber(content, match.index),
      });
    }

    return { file: filePath, language: 'html', imports, exports: [] };
  }

  private getLineNumber(content: string, index: number): number {
    return content.substring(0, index).split('\n').length;
  }
}
```

### 3.5 Parser Factory

```typescript
// src/parsers/index.ts
import { BaseParser, ParseResult } from './base';
import { JavaScriptParser } from './javascript';
import { PythonParser } from './python';
import { HtmlParser } from './html';

export class ParserFactory {
  private parsers: BaseParser[] = [
    new JavaScriptParser(),
    new PythonParser(),
    new HtmlParser(),
  ];

  parse(content: string, filePath: string): ParseResult | null {
    for (const parser of this.parsers) {
      if (parser.canParse(filePath)) {
        return parser.parse(content, filePath);
      }
    }
    return null;
  }
}
```

---

## 3.6 Graph Builder Implementation

```typescript
// src/graph/types.ts
export interface GraphNode {
  id: string;
  file: string;
  language: string;
  symbols: { name: string; type: string; line: number }[];
  imports: string[];
  collapsed?: boolean;
}

export interface DependencyGraph {
  nodes: Map<string, GraphNode>;
  rootId: string;
  edges: { source: string; target: string }[];
}
```

```typescript
// src/graph/builder.ts
import { readFileSync } from 'fs';
import { resolve, dirname, extname } from 'path';
import { ParserFactory } from '../parsers';
import { GraphNode, DependencyGraph } from './types';

export class GraphBuilder {
  private parserFactory: ParserFactory;
  private visited: Set<string>;
  private maxDepth: number;

  constructor(maxDepth = 3) {
    this.parserFactory = new ParserFactory();
    this.visited = new Set();
    this.maxDepth = maxDepth;
  }

  buildGraph(entryFile: string): DependencyGraph {
    const nodes = new Map<string, GraphNode>();
    this.visited.clear();
    
    this.buildNode(entryFile, nodes, 0);
    
    return {
      nodes,
      rootId: this.normalizePath(entryFile),
      edges: this.extractEdges(nodes),
    };
  }

  private buildNode(filePath: string, nodes: Map<string, GraphNode>, depth: number): void {
    const normalizedPath = this.normalizePath(filePath);
    if (this.visited.has(normalizedPath) || depth > this.maxDepth) return;
    this.visited.add(normalizedPath);

    try {
      const content = readFileSync(filePath, 'utf-8');
      const result = this.parserFactory.parse(content, filePath);
      if (!result) return;

      const node: GraphNode = {
        id: normalizedPath,
        file: filePath,
        language: result.language,
        symbols: result.exports,
        imports: [],
      };

      // Process imports recursively
      for (const imp of result.imports) {
        const resolvedPath = this.resolveImport(imp.path, filePath);
        if (resolvedPath) {
          node.imports.push(this.normalizePath(resolvedPath));
          this.buildNode(resolvedPath, nodes, depth + 1);
        }
      }

      nodes.set(normalizedPath, node);
    } catch (err) {
      console.warn(`Could not parse ${filePath}:`, err);
    }
  }

  private resolveImport(importPath: string, fromFile: string): string | null {
    if (importPath.startsWith('.')) {
      const dir = dirname(fromFile);
      return resolve(dir, importPath);
    }
    // Skip node_modules for now
    return null;
  }

  private normalizePath(filePath: string): string {
    return filePath.replace(/\\/g, '/');
  }

  private extractEdges(nodes: Map<string, GraphNode>): { source: string; target: string }[] {
    const edges: { source: string; target: string }[] = [];
    for (const node of nodes.values()) {
      for (const target of node.imports) {
        edges.push({ source: node.id, target });
      }
    }
    return edges;
  }
}
```

---

## 3.7 D3 Tree Visualization (static/index.html)

```html
<!DOCTYPE html>
<html>
<head>
  <title>Dependency Graph</title>
  <script src="https://d3js.org/d3.v7.min.js"></script>
  <style>
    .node rect { cursor: pointer; }
    .node text { font: 12px sans-serif; }
    .link { fill: none; stroke: #ccc; stroke-width: 1.5px; }
    .tooltip { position: absolute; background: #222; color: #fff; padding: 8px; border-radius: 4px; font-size: 12px; }
    .controls { position: fixed; top: 10px; right: 10px; }
  </style>
</head>
<body>
  <div class="controls">
    <button id="zoom-in">+</button>
    <button id="zoom-out">-</button>
    <button id="export-dot">Export .dot</button>
  </div>
  <svg></svg>
  <script>
    const LANGUAGE_COLORS = {
      javascript: '#f7df1e',
      typescript: '#3178c6',
      python: '#3776ab',
      html: '#e34f26'
    };

    async function loadGraph() {
      const params = new URLSearchParams(window.location.search);
      const file = params.get('file');
      const response = await fetch(`/graph?file=${encodeURIComponent(file)}`);
      const data = await response.json();
      renderGraph(data);
    }

    function renderGraph(data) {
      const width = window.innerWidth;
      const height = window.innerHeight;
      const svg = d3.select('svg').attr('width', width).attr('height', height);
      const g = svg.append('g');

      // Build hierarchy from edges
      const nodes = Array.from(data.nodes.values());
      const edges = data.edges;
      
      // Create adjacency list for tree layout
      const root = nodes.find(n => n.id === data.rootId);
      
      // Use d3.tree layout
      const hierarchy = d3.stratify()
        .id(d => d.id)
        .parentId(d => {
          const parent = edges.find(e => e.target === d.id);
          return parent ? parent.source : null;
        })(nodes);

      const treeLayout = d3.tree().size([height - 100, width - 200]);
      treeLayout(hierarchy);

      // Draw links
      g.selectAll('.link')
        .data(hierarchy.links())
        .enter().append('path')
        .attr('class', 'link')
        .attr('d', d3.linkHorizontal()
          .x(d => d.y)
          .y(d => d.x));

      // Draw nodes
      const node = g.selectAll('.node')
        .data(hierarchy.descendants())
        .enter().append('g')
        .attr('class', 'node')
        .attr('transform', d => `translate(${d.y},${d.x})`);

      node.append('rect')
        .attr('x', 0)
        .attr('y', -15)
        .attr('width', 120)
        .attr('height', 30)
        .attr('rx', 5)
        .attr('fill', d => LANGUAGE_COLORS[d.data.language] || '#888');

      node.append('text')
        .attr('dx', 60)
        .attr('dy', 4)
        .attr('text-anchor', 'middle')
        .text(d => d.data.file.split('/').pop());

      // Zoom behavior
      const zoom = d3.zoom().scaleExtent([0.1, 4]).on('zoom', e => g.attr('transform', e.transform));
      svg.call(zoom);
      
      d3.select('#zoom-in').on('click', () => svg.transition().call(zoom.scaleBy, 1.3));
      d3.select('#zoom-out').on('click', () => svg.transition().call(zoom.scaleBy, 0.7));
    }

    loadGraph();
  </script>
</body>
</html>
```

---

## 3.8 Graphviz DOT Export

```typescript
// src/renderer/graphviz.ts
import { DependencyGraph } from '../graph/types';

export function toGraphvizDot(graph: DependencyGraph): string {
  const lines = ['digraph dependencies {'];
  lines.push('  rankdir=LR;');
  lines.push('  node [shape=box];');

  const colors: Record<string, string> = {
    javascript: '#f7df1e',
    typescript: '#3178c6',
    python: '#3776ab',
    html: '#e34f26',
  };

  for (const node of graph.nodes.values()) {
    const color = colors[node.language] || '#888888';
    const label = node.file.split('/').pop();
    lines.push(`  "${node.id}" [label="${label}" style=filled fillcolor="${color}"];`);
  }

  for (const edge of graph.edges) {
    lines.push(`  "${edge.source}" -> "${edge.target}";`);
  }

  lines.push('}');
  return lines.join('\n');
}
```

---

## 3.9 CLI Command (src/cli/index.ts)

```typescript
#!/usr/bin/env node
import { Command } from 'commander';
import { GraphBuilder } from '../graph/builder';
import { toGraphvizDot } from '../renderer/graphviz';
import { startServer } from '../server';

const program = new Command();

program
  .name('dependency-graph')
  .description('Generate interactive dependency graphs for JS/TS/Python/HTML')
  .argument('<file>', 'Entry file to analyze')
  .option('-d, --depth <number>', 'Max depth to traverse', '3')
  .option('-f, --format <format>', 'Output format: html|json|dot', 'html')
  .action(async (file: string, opts) => {
    const builder = new GraphBuilder(parseInt(opts.depth));
    const graph = builder.buildGraph(file);

    if (opts.format === 'json') {
      console.log(JSON.stringify(graph, null, 2));
    } else if (opts.format === 'dot') {
      console.log(toGraphvizDot(graph));
    } else {
      await startServer(graph, file);
    }
  });

program.parse();
```

---

## 3.10 HTTP Server (src/server/index.ts)

```typescript
// src/server/index.ts
import http from 'http';
import fs from 'fs';
import path from 'path';
import { DependencyGraph } from '../graph/types';

export function startServer(graph: DependencyGraph, entryFile: string): Promise<void> {
  return new Promise((resolve) => {
    const PORT = 3000;
    
    const server = http.createServer((req, res) => {
      const url = new URL(req.url || '', `http://localhost:${PORT}`);
      
      if (url.pathname === '/graph') {
        res.writeHead(200, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify(graph));
      } else if (url.pathname === '/export/dot') {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end(toGraphvizDot(graph));
      } else {
        const filePath = path.join(__dirname, '../../static/index.html');
        res.writeHead(200, { 'Content-Type': 'text/html' });
        res.end(fs.readFileSync(filePath));
      }
    });

    server.listen(PORT, () => {
      console.log(`Dependency graph running at http://localhost:${PORT}/graph?file=${encodeURIComponent(entryFile)}`);
      // Auto-open browser (platform-specific)
      const { exec } = require('child_process');
      const open = process.platform === 'win32' ? 'start' : 'open';
      exec(`${open} http://localhost:${PORT}/graph?file=${encodeURIComponent(entryFile)}`);
    });
  });
}
```

---

## 4. Implementation Order

| Step | Task | File | Estimated Time |
|------|------|------|----------------|
| 1 | Project setup | package.json, tsconfig.json | 15 min |
| 2 | Base parser interface | src/parsers/base.ts | 15 min |
| 3 | JS/TS parser | src/parsers/javascript.ts | 1 hr |
| 4 | Python parser | src/parsers/python.ts | 45 min |
| 5 | HTML parser | src/parsers/html.ts | 30 min |
| 6 | Parser factory | src/parsers/index.ts | 15 min |
| 7 | Graph types | src/graph/types.ts | 10 min |
| 8 | Graph builder | src/graph/builder.ts | 1 hr |
| 9 | D3 HTML page | static/index.html | 1 hr |
| 10 | Graphviz export | src/renderer/graphviz.ts | 30 min |
| 11 | HTTP server | src/server/index.ts | 30 min |
| 12 | CLI command | src/cli/index.ts | 30 min |
| 13 | Error handling | All modules | 30 min |
| 14 | Unit tests | tests/*.test.ts | 2 hr |

**Total Estimate:** ~10 hours

---

## 5. File Creation Sequence

```
dependency-graph/
├── package.json           # Step 1
├── tsconfig.json          # Step 1
├── src/
│   ├── parsers/
│   │   ├── base.ts        # Step 2
│   │   ├── javascript.ts  # Step 3
│   │   ├── python.ts      # Step 4
│   │   ├── html.ts        # Step 5
│   │   └── index.ts       # Step 6
│   ├── graph/
│   │   ├── types.ts       # Step 7
│   │   └── builder.ts     # Step 8
│   ├── renderer/
│   │   └── graphviz.ts    # Step 10
│   ├── server/
│   │   └── index.ts       # Step 11
│   └── cli/
│       └── index.ts       # Step 12
├── static/
│   └── index.html         # Step 9
└── tests/
    ├── parsers.test.ts    # Step 14
    └── graph.test.ts      # Step 14
```

---

## 6. Next Steps

1. [ ] Create `dependency-graph/` directory in `.opencode/`
2. [ ] Initialize package.json with all dependencies
3. [ ] Create tsconfig.json for TypeScript compilation
4. [ ] Implement parsers in isolation (test with sample files)
5. [ ] Build graph builder with circular dependency detection
6. [ ] Create D3 visualization HTML
7. [ ] Wire CLI command and server
8. [ ] Add comprehensive error handling
9. [ ] Write unit tests with vitest
10. [ ] Integration test with real codebase

---

## 7. Integration with opencode

Add to `.opencode/`:
```typescript
// Custom command registration
export const commands = {
  'dependency-graph': {
    description: 'Generate dependency graph for a file',
    execute: async (filePath: string, options: Record<string, any>) => {
      const builder = new GraphBuilder(options.depth || 3);
      const graph = builder.buildGraph(filePath);
      await startServer(graph, filePath);
    }
  }
};
```

---

*Plan ready for implementation.*
