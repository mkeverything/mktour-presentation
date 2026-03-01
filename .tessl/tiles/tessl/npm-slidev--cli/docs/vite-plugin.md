# Vite Plugin

Vite plugin system providing all necessary transformations and integrations for Slidev functionality, including Markdown processing, Vue component handling, and asset optimization.

## Capabilities

### Main Vite Plugin Function

Creates a comprehensive array of Vite plugins that handle all aspects of Slidev presentation processing and development workflow.

```typescript { .api }
/**
 * Create complete Vite plugin configuration for Slidev
 * @param options - Resolved Slidev configuration options
 * @param pluginOptions - Optional plugin-specific configuration
 * @param serverOptions - Optional server-specific configuration
 * @returns Promise<PluginOption[]> - Array of configured Vite plugins
 */
function ViteSlidevPlugin(
  options: ResolvedSlidevOptions,
  pluginOptions?: SlidevPluginOptions,
  serverOptions?: SlidevServerOptions
): Promise<PluginOption[]>;

interface SlidevPluginOptions {
  /** Custom plugin configurations */
  [pluginName: string]: any;
}

interface SlidevServerOptions {
  /** Custom data loading function */
  loadData?: (loadedSource: Record<string, string>) => Promise<SlidevData | false>;
}
```

**Usage Examples:**

```typescript
import { ViteSlidevPlugin, resolveOptions } from "@slidev/cli";
import { createServer } from "vite";

// Basic plugin setup
const options = await resolveOptions({ entry: "slides.md" }, "dev");
const plugins = await ViteSlidevPlugin(options);

const viteServer = await createServer({
  plugins,
  server: { port: 3000 }
});

// Plugin with custom options
const pluginsWithOptions = await ViteSlidevPlugin(options, {
  // Plugin-specific options
  markdown: { 
    customRenderer: true 
  },
  vue: { 
    customBlocks: ['slide'] 
  }
});

// Plugin with server options
const pluginsWithServer = await ViteSlidevPlugin(options, {}, {
  loadData: async (loadedSource) => {
    console.log("Loaded files:", Object.keys(loadedSource));
    // Custom processing...
    return parsedData;
  }
});
```

### Individual Plugin Components

The main plugin function orchestrates multiple specialized plugins for different aspects of Slidev functionality.

```typescript { .api }
// Plugin component functions (internal)
interface PluginComponents {
  /** Slide loading and processing */
  createSlidesLoader(
    options: ResolvedSlidevOptions,
    serverOptions?: SlidevServerOptions
  ): Promise<PluginOption>;
  
  /** Markdown processing and transformation */
  createMarkdownPlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** Layout wrapper injection */
  createLayoutWrapperPlugin(
    options: ResolvedSlidevOptions
  ): Promise<PluginOption>;
  
  /** Context injection for slides */
  createContextInjectionPlugin(): Promise<PluginOption>;
  
  /** Vue.js integration */
  createVuePlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** HMR patch for development */
  createHmrPatchPlugin(): Promise<PluginOption>;
  
  /** Component auto-registration */
  createComponentsPlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** Icon processing */
  createIconsPlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** Remote asset handling */
  createRemoteAssetsPlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** Server reference injection */
  createServerRefPlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** Configuration extension */
  createConfigPlugin(
    options: ResolvedSlidevOptions
  ): Promise<PluginOption>;
  
  /** Monaco editor types */
  createMonacoTypesLoader(
    options: ResolvedSlidevOptions
  ): Promise<PluginOption>;
  
  /** Monaco editor writer */
  createMonacoWriterPlugin(
    options: ResolvedSlidevOptions
  ): Promise<PluginOption>;
  
  /** Vue compiler flags */
  createVueCompilerFlagsPlugin(
    options: ResolvedSlidevOptions
  ): Promise<PluginOption>;
  
  /** UnoCSS integration */
  createUnocssPlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** Static file copying */
  createStaticCopyPlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** Development inspection */
  createInspectPlugin(
    options: ResolvedSlidevOptions,
    pluginOptions?: SlidevPluginOptions
  ): Promise<PluginOption>;
  
  /** User-defined plugins */
  createUserVitePlugins(
    options: ResolvedSlidevOptions
  ): Promise<PluginOption>;
}
```

### Slides Loader Plugin

Core plugin responsible for loading and processing slide content with hot module reloading support.

```typescript { .api }
// Slides loader functionality
interface SlidesLoaderPlugin {
  /** Virtual module handling */
  virtualModules: {
    "/@slidev/slides": "slide-data-module";
    "/@slidev/config": "config-data-module"; 
    "/@slidev/context": "context-data-module";
  };
  
  /** File watching */
  fileWatching: {
    markdownFiles: string[];        // *.md files
    configFiles: string[];          // slidev.config.*, frontmatter
    setupFiles: string[];           // setup/ directory
    componentFiles: string[];       // components/ directory
  };
  
  /** Hot reloading */
  hotReloading: {
    slideUpdates: "granular-updates";     // Individual slide changes
    configUpdates: "full-reload";         // Configuration changes
    themeUpdates: "server-restart";       // Theme changes
    addFiles: "incremental-addition";     // New file detection
  };
  
  /** Data processing */
  dataProcessing: {
    markdownParsing: "frontmatter-aware";
    slideExtraction: "delimiter-based";
    metadataResolution: "merged-configs";
    assetDiscovery: "automatic";
  };
}
```

### Markdown Processing Plugin

Plugin for transforming Markdown content into Vue components with Slidev-specific features.

```typescript { .api }
// Markdown processing capabilities
interface MarkdownPlugin {
  /** Markdown parsing */
  parsing: {
    frontmatterExtraction: "yaml-parsing";
    slideDelimitation: "--- separator";
    codeBlockProcessing: "shiki-highlighting";
    mathProcessing: "katex-rendering";
  };
  
  /** Vue component generation */
  componentGeneration: {
    slideComponents: "vue-sfc-generation";
    layoutWrapping: "automatic-layout-selection";
    clickAnimations: "v-click-directive";
    transitionEffects: "slide-transitions";
  };
  
  /** Asset handling */
  assetHandling: {
    imageOptimization: "automatic-processing";
    remoteAssets: "caching-and-optimization";
    assetImports: "es-module-imports";
  };
  
  /** Extension support */
  extensionSupport: {
    mermaidDiagrams: "svg-generation";
    plantUmlDiagrams: "image-generation";
    mathEquations: "katex-rendering";
    codeHighlighting: "shiki-processing";
  };
}
```

### Vue Integration Plugin

Plugin for Vue.js framework integration with Slidev-specific enhancements.

```typescript { .api }
// Vue integration features
interface VueIntegrationPlugin {
  /** Component processing */
  componentProcessing: {
    singleFileComponents: "full-sfc-support";
    globalComponents: "auto-registration";
    layoutComponents: "theme-provided-layouts";
    slideComponents: "generated-from-markdown";
  };
  
  /** Directive support */
  directiveSupport: {
    vClick: "click-animation-directive";
    vMotion: "motion-animation-directive";
    vShow: "visibility-control-directive";
    vIf: "conditional-rendering-directive";
  };
  
  /** Composition API enhancements */
  compositionApi: {
    useSlideContext: "slide-specific-context";
    useNav: "navigation-utilities";
    useStorage: "persistent-storage";
    useClicks: "click-tracking";
  };
  
  /** Template features */
  templateFeatures: {
    slotSupport: "layout-slot-system";
    scopedSlots: "data-passing-support";
    dynamicComponents: "component-switching";
    asyncComponents: "lazy-loading";
  };
}
```

### UnoCSS Integration Plugin

Plugin for UnoCSS utility-first CSS framework integration with Slidev theming.

```typescript { .api }
// UnoCSS integration capabilities
interface UnocssPlugin {
  /** CSS generation */
  cssGeneration: {
    utilityClasses: "on-demand-generation";
    customClasses: "user-defined-utilities";
    themeClasses: "theme-provided-utilities";
    componentClasses: "component-specific-styles";
  };
  
  /** Configuration merging */
  configMerging: {
    userConfig: "uno.config.ts";
    themeConfig: "theme-provided-config";
    addonConfigs: "addon-provided-configs";
    builtinConfig: "slidev-defaults";
  };
  
  /** Development features */
  developmentFeatures: {
    hotReloading: "instant-css-updates";
    inspector: "css-class-inspection";
    debugging: "generated-css-viewing";
  };
  
  /** Optimization */
  optimization: {
    treeshaking: "unused-css-removal";
    minification: "production-css-minification";
    purging: "unused-utility-removal";
  };
}
```

### Asset Processing Plugins

Plugins for handling various types of assets used in presentations.

```typescript { .api }
// Asset processing capabilities
interface AssetProcessingPlugins {
  /** Icon processing */
  iconProcessing: {
    iconSets: "iconify-integration";
    customIcons: "svg-optimization";
    iconComponents: "vue-component-generation";
    treeshaking: "unused-icon-removal";
  };
  
  /** Image processing */
  imageProcessing: {
    optimization: "imagemin-integration";
    responsiveImages: "srcset-generation";
    lazyLoading: "intersection-observer";
    formats: "webp-avif-support";
  };
  
  /** Font processing */
  fontProcessing: {
    webfonts: "google-fonts-optimization";
    localFonts: "font-file-optimization";
    subsetting: "character-subset-generation";
    preloading: "font-preload-hints";
  };
  
  /** Static assets */
  staticAssets: {
    copying: "public-directory-copying";
    processing: "asset-pipeline-processing";
    optimization: "compression-and-caching";
    urls: "asset-url-resolution";
  };
}
```

### Development Tools Plugins

Plugins providing development-time tools and debugging capabilities.

```typescript { .api }
// Development tools capabilities
interface DevelopmentToolsPlugins {
  /** HMR enhancements */
  hmrEnhancements: {
    slideReloading: "granular-slide-updates";
    configReloading: "configuration-hot-reloading";
    componentReloading: "vue-component-hmr";
    styleReloading: "css-hot-reloading";
  };
  
  /** Inspection tools */
  inspectionTools: {
    viteInspector: "vite-plugin-inspect";
    bundleAnalyzer: "bundle-composition-analysis";
    performanceMonitor: "build-performance-metrics";
    dependencyAnalyzer: "dependency-graph-visualization";
  };
  
  /** Error handling */
  errorHandling: {
    enhancedErrors: "detailed-error-messages";
    stackTraces: "source-mapped-stack-traces";
    suggestions: "fix-suggestions";
    recovery: "error-recovery-mechanisms";
  };
  
  /** Debugging utilities */
  debuggingUtilities: {
    sourceMapping: "accurate-source-maps";
    breakpointSupport: "debugger-integration";
    console: "enhanced-console-logging";
    profiling: "performance-profiling";
  };
}
```

### Production Optimization Plugins

Plugins that optimize builds for production deployment.

```typescript { .api }
// Production optimization capabilities
interface ProductionOptimizationPlugins {
  /** Bundle optimization */
  bundleOptimization: {
    codeSplitting: "route-based-splitting";
    treeshaking: "dead-code-elimination";
    minification: "terser-minification";
    compression: "gzip-brotli-compression";
  };
  
  /** Asset optimization */
  assetOptimization: {
    imageCompression: "lossy-lossless-compression";
    fontSubsetting: "character-subset-optimization";
    cssMinification: "cssnano-optimization";
    htmlMinification: "html-minifier-optimization";
  };
  
  /** Caching strategies */
  cachingStrategies: {
    contentHashing: "cache-busting-hashes";
    serviceWorker: "offline-caching-support";
    cdnOptimization: "cdn-friendly-assets";
    preloading: "resource-preloading-hints";
  };
  
  /** Performance monitoring */
  performanceMonitoring: {
    bundleAnalysis: "bundle-size-analysis";
    loadingMetrics: "loading-performance-metrics";
    runtimeMetrics: "runtime-performance-tracking";
    lighthouseIntegration: "lighthouse-ci-integration";
  };
}
```

### Plugin Configuration Options

Configuration options for customizing plugin behavior across different modes.

```typescript { .api }
// Plugin configuration interface
interface PluginConfiguration {
  /** Mode-specific settings */
  modeSettings: {
    development: {
      hotReloading: boolean;
      sourceMapping: boolean;
      errorOverlay: boolean;
      inspection: boolean;
    };
    
    production: {
      minification: boolean;
      treeshaking: boolean;
      compression: boolean;
      optimization: boolean;
    };
    
    export: {
      assetInlining: boolean;
      pathResolution: boolean;
      staticGeneration: boolean;
    };
  };
  
  /** Feature toggles */
  featureToggles: {
    monaco: boolean;              // Monaco editor support
    drawings: boolean;            // Drawing tools
    recording: boolean;           // Recording capabilities
    presenter: boolean;           // Presenter mode
    print: boolean;               // Print/export support
  };
  
  /** Performance settings */
  performanceSettings: {
    chunkSizeLimit: number;       // Bundle size warnings
    assetSizeLimit: number;       // Asset size warnings
    parallelProcessing: boolean;  // Parallel plugin execution
    caching: boolean;             // Plugin result caching
  };
}
```

**Usage Examples:**

```typescript
import { ViteSlidevPlugin, resolveOptions } from "@slidev/cli";

// Plugin with custom configuration
const options = await resolveOptions({ entry: "slides.md" }, "dev");
const plugins = await ViteSlidevPlugin(options, {
  // Disable Monaco editor for faster builds
  monaco: { enabled: false },
  
  // Custom UnoCSS configuration
  unocss: { 
    shortcuts: {
      'slide-title': 'text-4xl font-bold text-center mb-8'
    }
  },
  
  // Enhanced Vue integration
  vue: {
    customBlocks: ['slide', 'layout'],
    reactivityTransform: true
  }
});

// Use plugins in Vite configuration
export default {
  plugins,
  // ... other Vite options
};
```

## Integration with Vite Ecosystem

The Slidev Vite plugin integrates seamlessly with the broader Vite ecosystem:

- **Vite Dev Server**: Full HMR support with Slidev-specific enhancements
- **Vite Build**: Production optimizations tailored for presentation sites
- **Vite Plugin API**: Compatible with standard Vite plugin conventions
- **Framework Integration**: Works with Vue.js and other Vite-supported frameworks

The plugin system ensures that Slidev presentations benefit from Vite's fast development experience while adding presentation-specific functionality and optimizations.