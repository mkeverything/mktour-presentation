# Build System

Production build functionality for generating static sites and Single Page Applications (SPAs) from Slidev presentations with optimized assets and optional PDF generation.

## Capabilities

### Build Function

Creates optimized static site builds of Slidev presentations suitable for deployment to web servers or CDNs.

```typescript { .api }
/**
 * Build hostable SPA from Slidev presentation
 * @param options - Resolved Slidev configuration options
 * @param viteConfig - Optional Vite build configuration overrides
 * @param args - Build command arguments and options
 * @returns Promise that resolves when build completes
 */
function build(
  options: ResolvedSlidevOptions,
  viteConfig?: InlineConfig,
  args: BuildArgs
): Promise<void>;
```

**Usage Examples:**

```typescript
import { build, resolveOptions } from "@slidev/cli";

// Basic build
const options = await resolveOptions({ entry: "slides.md" }, "build");
await build(options, {}, { out: "dist", inspect: false });

// Build with custom configuration
await build(options, {
  build: {
    outDir: "public",
    assetsDir: "assets"
  },
  base: "/presentation/"
}, {
  out: "public",
  base: "/presentation/",
  download: true,
  inspect: false
});

// Build multiple presentations
const presentations = ["intro.md", "advanced.md"];
for (const entry of presentations) {
  const opts = await resolveOptions({ entry }, "build");
  await build(opts, {}, {
    out: `dist/${entry.replace('.md', '')}`,
    inspect: false
  });
}
```

### Build Arguments Configuration

Configuration interface for build command arguments and options.

```typescript { .api }
interface BuildArgs extends ExportArgs {
  /** Output directory for built files */
  out: string;
  /** Base URL for deployment (e.g., "/demo/") */
  base?: string;
  /** Enable PDF download in built site */
  download?: boolean;
  /** Enable Vite inspect plugin for debugging */
  inspect: boolean;
}

interface ExportArgs {
  /** Output file path */
  'output'?: string;
  /** Export format: pdf|png|pptx|md */
  'format'?: string;
  /** Timeout for rendering in milliseconds */
  'timeout'?: number;
  /** Wait time before exporting in milliseconds */
  'wait'?: number;
  /** Wait until event: networkidle|load|domcontentloaded|none */
  'wait-until'?: string;
  /** Page ranges to export (e.g., "1,4-5,6") */
  'range'?: string;
  /** Export as dark theme */
  'dark'?: boolean;
  /** Export pages for every click animation */
  'with-clicks'?: boolean;
  /** Override Playwright browser executable path */
  'executable-path'?: string;
  /** Export pages with table of contents */
  'with-toc'?: boolean;
  /** Export slide by slide (breaks cross-slide links) */
  'per-slide'?: boolean;
  /** Scale factor for image export */
  'scale'?: number;
  /** Export PNG without browser background */
  'omit-background'?: boolean;
}
```

### Build Process Workflow

The build system follows a structured workflow for processing presentations.

```typescript { .api }
// Build process stages:
interface BuildWorkflow {
  /** 1. Generate temporary index.html */
  generateIndexHtml(): Promise<void>;
  
  /** 2. Execute Vite build process */  
  viteBuild(): Promise<void>;
  
  /** 3. Process OG image generation */
  generateOgImage(): Promise<void>;
  
  /** 4. Create deployment files */
  createDeploymentFiles(): Promise<void>;
  
  /** 5. Generate PDF download (if enabled) */
  generatePdfDownload(): Promise<void>;
  
  /** 6. Cleanup temporary files */
  cleanup(): Promise<void>;
}
```

**Detailed Process:**

```typescript
// 1. Index HTML Generation
interface IndexHtmlGeneration {
  /** Create temporary index.html from template */
  temporaryFile: string;           // options.utils.indexHtml content
  originalBackup?: string;         // Backup existing index.html
  restoration: "automatic";       // Restore original after build
}

// 2. Vite Build Configuration
interface ViteBuildConfig {
  /** Build optimizations */
  chunkSizeWarningLimit: 2000;    // Increased for presentation assets
  outDir: string;                 // From args.out
  assetsDir: string;              // Asset organization
  base: string;                   // From args.base
}

// 3. OG Image Processing
interface OgImageProcessing {
  /** Auto-generation conditions */
  autoGenerate: boolean;          // config.seoMeta.ogImage === 'auto'
  relativeImage: boolean;         // ogImage starts with '.'
  copyExisting: boolean;          // Copy existing og-image.png
  generateNew: boolean;           // Generate from first slide
}

// 4. Deployment Files
interface DeploymentFiles {
  /** SPA routing support */
  "404.html": "copy-of-index";    // GitHub Pages SPA support
  "_redirects": "spa-routing";    // Netlify SPA support
}

// 5. PDF Download Generation
interface PdfDownloadGeneration {
  /** Conditional generation */
  enabled: boolean;               // args.download or config.download
  outputPath: string;             // 'slidev-exported.pdf' in build dir
  serverPort: number;             // Temporary server for export
}
```

### Vite Configuration Integration

The build system integrates deeply with Vite's build process and configuration.

```typescript { .api }
/**
 * Vite configuration resolution for build mode
 * @param options - Resolved Slidev options
 * @param baseConfig - Base Vite configuration
 * @param userConfig - User-provided Vite configuration
 * @param mode - Build mode ('build')
 * @returns Promise<InlineConfig> - Resolved Vite configuration
 */
function resolveViteConfigs(
  options: ResolvedSlidevOptions,
  baseConfig: InlineConfig,
  userConfig: InlineConfig,
  mode: 'build'
): Promise<InlineConfig>;

// Automatic build optimizations:
interface BuildOptimizations {
  /** Code splitting */
  codeSplitting: {
    chunks: "vendor" | "app" | "theme";
    dynamicImports: boolean;
  };
  
  /** Asset optimization */
  assetOptimization: {
    images: "optimized";
    fonts: "subsetted";
    icons: "tree-shaken";
  };
  
  /** Bundle analysis */
  bundleAnalysis: {
    chunkSizeWarning: 2000;      // KB threshold
    inspectPlugin: boolean;      // Via args.inspect
  };
}
```

### Asset Processing

Comprehensive asset processing during the build phase.

```typescript { .api }
// Asset types and processing:
interface AssetProcessing {
  /** Static assets */
  staticAssets: {
    images: "copy-optimized";     // PNG, JPG, SVG, etc.
    fonts: "copy-subsetted";      // WOFF, WOFF2, TTF
    videos: "copy-as-is";         // MP4, WebM
    documents: "copy-as-is";      // PDF, etc.
  };
  
  /** Generated assets */
  generatedAssets: {
    css: "extracted-minified";   // From Vue SFCs and imports
    js: "bundled-minified";      // Application bundles
    html: "templated-minified";  // Main HTML file
  };
  
  /** Theme assets */
  themeAssets: {
    layouts: "compiled";         // Vue layout components
    styles: "merged";            // Theme-specific styles
    components: "tree-shaken";   // Used components only
  };
}
```

### Output Structure

Standard output directory structure for built presentations.

```typescript { .api }
// Built output structure:
interface BuildOutput {
  /** Root files */
  "index.html": "main-entry-point";
  "404.html": "spa-fallback";
  "_redirects": "netlify-config";
  "og-image.png"?: "social-media-preview";
  "slidev-exported.pdf"?: "download-version";
  
  /** Asset directories */
  "assets/": {
    "*.js": "javascript-bundles";
    "*.css": "stylesheet-files";
    "*.woff2": "font-files";
    "*.png|jpg|svg": "image-assets";
  };
  
  /** Static files */
  "public/": "copied-static-files";
}
```

### Error Handling and Recovery

Build error handling and recovery mechanisms.

```typescript { .api }
// Build error scenarios:
interface BuildErrorHandling {
  /** Vite build failures */
  viteBuildError: {
    cleanup: "restore-index-html";
    logging: "detailed-error-info";
    recovery: "manual-intervention";
  };
  
  /** Asset processing failures */
  assetError: {
    ogImage: "skip-generation";
    pdfExport: "warn-and-continue";
    staticAssets: "copy-available";
  };
  
  /** Configuration errors */
  configError: {
    validation: "pre-build-check";
    reporting: "detailed-messages";
    suggestions: "fix-recommendations";
  };
  
  /** File system errors */
  fileSystemError: {
    permissions: "check-and-report";
    diskSpace: "check-and-warn";
    cleanup: "best-effort";
  };
}
```

**Usage Examples:**

```typescript
import { build, resolveOptions } from "@slidev/cli";

async function buildWithErrorHandling() {
  try {
    const options = await resolveOptions({
      entry: "slides.md",
      base: "/demo/"
    }, "build");
    
    await build(options, {
      build: {
        outDir: "dist",
        reportCompressedSize: false  // Faster builds
      }
    }, {
      out: "dist",
      base: "/demo/",
      download: true,
      inspect: false
    });
    
    console.log("Build completed successfully");
    
  } catch (error) {
    if (error.message.includes('ENOSPC')) {
      console.error("Insufficient disk space for build");
    } else if (error.message.includes('permission')) {
      console.error("Permission denied writing to output directory");
    } else {
      console.error("Build failed:", error.message);
    }
  }
}
```

## Integration with CLI

The build function is used internally by the CLI `slidev build` command:

```bash
# CLI command internally calls:
slidev build [entry..]    # → build(resolvedOptions, viteConfig, args)
```

The programmatic API provides additional control over the build process for custom deployment workflows and CI/CD integration.