# AI Coding Guidelines for .NET 10 Refactor Note Slides

## ⚠️ IMPORTANT: Always Reference CLAUDE.md First

**Before making any changes or providing guidance, ALWAYS consult the comprehensive `CLAUDE.md` file in the project root.** This file contains the complete project documentation, detailed architecture information, and extensive Slidev documentation references that are essential for accurate work in this codebase.

## Project Overview

This is a **Slidev** presentation project for ".NET 10 Refactor Note". Slidev is a web-based slide deck framework designed for developers, allowing presentations to be written in Markdown with embedded Vue components, code highlighting, and interactive elements.

## Development Commands

- **Start dev server**: `pnpm dev` - Opens presentation at http://localhost:3030 with hot reload
- **Build for production**: `pnpm build` - Creates static site in `dist/` directory
- **Export slides**: `pnpm export` - Exports slides to PDF, PPTX, PNG options

## Architecture & Structure

### Core Files

- **`slides.md`**: Main presentation content - all slides defined here using Markdown with frontmatter
- **`components/`**: Custom Vue components that can be used within slides (e.g., `Counter.vue`)
- **`pages/`**: Additional slide pages imported into main slides using `src` attribute
- **`snippets/`**: External TypeScript/JavaScript code snippets embedded with `<<< @/snippets/file.ts#region` syntax

### Slide Organization

Slides use YAML frontmatter for configuration (theme, layout, transitions, etc.) and are separated by `---`. The presentation supports:

- Multiple themes (currently using `seriph`)
- Various layouts (e.g., `two-cols`, `image-right`, `center`)
- Slide transitions and animations
- Click animations with `v-click` directive
- Code highlighting with language-specific syntax + line highlighting
- Embedded Vue components and `<script setup>` blocks
- LaTeX math equations, Mermaid diagrams, and PlantUML diagrams
- Monaco editor for interactive code editing

### Component System

Vue components in `components/` are automatically available in slides. They use:

- Vue 3 Composition API (`<script setup>`)
- UnoCSS for styling (attribute-based utility classes)
- TypeScript for type safety

### Code Snippets

External code in `snippets/` can be referenced using region markers:

```ts
// #region snippet
export function example() {}
// #endregion snippet
```

Then embedded with: `<<< @/snippets/external.ts#snippet`

## Available Commands

Reference these existing commands for common tasks:

- **Generate imported page**: Use `.claude/commands/generate_imported_page.md` to create modular slide files in `pages/`
- **Generate Shiki Magic Move**: Use `.claude/commands/generate_shiki_magic_move.md` for code transition animations

## Deployment

Project configured for deployment to both Netlify and Vercel:

- Build output: `dist/` directory
- Build command: `npm run build` (uses pnpm internally)
- Node version: 20
- SPA routing handled via redirects/rewrites to `index.html`

## Editing Guidelines

When modifying slides:

1. Edit `slides.md` for main content
2. Use existing layouts and themes before creating custom ones
3. Add custom components to `components/` only when built-in components insufficient
4. Place reusable code snippets in `snippets/` for cleaner slide content
5. Split large presentations into separate files in `pages/` and import them

The dev server provides instant feedback - changes to `slides.md` are reflected immediately in the browser.

Reference: [Slidev Documentation](https://sli.dev/)</content>
<parameter name="filePath">/Users/weberyangwork/Workspace/slides/dotnet10-refactor-note/.github/copilot-instructions.md
