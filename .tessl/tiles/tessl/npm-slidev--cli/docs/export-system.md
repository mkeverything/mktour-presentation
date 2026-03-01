# Export System

Multi-format export capabilities for generating PDF, PNG, PPTX, and Markdown outputs from Slidev presentations using browser automation with Playwright.

## Capabilities

### Export Slides Function

Export slides to various formats with comprehensive configuration options for different output types.

```typescript { .api }
/**
 * Export slides to PDF, PNG, PPTX, or Markdown formats
 * @param options - Export configuration options
 * @returns Promise<string> - Path to exported file(s)
 */
function exportSlides(options: ExportOptions): Promise<string>;

interface ExportOptions {
  /** Total number of slides */
  total: number;
  /** Page ranges to export (e.g., "1,3-5,10") */
  range?: string;
  /** Slide information array */
  slides: SlideInfo[];
  /** Server port for rendering */
  port?: number;
  /** Base URL for server */
  base?: string;
  /** Output format */
  format?: 'pdf' | 'png' | 'pptx' | 'md';
  /** Output file path */
  output?: string;
  /** Rendering timeout in milliseconds */
  timeout?: number;
  /** Wait time before export in milliseconds */
  wait?: number;
  /** Wait until specific event */
  waitUntil: 'networkidle' | 'load' | 'domcontentloaded' | undefined;
  /** Export as dark theme */
  dark?: boolean;
  /** Router mode for URL generation */
  routerMode?: 'hash' | 'history';
  /** Slide width in pixels */
  width?: number;
  /** Slide height in pixels */
  height?: number;
  /** Export click animations */
  withClicks?: boolean;
  /** Custom browser executable path */
  executablePath?: string;
  /** Include table of contents */
  withToc?: boolean;
  /** Render slides individually (better for global components) */
  perSlide?: boolean;
  /** Scale factor for image export */
  scale?: number;
  /** Omit browser background for PNG */
  omitBackground?: boolean;
}
```

**Usage Examples:**

```typescript
import { exportSlides, resolveOptions } from "@slidev/cli";

// Basic PDF export
const options = await resolveOptions({ entry: "slides.md" }, "export");
const outputPath = await exportSlides({
  total: options.data.slides.length,
  slides: options.data.slides,
  port: 12445,
  format: 'pdf',
  output: 'presentation.pdf',
  waitUntil: 'networkidle'
});

// PNG export with custom dimensions
await exportSlides({
  total: options.data.slides.length,
  slides: options.data.slides,
  port: 12445,
  format: 'png',
  output: 'slides',  // Will create slides-001.png, slides-002.png, etc.
  width: 1920,
  height: 1080,
  scale: 2,
  omitBackground: true
});

// Export specific slides with clicks
await exportSlides({
  total: options.data.slides.length,
  slides: options.data.slides,
  port: 12445,
  range: "1,3-5,10",
  withClicks: true,
  dark: true,
  timeout: 60000
});
```

### Export Notes Function

Export slide notes to PDF format for speaker reference and presentation preparation.

```typescript { .api }
/**
 * Export slide notes to PDF format
 * @param options - Notes export configuration
 * @returns Promise<string> - Path to exported notes file
 */
function exportNotes(options: ExportNotesOptions): Promise<string>;

interface ExportNotesOptions {
  /** Server port for rendering */
  port?: number;
  /** Base URL for server */
  base?: string;
  /** Output file path */
  output?: string;
  /** Rendering timeout in milliseconds */
  timeout?: number;
  /** Wait time before export in milliseconds */
  wait?: number;
}
```

**Usage Examples:**

```typescript
import { exportNotes, createServer, resolveOptions } from "@slidev/cli";

// Export speaker notes
const options = await resolveOptions({ entry: "slides.md" }, "export");
const server = await createServer(options, { server: { port: 12445 } });
await server.listen();

const notesPath = await exportNotes({
  port: 12445,
  output: "speaker-notes.pdf",
  timeout: 30000,
  wait: 1000
});

await server.close();
```

### Export Options Resolution

Utility function for resolving export options from CLI arguments and Slidev configuration.

```typescript { .api }
/**
 * Resolve export options from CLI arguments and configuration
 * @param args - CLI export arguments
 * @param options - Resolved Slidev options
 * @param defaultOutput - Default output filename
 * @returns ExportOptions - Resolved export configuration
 */
function getExportOptions(
  args: ExportArgs,
  options: ResolvedSlidevOptions,
  defaultOutput?: string
): ExportOptions;

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
```

**Usage Examples:**

```typescript
import { getExportOptions, resolveOptions } from "@slidev/cli";

const options = await resolveOptions({ entry: "slides.md" }, "export");
const args = {
  'format': 'png',
  'dark': true,
  'with-clicks': true,
  'scale': 2
};

const exportOptions = getExportOptions(args, options, "my-presentation");
// Automatically resolves paths, dimensions, and configuration
```

### Format-Specific Export Options

Different export formats support specific configuration options and behaviors.

```typescript { .api }
// PDF Export Configuration
interface PdfExportOptions {
  /** Page format and margins */
  format: 'A4' | 'Letter' | 'Legal' | 'A3';
  landscape: boolean;           // Always true for presentations
  printBackground: boolean;     // Include CSS backgrounds
  
  /** PDF metadata */
  title?: string;              // From presentation title
  author?: string;             // From presentation config
  subject?: string;            // From presentation description
  
  /** PDF structure */
  withToc: boolean;            // Include bookmark navigation
  perSlide: boolean;           // Individual slide rendering
  
  /** Quality settings */
  scale: number;               // 1-3, affects quality/size
  timeout: number;             // Rendering timeout per slide
}

// PNG Export Configuration  
interface PngExportOptions {
  /** Image dimensions */
  width: number;               // Slide width in pixels
  height: number;              // Slide height in pixels
  scale: number;               // Device pixel ratio (1-3)
  
  /** Visual options */
  omitBackground: boolean;     // Transparent background
  quality: number;             // 0-100, compression quality
  
  /** File naming */
  output: string;              // Base filename (adds -001, -002, etc.)
  numbering: 'sequential' | 'slide-number';
}

// PPTX Export Configuration
interface PptxExportOptions {
  /** Slide dimensions */
  slideSize: 'LAYOUT_16x9' | 'LAYOUT_4x3' | 'LAYOUT_WIDE';
  
  /** Content preservation */
  preserveAnimations: boolean;  // Convert click animations
  embedFonts: boolean;         // Include custom fonts
  
  /** Export quality */
  imageQuality: 'high' | 'medium' | 'low';
  compressImages: boolean;
}

// Markdown Export Configuration
interface MarkdownExportOptions {
  /** Content structure */
  includeNotes: boolean;       // Speaker notes
  includeFrontmatter: boolean; // YAML frontmatter
  slideDelimiter: string;      // Slide separator
  
  /** Asset handling */
  extractAssets: boolean;      // Save images separately
  assetPath: string;           // Relative path for assets
}
```

### Browser Automation Configuration

Playwright browser automation settings for rendering slides.

```typescript { .api }
// Browser automation configuration
interface BrowserConfig {
  /** Browser selection */
  browser: 'chromium' | 'firefox' | 'webkit';
  executablePath?: string;     // Custom browser path
  
  /** Rendering options */
  headless: boolean;           // Always true for export
  deviceScaleFactor: number;   // From scale option
  viewport: {
    width: number;
    height: number;
  };
  
  /** Performance settings */
  timeout: number;             // Per-page timeout
  waitUntil: 'networkidle' | 'load' | 'domcontentloaded';
  
  /** Resource handling */
  ignoreHTTPSErrors: boolean;
  bypassCSP: boolean;         // For embedded content
  
  /** Network optimization */
  blockResources: string[];    // Block unnecessary resources
  cachingEnabled: boolean;
}

// Page rendering configuration
interface PageRenderConfig {
  /** Click simulation */
  withClicks: boolean;
  clickDelay: number;          // Delay between clicks (ms)
  maxClicks: number;           // Safety limit
  
  /** Theme handling */
  dark: boolean;
  colorScheme: 'light' | 'dark' | 'no-preference';
  
  /** Wait strategies */
  waitForSelector?: string;    // Custom wait selector
  waitForFunction?: string;    // Custom wait function
  additionalDelay: number;     // Extra wait time
}
```

### Export Progress and Monitoring

Progress tracking and monitoring capabilities for long-running exports.

```typescript { .api }
// Progress tracking interface
interface ExportProgress {
  /** Overall progress */
  currentSlide: number;
  totalSlides: number;
  percentage: number;
  
  /** Current operation */
  operation: 'loading' | 'rendering' | 'processing' | 'saving';
  slideTitle?: string;
  
  /** Timing information */
  startTime: Date;
  elapsedTime: number;        // Milliseconds
  estimatedRemaining: number; // Milliseconds
  
  /** Error tracking */
  errors: ExportError[];
  warnings: string[];
}

interface ExportError {
  slideIndex: number;
  slideTitle?: string;
  error: Error;
  recoverable: boolean;
  retryCount: number;
}

// Progress monitoring utilities
interface ProgressMonitoring {
  /** Progress callback for CLI progress bar */
  onProgress?: (progress: ExportProgress) => void;
  
  /** Error handling callback */
  onError?: (error: ExportError) => 'retry' | 'skip' | 'abort';
  
  /** Completion callback */
  onComplete?: (results: ExportResults) => void;
}
```

### Error Handling and Recovery

Comprehensive error handling for export operations.

```typescript { .api }
// Export error scenarios
interface ExportErrorHandling {
  /** Browser automation errors */
  browserErrors: {
    launchFailure: "install-playwright";
    navigationTimeout: "increase-timeout";
    renderingError: "retry-with-fallback";
    memoryLeak: "restart-browser";
  };
  
  /** Content rendering errors */
  contentErrors: {
    missingAssets: "skip-and-warn";
    fontLoadingFailure: "fallback-fonts";
    componentError: "error-placeholder";
    networkFailure: "offline-mode";
  };
  
  /** File system errors */
  fileSystemErrors: {
    permissionDenied: "check-permissions";
    diskFull: "cleanup-and-retry";
    pathNotFound: "create-directories";
  };
  
  /** Format-specific errors */
  formatErrors: {
    pdfGeneration: "retry-with-different-settings";
    imageCompression: "reduce-quality";
    pptxCreation: "fallback-to-pdf";
  };
}
```

**Usage Examples:**

```typescript
import { exportSlides } from "@slidev/cli";

async function exportWithErrorHandling() {
  try {
    const result = await exportSlides({
      total: 20,
      slides: slideData,
      port: 12445,
      format: 'pdf',
      timeout: 60000,  // Increased timeout
      waitUntil: 'networkidle'
    });
    
    console.log("Export completed:", result);
    
  } catch (error) {
    if (error.message.includes('timeout')) {
      console.log("Export timed out, trying with increased timeout...");
      // Retry with longer timeout
    } else if (error.message.includes('browser')) {
      console.log("Browser launch failed, check Playwright installation");
    } else {
      console.error("Export failed:", error);
    }
  }
}
```

## Integration with CLI

The export functions are used internally by CLI export commands:

```bash
# CLI commands internally call:
slidev export [entry..]           # → exportSlides(options)
slidev export-notes [entry..]     # → exportNotes(options)
slidev build --download           # → exportSlides() during build
```

The programmatic API provides fine-grained control over export processes for custom workflows and batch processing scenarios.