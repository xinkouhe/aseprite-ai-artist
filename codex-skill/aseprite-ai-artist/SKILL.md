---
name: aseprite-ai-artist
description: Use when an open Aseprite window is connected to the aseprite-ai-artist MCP and the task involves drawing, editing, reviewing, animating, or exporting pixel art through that MCP.
---

# Aseprite AI Artist

This is a Codex routing skill for the Aseprite AI Artist MCP. The MCP's `skill://pixel-*` resources contain the detailed pixel-art workflows; use them instead of duplicating their craft rules here.

## Before editing

- Call `preflight` first. If it is not ready, stop and report that Aseprite must be open and connected.
- Call `sprite_info` before the first edit. Use the returned sprite id, layer names, frame count, active frame, and palette; never guess them.
- For existing art, inspect it with `look` before changing it. For a broad or stylistic change, state the intended scope first.
- Keep the user's active sprite, layer, and frame unchanged unless the request requires otherwise.

## Choose the workflow

Read the matching MCP resource when it is available:

- `skill://pixel-brief` for an open-ended visual request that needs decisions first.
- `skill://pixel-new` for a new document; `pixel-draw` for the main drawing pass.
- `skill://pixel-fix` for edits to existing art; `pixel-palette` or `pixel-shade` for colour and volume work.
- `skill://pixel-rig` before splitting a character for animation; `pixel-animate` for a cycle.
- `skill://pixel-review` before calling work finished; `pixel-export` for engine-ready files.
- `skill://pixel-tileset` for repeating level art.

Use the relevant `rules://` resource when the workflow points to one. The user's request takes precedence if it conflicts with a workflow instruction.

## Mutation and verification

- Batch related pixel operations into one `draw` call. Keep `paletteLock` enabled unless the user explicitly wants to expand the palette.
- Scope edits with a layer, region, or selection. Do not redraw existing work from scratch unless requested.
- After drawing or recolouring, inspect the result with `look`; use `ascii` for exact pixels and `diff` for animation changes.
- Run `validate` before reporting completion. Report warnings that were intentionally left unresolved.
- Never edit `.aseprite` or exported files directly as a fallback when the MCP is disconnected. That would change a file the user cannot see in the open document and may be overwritten by a later save.
- Export one operation at a time. `export` writes an artifact but does not save the working document; use `sprite_manage` save operations only when saving the source is part of the request.

This skill is a thin Codex integration layer. Keep pixel-art principles and tool schemas in the MCP's maintained resources so other MCP clients continue to share the same workflows.
