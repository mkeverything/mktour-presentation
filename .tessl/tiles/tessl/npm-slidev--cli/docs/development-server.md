# Development Server

Node.js API for creating and managing Slidev development servers programmatically with Vite integration.

## Capabilities

### Create Server Function

Creates a Vite development server configured for Slidev presentations with hot module reloading and optional server customization.

```typescript { .api }
/**
 * Create a Vite development server for Slidev presentations
 * @param options - Resolved Slidev configuration options
 * @param viteConfig - Optional Vite configuration overrides
 * @param serverOptions - Optional Slidev server configuration
 * @returns Promise resolving to ViteDevServer instance
 */
function createServer(
  options: ResolvedSlidevOptions,
  viteConfig?: InlineConfig,
  serverOptions?: SlidevServerOptions
): Promise<ViteDevServer>;
```

**Usage Examples:**

```typescript
import { createServer, resolveOptions } from "@slidev/cli";

// Basic server setup
const options = await resolveOptions({ entry: "slides.md" }, "dev");
const server = await createServer(options);
await server.listen();

// Server with custom port and host
const serverWithConfig = await createServer(options, {
  server: {
    port: 4000,
    host: "0.0.0.0"
  }
});

// Server with data loading customization
const serverWithLoader = await createServer(options, {}, {
  loadData: async (loadedSource) => {
    // Custom data processing
    console.log("Loading source files:", Object.keys(loadedSource));
    // Return false to trigger server restart
    return false;
  }
});
```

### Server Configuration Types

Configuration interfaces for customizing server behavior and Vite integration.

```typescript { .api }
interface SlidevServerOptions {
  /**
   * Custom data loading function called when source files change
   * @param loadedSource - Map of loaded source file paths to content
   * @returns Promise<SlidevData | false> - Return false to trigger server restart
   */
  loadData?: (loadedSource: Record<string, string>) => Promise<SlidevData | false>;
}

interface ResolvedSlidevOptions extends RootsInfo, SlidevEntryOptions {
  /** Parsed presentation data */
  data: SlidevData;
  /** Raw theme identifier */
  themeRaw: string;
  /** Theme root directories */
  themeRoots: string[];
  /** Addon root directories */
  addonRoots: string[];
  /** All root directories */
  roots: string[];
  /** Server mode */
  mode: 'dev' | 'build' | 'export';
  /** Utility functions and configurations */
  utils: ResolvedSlidevUtils;
}

interface RootsInfo {
  /** CLI package root directory */
  cliRoot: string;
  /** Client assets root directory */
  clientRoot: string;
  /** User project root directory */
  userRoot: string;
  /** User package.json contents */
  userPkgJson: Record<string, any>;
  /** User workspace root directory */
  userWorkspaceRoot: string;
}
```

### Vite Integration

The server automatically configures Vite with Slidev-specific plugins and optimizations.

```typescript { .api }
// Automatic Vite configuration includes:
interface AutoViteConfig {
  /** Optimized dependency pre-bundling */
  optimizeDeps: {
    entries: string[];        // Entry points for optimization
    force?: boolean;          // Force re-optimization
  };
  /** Development server configuration */
  server: {
    port?: number;            // Server port
    host?: string;            // Server host
    open?: boolean;           // Auto-open browser
    strictPort?: boolean;     // Fail if port unavailable
  };
  /** Logging configuration */
  logLevel: LogLevel;         // Vite logging level
  /** Base URL configuration */
  base: string;               // Public base path
}
```

**Usage Examples:**

```typescript
import { createServer, resolveOptions } from "@slidev/cli";
import type { InlineConfig } from "vite";

// Advanced server configuration
const viteConfig: InlineConfig = {
  server: {
    port: 3000,
    host: "localhost",
    open: true,
    strictPort: true
  },
  optimizeDeps: {
    force: true  // Force dependency re-optimization
  },
  logLevel: "info",
  base: "/presentation/"
};

const options = await resolveOptions({
  entry: "slides.md",
  theme: "@slidev/theme-default",
  base: "/presentation/"
}, "dev");

const server = await createServer(options, viteConfig);
await server.listen();
```

### Server Lifecycle Management

Methods for managing server lifecycle and handling cleanup.

```typescript { .api }
// ViteDevServer methods for lifecycle management
interface ViteDevServerLifecycle {
  /** Start listening on configured port */
  listen(port?: number, isRestart?: boolean): Promise<ViteDevServer>;
  
  /** Close server and cleanup resources */
  close(): Promise<void>;
  
  /** Restart server (close and recreate) */
  restart(forceOptimize?: boolean): Promise<void>;
  
  /** Server URL information */
  resolvedUrls?: {
    local: string[];
    network: string[];
  };
}
```

**Usage Examples:**

```typescript
import { createServer, resolveOptions } from "@slidev/cli";

async function serverLifecycleExample() {
  const options = await resolveOptions({ entry: "slides.md" }, "dev");
  const server = await createServer(options);
  
  try {
    // Start server
    await server.listen(3000);
    console.log("Server started:", server.resolvedUrls);
    
    // Server is running...
    
    // Restart server if needed
    await server.restart();
    
  } finally {
    // Always cleanup
    await server.close();
  }
}
```

### Hot Module Reloading

The server provides automatic hot reloading for slide content and configuration changes.

```typescript { .api }
// Automatic HMR triggers for:
interface HMRTriggers {
  /** Slide content changes */
  slideContentChanges: boolean;      // *.md files
  
  /** Configuration changes */
  configChanges: boolean;            // slidev.config.*, frontmatter
  
  /** Theme changes */
  themeChanges: boolean;             // Theme switching, theme files
  
  /** Component changes */
  componentChanges: boolean;         // *.vue files
  
  /** Asset changes */
  assetChanges: boolean;             // Images, styles, etc.
  
  /** Setup file changes */
  setupChanges: boolean;             // setup/ directory files
}

// Files that trigger full server restart:
interface RestartTriggers {
  configFields: string[];           // Monaco, routerMode, fonts, css, mdc, etc.
  createFiles: string[];            // global-*.vue, uno.config.*, etc.
  changeFiles: string[];            // setup/shiki.ts, setup/katex.ts, etc.
}
```

### Error Handling

Server error handling and recovery mechanisms.

```typescript { .api }
// Common error scenarios and handling
interface ServerErrorHandling {
  /** Port already in use - automatically finds available port */
  portConflicts: "auto-resolve";
  
  /** Missing dependencies - provides installation guidance */
  missingDependencies: "guidance";
  
  /** Invalid configuration - detailed error messages */
  configErrors: "detailed-messages";
  
  /** Theme resolution failures - fallback to default */
  themeErrors: "fallback";
  
  /** File system watch errors - graceful degradation */
  watchErrors: "graceful-degradation";
}
```

**Usage Examples:**

```typescript
import { createServer, resolveOptions } from "@slidev/cli";

async function errorHandlingExample() {
  try {
    const options = await resolveOptions({ entry: "slides.md" }, "dev");
    const server = await createServer(options, {
      server: {
        port: 3000,
        strictPort: false  // Allow port auto-resolution
      }
    });
    
    await server.listen();
    
  } catch (error) {
    if (error.code === 'EADDRINUSE') {
      console.log("Port in use, trying alternative...");
      // Server will automatically find available port
    } else if (error.message.includes('theme')) {
      console.log("Theme error, falling back to default");
    } else {
      console.error("Server startup failed:", error);
    }
  }
}
```

## Integration with CLI

The development server function is used internally by the CLI `slidev` command:

```bash
# CLI command internally calls:
slidev [entry]    # → createServer(resolvedOptions, viteConfig)
```

The programmatic API provides the same functionality with additional customization options for embedding Slidev in larger applications or custom development workflows.