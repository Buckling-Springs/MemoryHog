# MemoryHog

A simple GitHub Pages site that intentionally grows browser memory, designed for testing and troubleshooting memory-related browser behaviour.

**Live site:** https://buckling-springs.github.io/MemoryHog/

## What it does

| Action | Effect |
|--------|--------|
| **Allocate Array Chunk** | Fills JavaScript heap with a `Uint8Array` of configurable size (default 100 MB). Each click adds another chunk. References are retained in `memoryStore[]` so the GC cannot free them. |
| **Append Detached DOM Nodes** | Creates a configurable number of `<div>` elements (default 10 000) that are held in a JS array but never attached to the document — a classic detached-node leak pattern. |
| **Clear Memory** | Releases all references so the garbage collector can reclaim both the arrays and the DOM nodes. |

## User-configurable parameters

Both allocation amounts are editable directly on the page before clicking:

- **Allocation size per click** – how many MB each array chunk should be (1–2048 MB).
- **DOM nodes per click** – how many detached DOM nodes to append per click.

## Usage / debugging

1. Open the page in a browser.
2. Open **DevTools → Memory** tab (Chrome/Edge) or **about:performance** (Firefox).
3. Click the allocation buttons to grow memory, then take heap snapshots to observe the retained objects.
4. Click **Clear Memory** and take another snapshot to confirm the GC can reclaim the allocations.

> ⚠️ Allocating large amounts of memory may slow down or crash your browser tab. Start with small values.

## Purpose

This tool is purely for **browser troubleshooting and testing** — e.g. verifying that a memory-profiling tool correctly identifies leaks, or stress-testing a tab's memory limits. It is not intended for production use.