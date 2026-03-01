# Slidev CLI

Slidev is a modern presentation framework designed specifically for developers that transforms Markdown files into interactive, web-based slide presentations. It provides a comprehensive CLI tool for creating, developing, and building presentation slides with built-in features including syntax highlighting for code blocks, live coding capabilities, Vue component integration, multiple export formats (PDF, PPTX, PNG), presenter mode with speaker notes, drawing and annotation tools, LaTeX math support, Mermaid diagram generation, theme system with npm package distribution, UnoCSS styling, recording capabilities, and real-time development server with hot reloading.

## Package Information

- **Package Name**: @slidev/cli
- **Package Type**: npm
- **Language**: TypeScript/JavaScript
- **Installation**: `npm install @slidev/cli` or `npm create slidev`
- **Global Installation**: `npm install -g @slidev/cli`

## Core Imports

**Node.js API:**

```typescript
import { createServer, parser, ViteSlidevPlugin } from "@slidev/cli";
import { resolveOptions } from "@slidev/cli";
```

For CommonJS:

```javascript
const { createServer, parser, ViteSlidevPlugin } = require("@slidev/cli");
```

**CLI Usage:**

```bash
# Global installation
npx slidev [entry.md]        # Start development server
npx slidev build [entry.md]  # Build static site
npx slidev export [entry.md] # Export to PDF/PNG/PPTX
```

## Basic Usage

**CLI Development Server:**

```bash
# Start development server with slides.md
slidev

# Start with custom entry file
slidev my-presentation.md

# Start with custom port and open browser
slidev --port 3000 --open

# Enable remote control
slidev --remote mypassword
```

**Node.js API Server:**

```typescript
import { createServer, resolveOptions } from "@slidev/cli";

const options = await resolveOptions({ entry: "slides.md" }, "dev");
const server = await createServer(options, {
  server: { port: 3000 }
});

await server.listen();
```

**Building for Production:**

```bash
# Build static site
slidev build

# Build with custom output directory
slidev build --out dist

# Build with PDF download enabled
slidev build --download
```

## Architecture

Slidev is built around several key components:

- **CLI Interface**: Command-line tool providing development server, build, export, and formatting capabilities
- **Vite Integration**: Built on Vite for fast development and optimized production builds
- **Vue.js Framework**: Uses Vue 3 for component rendering and interactive features
- **Markdown Parser**: Custom parser for slide content with frontmatter support and slide separation
- **Theme System**: Extensible theme architecture supporting npm-distributed themes
- **Export Engine**: Multi-format export system using Playwright for PDF/PNG/PPTX generation
- **Development Tools**: Hot reloading, presenter mode, drawing tools, and remote control capabilities

## Capabilities

### CLI Commands

Core command-line interface providing development server, build, export, and utility commands for Slidev presentations.

```typescript { .api }
// Development server command
slidev [entry] [options]

// Build command  
slidev build [entry..] [options]

// Export command
slidev export [entry..] [options]

// Format command
slidev format [entry..]

// Theme commands
slidev theme eject [options]

// Export notes command
slidev export-notes [entry..] [options]
```

[CLI Commands](./cli-commands.md)

### Development Server

Node.js API for creating and managing Slidev development servers programmatically.

```typescript { .api }
function createServer(
  options: ResolvedSlidevOptions,
  viteConfig?: InlineConfig,
  serverOptions?: SlidevServerOptions
): Promise<ViteDevServer>;
```

[Development Server](./development-server.md)

### Build System

Production build functionality for generating static sites and handling export integration.

```typescript { .api }
function build(
  options: ResolvedSlidevOptions,
  viteConfig?: InlineConfig,
  args: BuildArgs
): Promise<void>;
```

[Build System](./build-system.md)

### Export System

Multi-format export capabilities for generating PDF, PNG, PPTX, and Markdown outputs from Slidev presentations.

```typescript { .api }
function exportSlides(options: ExportOptions): Promise<string>;
function exportNotes(options: NotesExportOptions): Promise<string>;
function getExportOptions(args: ExportArgs, options: ResolvedSlidevOptions, defaultOutput?: string): ExportOptions;
```

[Export System](./export-system.md)

### Options Resolution

Configuration resolution system for processing entry options and creating resolved configurations.

```typescript { .api }
function resolveOptions(
  entryOptions: SlidevEntryOptions,
  mode: 'dev' | 'build' | 'export'
): Promise<ResolvedSlidevOptions>;
```

[Options Resolution](./options-resolution.md)

### Vite Plugin

Vite plugin system providing all necessary transformations and integrations for Slidev functionality.

```typescript { .api }
function ViteSlidevPlugin(
  options: ResolvedSlidevOptions,
  pluginOptions?: SlidevPluginOptions,
  serverOptions?: SlidevServerOptions
): Promise<PluginOption[]>;
```

[Vite Plugin](./vite-plugin.md)

### Markdown Parser

Re-exported parser functionality from `@slidev/parser/fs` for processing Slidev markdown files with frontmatter and slide separation.

```typescript { .api }
// Re-exported as parser namespace from @slidev/parser/fs
import { parser } from "@slidev/cli";

// Parser provides functions for loading, parsing, and processing slide markdown
const parser: {
  load: (userRoot: string, entry: string, loadedSource?: Record<string, string>, mode?: string) => Promise<SlidevMarkdown>;
  parse: (content: string, filepath: string) => SlidevMarkdown;
  save: (markdown: SlidevMarkdown) => Promise<void>;
  prettify: (markdown: SlidevMarkdown) => void;
  resolveConfig: (headmatter: Record<string, any>, themeMeta?: SlidevThemeMeta, entry?: string) => SlidevConfig;
};
```

## Core Types

```typescript { .api }
interface SlidevEntryOptions {
  /** Markdown entry file path */
  entry: string;
  /** Theme identifier */
  theme?: string;
  /** Remote password for control */
  remote?: string;
  /** Enable inspect plugin */
  inspect?: boolean;
  /** Build with download option */
  download?: boolean;
  /** Base URL in dev or build mode */
  base?: string;
}

interface ResolvedSlidevOptions extends RootsInfo, SlidevEntryOptions {
  data: SlidevData;
  themeRaw: string;
  themeRoots: string[];
  addonRoots: string[];
  roots: string[];
  mode: 'dev' | 'build' | 'export';
  utils: ResolvedSlidevUtils;
}

interface SlidevServerOptions {
  loadData?: (loadedSource: Record<string, string>) => Promise<SlidevData | false>;
}

interface BuildArgs extends ExportArgs {
  out: string;
  base?: string;
  download?: boolean;
  inspect: boolean;
}

interface ExportArgs {
  'output'?: string;
  'format'?: string;
  'timeout'?: number;
  'wait'?: number;
  'wait-until'?: string;
  'range'?: string;
  'dark'?: boolean;
  'with-clicks'?: boolean;
  'executable-path'?: string;
  'with-toc'?: boolean;
  'per-slide'?: boolean;
  'scale'?: number;
  'omit-background'?: boolean;
}

interface SlidevMarkdown {
  filepath: string;
  slides: SourceSlideInfo[];
  headmatter: Record<string, any>;
  features: SlidevDetectedFeatures;
}

interface SourceSlideInfo {
  filepath: string;
  index: number;
  start: number;
  end: number;
  raw: string;
  content: string;
  frontmatter: Record<string, any>;
  title?: string;
  level?: number;
}

interface SlidevDetectedFeatures {
  katex: boolean;
  monaco: false | { types: string[]; deps: string[] };
  tweet: boolean;
  mermaid: boolean;
}

interface SlidevThemeMeta {
  defaults?: Partial<SlidevConfig>;
  colorSchema?: 'dark' | 'light' | 'both';
  highlighter?: 'shiki';
}

interface SlidevConfig {
  // Core configuration interface (extensive type definition)
  [key: string]: any;
}
```