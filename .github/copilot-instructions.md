# MemoryHog

MemoryHog is a single-page, dependency-free HTML/CSS/JS tool that intentionally grows
browser memory, used for testing and troubleshooting memory-related browser behaviour
(e.g. verifying memory-profiling tools correctly detect leaks). It is deployed as-is via
GitHub Pages at https://buckling-springs.github.io/MemoryHog/.

## Structure

- `index.html` — the entire application: markup, inline `<style>`, and inline `<script>`.
  There is no build step, bundler, package manager, or test suite — all changes are made
  directly to this file and take effect immediately when the page is reloaded.
- No `package.json`, CI workflows, or lint config exist. Validate changes by opening
  `index.html` in a browser and exercising the buttons manually.

## Core mechanics (in `index.html`'s `<script>`)

- `memoryStore` (array of `Uint8Array` chunks) and `detachedNodes` (array of unattached
  `<div>` elements) are module-level variables that intentionally retain references so the
  GC cannot reclaim them — this retention is the entire point of the tool, not a bug to fix.
- **Allocate Array Chunk** pushes a new `Uint8Array` of `getAllocMB()` MB, filled byte-by-byte
  (`.fill(42)`) so the OS actually commits the pages rather than lazily mapping them.
- **Append Detached DOM Nodes** creates `getDOMCount()` `<div>` elements, deliberately never
  appended to `document`, to simulate a detached-DOM-node leak.
- **Clear Memory** resets both arrays to `[]`, allowing the GC to reclaim everything.
- `updateStats()` refreshes the `#stats` panel (chunk count, total MB, DOM node count) and
  must be called after any mutation to `memoryStore`/`detachedNodes`.
- Allocation size (MB) and DOM node count are user-editable via the `#allocSize` and
  `#domCount` number inputs before clicking; `getAllocMB()`/`getDOMCount()` parse and
  fall back to sane defaults (100 MB / 10,000 nodes) on invalid input.

## Conventions

- Keep everything in `index.html` — no separate JS/CSS files or external dependencies unless
  explicitly requested.
- Preserve the intentional memory-retention behavior; do not "fix" the leaks/retention, as
  that defeats the tool's purpose.
- Match the existing inline style (plain ES6, `"use strict"`, no frameworks, sans-serif/
  Bootstrap-like color palette already defined in `<style>`).
