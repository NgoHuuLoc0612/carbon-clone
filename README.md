# Carbon Clone — Code Screenshot Tool

A fully-featured, production-quality clone of [carbon.now.sh](https://carbon.now.sh) built with:

- **Vite + TypeScript + React** — Fast HMR dev experience
- **Monaco Editor** — Full LSP-powered editing with syntax validation
- **Shiki** — 30+ themes with pixel-perfect syntax highlighting via TextMate grammars
- **Tree-sitter (WASM)** — Real AST parsing for Cyclomatic Complexity, symbol extraction, and error detection
- **html-to-image** — Client-side PNG/JPEG/WebP/SVG export
- **resvg-wasm** — High-fidelity SVG→PNG conversion in the browser
- **FFmpeg.wasm** — Animated GIF and MP4 export with typing/fade/slide animations
- **Puppeteer** — Server-side high-fidelity screenshot via headless Chromium
- **Google Fonts API** — Full monospace font library with live preview
- **Zustand** — Persistent state with localStorage sync

---

## Quick Start

```bash
# Install all dependencies
npm install

# Start Vite dev server only (no Puppeteer server)
npm run dev

# Start both Vite + Puppeteer server (for server-side screenshots)
npm run dev:full
```

Open **http://localhost:5173**

---

## Architecture

```
carbon-clone/
├── src/
│   ├── types/          # Shared TypeScript interfaces
│   ├── store/          # Zustand state management
│   │   └── editorStore.ts
│   ├── hooks/
│   │   ├── useShiki.ts        # Shiki highlighter with dynamic theme/lang loading
│   │   ├── useTreeSitter.ts   # Web Tree-sitter for AST analysis
│   │   ├── useFFmpeg.ts       # FFmpeg.wasm for video/GIF generation
│   │   └── useGoogleFonts.ts  # Google Fonts API + font injection
│   ├── utils/
│   │   └── export.ts   # Export pipeline (html-to-image, resvg, puppeteer)
│   └── components/
│       ├── Editor/
│       │   └── CodeEditor.tsx      # Monaco Editor with custom themes
│       ├── Preview/
│       │   └── CodeWindow.tsx      # The rendered code image with all decorations
│       ├── Controls/
│       │   ├── ControlsPanel.tsx   # Background/shadow/window/font/theme controls
│       │   └── PresetsPanel.tsx    # 8 built-in visual presets
│       ├── Export/
│       │   └── ExportPanel.tsx     # Format/quality/animation/method selection
│       └── UI/
│           ├── Toolbar.tsx
│           └── LanguageSelector.tsx
├── server.ts           # Express + Puppeteer screenshot API
├── vite.config.ts      # COOP/COEP headers for SharedArrayBuffer
└── public/wasm/        # Tree-sitter WASM grammars (download separately)
```

---

## Tree-sitter WASM Setup

Tree-sitter language grammars need to be downloaded separately:

```bash
# Create WASM directory
mkdir -p public/wasm

# Download main WASM
curl -L https://github.com/tree-sitter/tree-sitter/releases/download/v0.22.6/tree-sitter.wasm \
  -o public/wasm/tree-sitter.wasm

# Download language grammars (examples)
# JavaScript
curl -L https://github.com/tree-sitter/tree-sitter-javascript/releases/download/v0.21.3/tree-sitter-javascript.wasm \
  -o public/wasm/tree-sitter-javascript.wasm

# TypeScript
curl -L https://github.com/tree-sitter/tree-sitter-typescript/releases/download/v0.21.2/tree-sitter-typescript.wasm \
  -o public/wasm/tree-sitter-typescript.wasm

# Python
curl -L https://github.com/tree-sitter/tree-sitter-python/releases/download/v0.21.0/tree-sitter-python.wasm \
  -o public/wasm/tree-sitter-python.wasm
```

The app gracefully degrades if WASM files are not present.

---

## Export Methods

| Method | Quality | Speed | Transparency | Notes |
|--------|---------|-------|--------------|-------|
| html-to-image | ★★★★ | Fast | ✓ PNG | Client-side, no server required |
| resvg-wasm | ★★★★★ | Medium | ✓ PNG | SVG→PNG, best font rendering |
| Puppeteer | ★★★★★ | Slow | ✓ PNG | Requires `npm run server`, most accurate |

---

## Video/Animation Export

1. Click **Export** → select **MP4** or **GIF**
2. Choose animation type (Typing, Fade, Slide, Highlight Scan)
3. Configure duration and FPS
4. Click "Load FFmpeg" when prompted (downloads ~30MB WASM)
5. Click Download

The typing animation renders each keystroke as a frame for a live-coding effect.

---

## Google Fonts API

Add your API key to `.env.local`:
```
VITE_GOOGLE_FONTS_API_KEY=your_key_here
```

Without a key, the app uses a curated list of 20 monospace fonts from Google Fonts.

---

## Environment Variables

```env
VITE_GOOGLE_FONTS_API_KEY=  # Optional: enables full Google Fonts catalog
```

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl/⌘ + E` | Open Export panel |
| `Ctrl/⌘ + Space` | Trigger Monaco autocomplete |
| `F1` | Monaco command palette |