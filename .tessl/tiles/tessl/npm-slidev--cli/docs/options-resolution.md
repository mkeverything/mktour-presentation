# Options Resolution

Configuration resolution system for processing entry options and creating resolved configurations with theme integration, addon support, and utility function initialization.

## Capabilities

### Resolve Options Function

Main configuration resolution function that processes entry options and creates a complete resolved configuration for Slidev operations.

```typescript { .api }
/**
 * Resolve Slidev configuration from entry options and mode
 * @param entryOptions - User-provided entry configuration
 * @param mode - Operating mode for the configuration
 * @returns Promise<ResolvedSlidevOptions> - Complete resolved configuration
 */
function resolveOptions(
  entryOptions: SlidevEntryOptions,
  mode: 'dev' | 'build' | 'export'
): Promise<ResolvedSlidevOptions>;

interface SlidevEntryOptions {
  /** Markdown entry file path */
  entry: string;
  /** Theme identifier (package name, local path, or 'none') */
  theme?: string;
  /** Remote control password */
  remote?: string;
  /** Enable Vite inspect plugin for debugging */
  inspect?: boolean;
  /** Enable PDF download in build mode */
  download?: boolean;
  /** Base URL for deployment */
  base?: string;
}
```

**Usage Examples:**

```typescript
import { resolveOptions } from "@slidev/cli";

// Basic development configuration
const devOptions = await resolveOptions({
  entry: "slides.md"
}, "dev");

// Production build with custom theme
const buildOptions = await resolveOptions({
  entry: "presentation.md",
  theme: "@slidev/theme-seriph",
  base: "/demo/",
  download: true
}, "build");

// Export configuration with custom entry
const exportOptions = await resolveOptions({
  entry: "./docs/slides.md",
  theme: "./my-theme"
}, "export");
```

### Resolved Options Structure

Complete resolved configuration structure containing all necessary information for Slidev operations.

```typescript { .api }
interface ResolvedSlidevOptions extends RootsInfo, SlidevEntryOptions {
  /** Parsed presentation data */
  data: SlidevData;
  /** Raw theme identifier as provided */
  themeRaw: string;
  /** Resolved theme root directories */
  themeRoots: string[];
  /** Resolved addon root directories */
  addonRoots: string[];
  /** All resolved root directories (themes + addons + user) */
  roots: string[];
  /** Operating mode */
  mode: 'dev' | 'build' | 'export';
  /** Resolved utility functions and configurations */
  utils: ResolvedSlidevUtils;
}

interface RootsInfo {
  /** CLI package root directory */
  cliRoot: string;
  /** Client assets root directory */
  clientRoot: string;
  /** User project root directory */
  userRoot: string;
  /** Parsed user package.json */
  userPkgJson: Record<string, any>;
  /** User workspace root directory */
  userWorkspaceRoot: string;
}
```

### Data Utils Creation

Utility function creation system for resolved configurations with performance optimizations and caching.

```typescript { .api }
/**
 * Create utility functions and configurations for resolved options
 * @param resolved - Resolved options without utils
 * @returns Promise<ResolvedSlidevUtils> - Complete utility configuration
 */
function createDataUtils(
  resolved: Omit<ResolvedSlidevOptions, 'utils'>
): Promise<ResolvedSlidevUtils>;

interface ResolvedSlidevUtils {
  /** Configured Shiki syntax highlighter */
  shiki: HighlighterGeneric<any, any>;
  /** Shiki configuration options */
  shikiOptions: MarkdownItShikiOptions;
  /** KaTeX math rendering options (null if disabled) */
  katexOptions: KatexOptions | null;
  /** Generated HTML template for index.html */
  indexHtml: string;
  /** Vite define constants for build-time replacement */
  define: Record<string, string>;
  /** Icon resolution paths for unplugin-icons */
  iconsResolvePath: string[];
  /** Function to check if Monaco types should be ignored */
  isMonacoTypesIgnored: (pkg: string) => boolean;
  /** Function to get available layouts (cached) */
  getLayouts: () => Record<string, string>;
}
```

**Usage Examples:**

```typescript
import { createDataUtils, resolveOptions } from "@slidev/cli";

// Create utils for custom resolved options
const partialOptions = {
  // ... other resolved options without utils
  data: slidevData,
  roots: [themeRoot, userRoot],
  mode: 'dev' as const
};

const utils = await createDataUtils(partialOptions);

// Access utility functions
const layouts = utils.getLayouts();
const isIgnored = utils.isMonacoTypesIgnored('@types/node');
console.log("Available layouts:", Object.keys(layouts));
```

### Theme Resolution Process

Theme resolution system supporting npm packages, local themes, and the special 'none' theme.

```typescript { .api }
// Theme resolution workflow
interface ThemeResolution {
  /** Theme identifier processing */
  identifierResolution: {
    npmPackage: string;          // "@slidev/theme-default"
    localPath: string;           // "./my-theme" or "/abs/path"  
    builtinTheme: string;        // "default", "seriph", etc.
    noneTheme: "none";           // Disable theming
  };
  
  /** Theme metadata loading */
  metadataLoading: {
    packageJson: "theme-package-json";
    slidevMeta: "slidev-field-metadata";
    defaults: "theme-default-config";
    colorSchema: "light-dark-both";
  };
  
  /** Theme root resolution */
  rootResolution: {
    packageRoot: string;         // Theme package directory
    layoutsDir: string;          // Theme layouts directory
    stylesDir: string;           // Theme styles directory
    componentsDir: string;       // Theme components directory
  };
}

// Theme metadata structure
interface SlidevThemeMeta {
  /** Default configuration overrides */
  defaults?: Partial<SlidevConfig>;
  /** Supported color schemes */
  colorSchema?: 'dark' | 'light' | 'both';
  /** Required syntax highlighter */
  highlighter?: 'shiki';
}
```

### Addon Resolution Process

Addon resolution system for extending Slidev functionality through npm packages.

```typescript { .api }
// Addon resolution workflow
interface AddonResolution {
  /** Addon discovery */
  discovery: {
    configField: "config.addons";    // From slidev.config or frontmatter
    npmPackages: string[];           // Array of npm package names
    localPaths: string[];            // Local addon directories
  };
  
  /** Addon validation */
  validation: {
    packageExists: boolean;
    validStructure: boolean;
    compatibleVersion: boolean;
  };
  
  /** Addon integration */
  integration: {
    rootDirectories: string[];       // Addon root paths
    mergedConfigs: "addon-configs";  // Merged addon configurations
    pluginRegistration: "automatic"; // Auto-register Vite plugins
  };
}

// Addon package structure requirements
interface AddonStructure {
  "package.json": {
    name: string;
    slidev?: {
      /** Addon metadata */
      addonVersion: string;
      compatibleSlidevVersion: string;
    };
  };
  
  /** Optional addon files */
  "layouts/"?: "vue-layout-components";
  "components/"?: "vue-components";
  "styles/"?: "css-style-files";
  "setup/"?: "setup-scripts";
  "vite.config.ts"?: "vite-configuration";
}
```

### Configuration Merging Process

Configuration merging system that combines user config, theme defaults, and addon configurations.

```typescript { .api }
// Configuration precedence (highest to lowest)
interface ConfigurationPrecedence {
  1: "cli-arguments";           // Command line options
  2: "frontmatter";            // Slide frontmatter  
  3: "slidev-config";          // slidev.config.ts
  4: "addon-configs";          // Addon-provided configs
  5: "theme-defaults";         // Theme default configs
  6: "builtin-defaults";       // Slidev built-in defaults
}

// Configuration merging rules
interface MergingRules {
  /** Simple values */
  primitives: "last-wins";      // Strings, numbers, booleans
  
  /** Arrays */
  arrays: "concatenate";        // Merge arrays together
  
  /** Objects */  
  objects: "deep-merge";        // Recursive object merging
  
  /** Special fields */
  specialFields: {
    css: "array-concatenation";
    fonts: "object-deep-merge";
    addons: "array-concatenation";
    monaco: "object-deep-merge";
  };
}
```

### Root Directory Resolution

System for resolving all relevant root directories for asset and component discovery.

```typescript { .api }
// Root directory types and purposes
interface RootDirectoryTypes {
  /** CLI package root */
  cliRoot: {
    purpose: "builtin-assets-and-components";
    contains: ["client/", "node/", "templates/"];
  };
  
  /** Client assets root */
  clientRoot: {
    purpose: "client-side-assets";
    contains: ["main.ts", "components/", "styles/"];
  };
  
  /** User project root */
  userRoot: {
    purpose: "user-presentation-files";
    contains: ["slides.md", "components/", "public/"];
  };
  
  /** Theme roots */
  themeRoots: {
    purpose: "theme-provided-assets";
    contains: ["layouts/", "components/", "styles/"];
  };
  
  /** Addon roots */
  addonRoots: {
    purpose: "addon-provided-functionality";
    contains: ["layouts/", "components/", "setup/"];
  };
}

// Directory resolution order
interface ResolutionOrder {
  /** Asset resolution priority (first found wins) */
  assets: ["userRoot", "themeRoots", "addonRoots", "clientRoot"];
  
  /** Component resolution priority */
  components: ["userRoot", "themeRoots", "addonRoots", "clientRoot"];
  
  /** Layout resolution priority */
  layouts: ["userRoot", "themeRoots", "addonRoots", "clientRoot"];
  
  /** Setup script resolution priority */
  setup: ["userRoot", "themeRoots", "addonRoots", "cliRoot"];
}
```

### Build-time Constants Generation

System for generating build-time constants and feature flags based on configuration.

```typescript { .api }
/**
 * Generate Vite define constants for build-time replacement
 * @param options - Resolved options without utils
 * @returns Record<string, string> - Define constants map
 */
function getDefine(
  options: Omit<ResolvedSlidevOptions, 'utils'>
): Record<string, string>;

// Generated constants
interface DefineConstants {
  /** Environment flags */
  __DEV__: boolean;                              // Development mode
  __SLIDEV_CLIENT_ROOT__: string;                // Client root path (@fs format)
  __SLIDEV_HASH_ROUTE__: boolean;                // Hash routing enabled
  __SLIDEV_HAS_SERVER__: boolean;                // Server available
  
  /** Feature flags */
  __SLIDEV_FEATURE_DRAWINGS__: boolean;          // Drawing tools enabled
  __SLIDEV_FEATURE_EDITOR__: boolean;            // Editor enabled
  __SLIDEV_FEATURE_DRAWINGS_PERSIST__: boolean;  // Drawing persistence
  __SLIDEV_FEATURE_RECORD__: boolean;            // Recording enabled
  __SLIDEV_FEATURE_PRESENTER__: boolean;         // Presenter mode
  __SLIDEV_FEATURE_PRINT__: boolean;             // Print/export enabled
  __SLIDEV_FEATURE_BROWSER_EXPORTER__: boolean;  // Browser export
  __SLIDEV_FEATURE_WAKE_LOCK__: boolean;         // Wake lock API
}
```

### Error Handling in Resolution

Error handling and validation during the options resolution process.

```typescript { .api }
// Resolution error scenarios
interface ResolutionErrors {
  /** Entry file errors */
  entryFileErrors: {
    notFound: "create-from-template-prompt";
    notReadable: "permission-error";
    invalidFormat: "format-validation-error";
  };
  
  /** Theme resolution errors */
  themeErrors: {
    notFound: "fallback-to-default";
    invalidStructure: "validation-error";
    incompatibleVersion: "version-warning";
  };
  
  /** Addon resolution errors */
  addonErrors: {
    notFound: "skip-with-warning";
    invalidStructure: "skip-with-error";
    circularDependency: "resolution-error";
  };
  
  /** Configuration errors */
  configErrors: {
    parseError: "syntax-error-details";
    validationError: "validation-failure-details";
    mergeConflict: "conflict-resolution-guidance";
  };
}
```

**Usage Examples:**

```typescript
import { resolveOptions } from "@slidev/cli";

async function resolveWithErrorHandling() {
  try {
    const options = await resolveOptions({
      entry: "slides.md",
      theme: "nonexistent-theme"
    }, "dev");
    
    console.log("Resolution successful");
    
  } catch (error) {
    if (error.message.includes('theme')) {
      console.log("Theme resolution failed, using default theme");
      // Retry with default theme
    } else if (error.message.includes('entry')) {
      console.log("Entry file not found, creating from template");
      // Handle entry file creation
    } else {
      console.error("Resolution failed:", error);
    }
  }
}
```

## Integration with Other Systems

The options resolution system integrates with all other Slidev components:

- **CLI Commands**: All commands use `resolveOptions()` as first step
- **Development Server**: Uses resolved options for Vite configuration
- **Build System**: Uses resolved options for production builds
- **Export System**: Uses resolved options for export configuration
- **Vite Plugin**: Receives resolved options for plugin configuration

The resolution process ensures consistent configuration across all Slidev operations while providing flexibility for customization and extension.