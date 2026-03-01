# CLI Commands

Command-line interface providing development server, build, export, and utility commands for Slidev presentations.

## Capabilities

### Default Development Server Command

Start a local development server for Slidev presentations with hot reloading and optional remote control.

```bash { .api }
slidev [entry]
slidev [entry] [options]

# Options:
--port, -p <number>          # Port number (default: 3030)
--open, -o                   # Open in browser (default: false)
--remote <string>            # Enable remote control with password
--tunnel                     # Enable Cloudflare Quick Tunnel (default: false)
--log <level>                # Log level: error|warn|info|silent (default: warn)
--inspect                    # Enable inspect plugin for debugging (default: false)
--force, -f                  # Force optimizer to ignore cache (default: false)
--bind <string>              # IP addresses to listen on in remote mode (default: 0.0.0.0)
--base <string>              # Base URL (default: /)
--theme, -t <string>         # Override theme
```

**Usage Examples:**

```bash
# Start with default slides.md
slidev

# Start with custom file
slidev my-presentation.md

# Start with custom port and auto-open
slidev --port 4000 --open

# Enable remote control
slidev --remote secretpassword

# Enable tunnel for external access
slidev --remote mypass --tunnel

# Force cache refresh
slidev --force
```

### Build Command

Build hostable Single Page Application (SPA) from Slidev presentations.

```bash { .api }
slidev build [entry..]
slidev build [entry..] [options]

# Options:
--out, -o <string>           # Output directory (default: dist)
--base <string>              # Output base URL (e.g., /demo/)
--download, -d               # Allow PDF download in built site
--inspect                    # Enable inspect plugin for debugging (default: false)
--theme, -t <string>         # Override theme

# Export options (inherited):
--output <string>            # Path to output file
--format <string>            # Output format: pdf|png|pptx|md
--timeout <number>           # Timeout for rendering print page
--wait <number>              # Wait time in ms before exporting
--wait-until <string>        # Wait until event: networkidle|load|domcontentloaded|none
--range <string>             # Page ranges (e.g., "1,4-5,6")
--dark                       # Export as dark theme
--with-clicks, -c            # Export pages for every click
--executable-path <string>   # Override Playwright browser executable
--with-toc                   # Export pages with outline
--per-slide                  # Export slide by slide (breaks cross-slide links)
--scale <number>             # Scale factor for image export
--omit-background            # Export PNG without browser background
```

**Usage Examples:**

```bash
# Build single presentation
slidev build

# Build multiple presentations
slidev build presentation1.md presentation2.md

# Build with custom output and download enabled
slidev build --out public --download

# Build with custom base URL for deployment
slidev build --base /presentations/
```

### Export Command

Export slides to PDF, PNG, PPTX, or Markdown formats using browser automation.

```bash { .api }
slidev export [entry..]
slidev export [entry..] [options]

# Options:
--output <string>            # Path to output file
--format <string>            # Output format: pdf|png|pptx|md (default: pdf)
--timeout <number>           # Timeout for rendering print page (default: 30000)
--wait <number>              # Wait time in ms before exporting (default: 0)
--wait-until <string>        # Wait until event: networkidle|load|domcontentloaded|none
--range <string>             # Page ranges to export (e.g., "1,4-5,6")
--dark                       # Export as dark theme
--with-clicks, -c            # Export pages for every click animation
--executable-path <string>   # Override Playwright browser executable
--with-toc                   # Export pages with table of contents
--per-slide                  # Export slide by slide (better for global components)
--scale <number>             # Scale factor for image export
--omit-background            # Export PNG pages without browser background
--theme, -t <string>         # Override theme
```

**Usage Examples:**

```bash
# Export to PDF (default)
slidev export presentation.md

# Export to PNG images
slidev export --format png

# Export specific slide ranges
slidev export --range "1,3-5,10"

# Export with click animations
slidev export --with-clicks

# Export dark theme version
slidev export --dark

# Export with custom output name
slidev export --output my-slides.pdf
```

### Format Command

Format Markdown files according to Slidev's formatting rules and conventions.

```bash { .api }
slidev format [entry..]

# No additional options - formats in place
```

**Usage Examples:**

```bash
# Format single file
slidev format slides.md

# Format multiple files
slidev format presentation1.md presentation2.md notes.md
```

### Theme Commands

Theme management operations for ejecting and customizing themes.

```bash { .api }
slidev theme eject
slidev theme eject [options]

# Options:
--dir <string>               # Output directory for ejected theme (default: theme)
--theme, -t <string>         # Override theme to eject
```

**Usage Examples:**

```bash
# Eject current theme to ./theme directory
slidev theme eject

# Eject to custom directory
slidev theme eject --dir my-custom-theme

# Eject specific theme
slidev theme eject --theme @slidev/theme-default
```

### Export Notes Command

Export slide notes to PDF format for speaker reference.

```bash { .api }
slidev export-notes [entry..]
slidev export-notes [entry..] [options]

# Options:
--output <string>            # Path to output file
--timeout <number>           # Timeout for rendering (default: 30000)
--wait <number>              # Wait time in ms before exporting (default: 0)
```

**Usage Examples:**

```bash
# Export notes for single presentation
slidev export-notes slides.md

# Export notes with custom output name
slidev export-notes --output speaker-notes.pdf

# Export notes for multiple presentations
slidev export-notes presentation1.md presentation2.md
```

## Common Options

### Entry File Specification

All commands accept entry file arguments with the following behavior:

- **Default**: `slides.md` if no entry specified
- **Multiple entries**: Supported by `build`, `export`, `format`, and `export-notes` commands
- **Auto-extension**: `.md` extension added automatically if missing
- **Creation prompt**: Offers to create file from template if it doesn't exist

### Theme Override

The `--theme` or `-t` option allows overriding the theme specified in the presentation's frontmatter:

```bash
slidev --theme @slidev/theme-seriph
slidev build --theme ./my-local-theme
```

### Remote Control Features

When using `--remote` option:

- Generates QR code for mobile device access
- Provides network interface URLs for remote access
- Supports `--tunnel` for internet access via Cloudflare
- Requires `--bind` to specify listening interfaces in remote mode

### Development Server Shortcuts

When running the development server, keyboard shortcuts are available:

- `r` - Restart server
- `o` - Open in browser
- `e` - Edit presentation (opens in VS Code)
- `q` - Quit server
- `c` - Show QR code (when remote enabled)

## Error Cases

**Common error scenarios:**

- **Missing entry file**: Prompts to create from template
- **Invalid theme**: Shows available themes or installation instructions
- **Port conflicts**: Automatically finds available port in range 3030-4000
- **Export failures**: Usually indicates missing Playwright dependencies
- **Build failures**: Often related to theme or asset resolution issues

**Troubleshooting commands:**

```bash
# Check dependencies
npm ls @slidev/cli

# Force cache clear
slidev --force

# Enable debug logging
slidev --log info
```