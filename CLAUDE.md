# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Slidev** presentation project for ".NET 10 Refactor Note". Slidev is a web-based slide deck framework designed for developers, allowing presentations to be written in Markdown with embedded Vue components, code highlighting, and interactive elements.

## Development Commands

- **Start dev server**: `pnpm dev` - Opens presentation at http://localhost:3030 with hot reload
- **Build for production**: `pnpm build` - Creates static site in `dist/` directory
- **Export slides**: `pnpm export` - Exports slides to PDF or other formats

## Architecture & Structure

### Core Files

- **slides.md**: Main presentation content - all slides are defined here using Markdown with frontmatter
- **components/**: Custom Vue components that can be used within slides (e.g., `Counter.vue`)
- **pages/**: Additional slide pages that can be imported into main slides using `src` attribute
- **snippets/**: External TypeScript/JavaScript code snippets that can be embedded in slides using `<<< @/snippets/file.ts#region` syntax

### Slide Organization

Slides use YAML frontmatter for configuration (theme, layout, transitions, etc.) and are separated by `---`. The presentation supports:
- Multiple themes (currently using `seriph`)
- Various layouts (e.g., `two-cols`, `image-right`, `center`)
- Slide transitions and animations
- Click animations with `v-click` directive
- Code highlighting with language-specific syntax and line highlighting
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
export function example() { }
// #endregion snippet
```
Then embedded with: `<<< @/snippets/external.ts#snippet`

## Deployment

Project is configured for deployment to both Netlify and Vercel:
- Build output: `dist/` directory
- Build command: `npm run build` (uses pnpm internally)
- Node version: 20
- SPA routing handled via redirects/rewrites to `index.html`

## Editing Slides

When modifying slides:
1. Edit `slides.md` for main content
2. Use existing layouts and themes before creating custom ones
3. Add custom components to `components/` only when built-in components are insufficient
4. Place reusable code snippets in `snippets/` for cleaner slide content
5. Split large presentations into separate files in `pages/` and import them

The dev server provides instant feedback - changes to `slides.md` are reflected immediately in the browser.

---

# Slidev Documentation Reference

> Presentation slides for developers

For comprehensive Slidev documentation, refer to the official docs at https://sli.dev/

## Table of Contents

### Guide

- [Why Slidev](https://sli.dev/guide/why.html)
- [Getting Started](https://sli.dev/guide.html)
- [Syntax Guide](https://sli.dev/guide/syntax.html)
- [User Interface](https://sli.dev/guide/ui.html)
- [Animation](https://sli.dev/guide/animations.html)
- [Theme and Addons](https://sli.dev/guide/theme-addon.html)
- [Components in Slides](https://sli.dev/guide/component.html)
- [Slide Layout](https://sli.dev/guide/layout.html)
- [Exporting](https://sli.dev/guide/exporting.html)
- [Building and Hosting](https://sli.dev/guide/hosting.html)
- [FAQ](https://sli.dev/guide/faq.html)

### Advanced

- [Global Context](https://sli.dev/guide/global-context.html)
- [Writing Layouts](https://sli.dev/guide/write-layout.html)
- [Writing Themes](https://sli.dev/guide/write-theme.html)
- [Writing Addons](https://sli.dev/guide/write-addon.html)

### Customizations

- [Customizations](https://sli.dev/custom.html)
- [Directory Structure](https://sli.dev/custom/directory-structure.html)
- [Configure Highlighter](https://sli.dev/custom/config-highlighter.html)
- [Configure Vite and Plugins](https://sli.dev/custom/config-vite.html)
- [Configure Vue App](https://sli.dev/custom/config-vue.html)
- [Configure UnoCSS](https://sli.dev/custom/config-unocss.html)
- [Configure Code Runners](https://sli.dev/custom/config-code-runners.html)
- [Configure Transformers](https://sli.dev/custom/config-transformers.html)
- [Configure Monaco](https://sli.dev/custom/config-monaco.html)
- [Configure KaTeX](https://sli.dev/custom/config-katex.html)
- [Configure Mermaid](https://sli.dev/custom/config-mermaid.html)
- [Configure Routes](https://sli.dev/custom/config-routes.html)
- [Configure Shortcuts](https://sli.dev/custom/config-shortcuts.html)
- [Configure Context Menu](https://sli.dev/custom/config-context-menu.html)
- [Configure Fonts](https://sli.dev/custom/config-fonts.html)
- [Configure Pre-Parser](https://sli.dev/custom/config-parser.html)

### Built-in

- [Slidev CLI](https://sli.dev/builtin/cli.html)
- [Components](https://sli.dev/builtin/components.html)
- [Layouts](https://sli.dev/builtin/layouts.html)

### Resources

- [Showcases](https://sli.dev/resources/showcases.html)
- [Theme Gallery](https://sli.dev/resources/theme-gallery.html)
- [Addon Gallery](https://sli.dev/resources/addon-gallery.html)
- [Learning Resources](https://sli.dev/resources/learning.html)
- [Curated Covers](https://sli.dev/resources/covers.html)

### Features

- [Block Frontmatter](https://sli.dev/features/block-frontmatter.html): Use a YAML code block as the frontmatter
- [Bundle Remote Assets](https://sli.dev/features/bundle-remote-assets.html): Download and bundle remote assets when building your slides
- [Click Markers](https://sli.dev/features/click-marker.html): Highlighting notes and auto-scrolling to the active section of notes
- [Code Groups](https://sli.dev/features/code-groups.html): Group multiple code blocks and automatically match icon by the title name
- [Draggable Elements](https://sli.dev/features/draggable.html): Move, resize, and rotate elements by dragging them with the mouse
- [Drawing & Annotations](https://sli.dev/features/drawing.html): Draw and annotate your slides with ease
- [Eject Theme](https://sli.dev/features/eject-theme.html): Eject the installed theme from your project to customize it
- [Features](https://sli.dev/features.html)
- [Frontmatter Merging](https://sli.dev/features/frontmatter-merging.html): Merge frontmatter from multiple markdown files
- [Generate PDF when Building](https://sli.dev/features/build-with-pdf.html): Generate a downloadable PDF along with your slides build
- [Global Layers](https://sli.dev/features/global-layers.html): Create custom components that persist across slides
- [Icons](https://sli.dev/features/icons.html): Use icons from virtually all open-source icon sets directly in your markdown
- [Import Code Snippets](https://sli.dev/features/import-snippet.html): Import code snippets from existing files into your slides
- [Importing Slides](https://sli.dev/features/importing-slides.html): Split your `slides.md` into multiple files for better reusability and organization
- [Integrated Editor](https://sli.dev/features/side-editor.html): Edit your slides source file alongside the presentation
- [LaTeX](https://sli.dev/features/latex.html): Slidev comes with LaTeX support out-of-box, powered by KaTeX
- [Line Highlighting](https://sli.dev/features/line-highlighting.html): Highlight specific lines in code blocks based on clicks
- [Line Numbers](https://sli.dev/features/code-block-line-numbers.html): Enable line numbering for all code blocks across the slides or individually
- [Max Height](https://sli.dev/features/code-block-max-height.html): Set a maximum height for a code block and enable scrolling
- [MDC Syntax](https://sli.dev/features/mdc.html): A powerful syntax to enhance your markdown content with components and styles
- [Mermaid Diagrams](https://sli.dev/features/mermaid.html): Create diagrams/graphs from textual descriptions, powered by Mermaid
- [Monaco Editor](https://sli.dev/features/monaco-editor.html): Turn code blocks into fully-featured editors, or generate a diff between two code blocks
- [Monaco Runner](https://sli.dev/features/monaco-run.html): Run code directly in the editor and see the result
- [Navigation Direction Variants](https://sli.dev/features/direction-variant.html): Apply different styles and animations based on the navigation direction
- [Notes Auto Ruby](https://sli.dev/features/notes-auto-ruby.html): Automatically add `<ruby>` tags to your notes
- [Open Graph Image](https://sli.dev/features/og-image.html): Set the Open Graph image for your slides
- [PlantUML Diagrams](https://sli.dev/features/plantuml.html): Create diagrams from textual descriptions, powered by PlantUML
- [Presenter Timer](https://sli.dev/features/timer.html): Timer for the presenter mode
- [Prettier Plugin](https://sli.dev/features/prettier-plugin.html): Use the Prettier plugin to format your slides
- [Recording](https://sli.dev/features/recording.html): Record your presentation with the built-in camera view and recording feature
- [Remote Access](https://sli.dev/features/remote-access.html): Access your presentation remotely with Slidev's remote access feature
- [Rough Markers](https://sli.dev/features/rough-marker.html): Integrate Rough Notation to allow marking or highlighting elements in your slides
- [SEO Meta Tags](https://sli.dev/features/seo-meta.html): Configure SEO meta tags for better social media sharing and search engine optimization
- [Shiki Magic Move](https://sli.dev/features/shiki-magic-move.html): Enable granular transition between code changes, similar to Keynote's Magic Move
- [Slide Canvas Size](https://sli.dev/features/canvas-size.html): Set the size for all your slides
- [Slide Hooks](https://sli.dev/features/slide-hook.html): Hooks to manage the slide lifecycle
- [Slide Scope Styles](https://sli.dev/features/slide-scope-style.html): Define styles for only the current slide
- [Slot Sugar for Layouts](https://sli.dev/features/slot-sugar.html): A syntax sugar for named slots in layouts
- [The `Transform` Component](https://sli.dev/features/transform-component.html): A component for scaling some elements
- [TwoSlash Integration](https://sli.dev/features/twoslash.html): A powerful tool for rendering TypeScript code blocks with type information on hover or inlined
- [VS Code Extension](https://sli.dev/features/vscode-extension.html): Help you better organize your slides and have a quick overview of them
- [Writable Monaco Editor](https://sli.dev/features/monaco-write.html): A monaco editor that allows you to write code directly in the slides and save the changes back to the file
- [Zoom Slides](https://sli.dev/features/zoom-slide.html): Zoom the content of a slide to a specific scale
