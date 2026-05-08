# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project shape

TimeNote is a single-file static web app: `public/index.html` contains all markup, CSS, and JS. There is no build step, no package manager, no server, no dependencies, and no test suite. To run or test changes, open `public/index.html` in a browser (or `open public/index.html` on macOS). To verify changes, reload the page and exercise the UI manually. The app is deployed to Cloudflare Pages from the `public/` directory.

## Architecture

State lives entirely client-side in `localStorage` under the key `timenote.v1`. The full state object is rewritten on every mutation via `save()`:

```
{ notes: [ { id, title, createdAt, entries: [ { startedAt, endedAt, text } ] } ], activeId, settings: { includePrompt, promptText } }
```

The whole script is one IIFE in `<script>` at the bottom of `public/index.html`. Rendering is imperative: any state change calls `render()`, which rebuilds the sidebar and main pane from scratch — there is no diffing, no reactive framework. When adding features, follow this pattern (mutate `state`, call `save()`, call `render()` or a sub-renderer) rather than introducing reactive abstractions.

`load()` merges `defaultSettings()` into stored state on read, so older `localStorage` blobs without a `settings` key keep working. New persisted fields should follow the same merge-on-load pattern rather than a versioned migration.

## The core UX invariant

The timestamp on each entry is captured **when the user starts typing**, not when they hit Enter. This is the whole point of the app — meeting notes get timestamped at the moment of thought, not at the moment of commit.

Implementation: a module-scoped `typingStart` is set on the first `input` event when the textarea goes from empty to non-empty, and cleared when the textarea returns to empty or an entry is committed. Any change to the composer must preserve this behavior. The Markdown export format documented in `README.md` is the user-facing contract — keep `exportMarkdown()` aligned with it.

## Conventions

- Browser-only ES (no transpilation). Stick to features that work in modern evergreen browsers without polyfills.
- Use `textContent` rather than `innerHTML` when inserting user-supplied strings (titles, entry text) — the existing code follows this and it's the only XSS protection in the app.
