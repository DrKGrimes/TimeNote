# TimeNote

Simple side app for taking notes during a meeting and recording the time of each entry. The exported Markdown can be paired with a meeting transcript and fed to an LLM to produce a hybrid summary that emphasizes your notes.

## Usage

Open `public/index.html` in a browser. No build step, no server required.

- **+ New** creates a new note doc.
- Type in the composer at the bottom — the timestamp captures the moment you started typing.
- **Enter** ends the entry and saves it with that timestamp. **Shift+Enter** inserts a newline within an entry.
- **Export .md** downloads the active note as a Markdown file.
- Notes are persisted in `localStorage`.

## Export format

```markdown
---
title: "Note title"
date: 2026-05-08
start_time: 14:32:15
---

## Notes

- **[14:32:15]** First note text
- **[14:33:02]** Second note
```

`start_time` is the timestamp of the first entry; it is omitted when the note has no entries. When the prompt-prepend setting is enabled, the prompt text appears between the YAML frontmatter and `## Notes`, separated by `---`.
