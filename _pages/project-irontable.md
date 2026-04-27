---
permalink: /projects/irontable/
title: "Project Iron Table"
---

**Engine:** Unreal Engine 5.7 &nbsp;|&nbsp; **Language:** C++ &nbsp;|&nbsp; **Status:** Active

[View on GitHub](https://github.com/AbrahamElfenbaum/ProjectIronTable){: .btn .btn--primary}

---

## Overview

Project Iron Table is a TTRPG (tabletop RPG) simulator built from scratch in Unreal Engine 5.7 using C++. The goal is to replicate the core experience of a physical tabletop session — rolling dice, managing a shared map, and communicating with other players — in a real-time 3D environment.

The project is built incrementally, starting with a fully-featured dice system and expanding outward into UI, chat, and session management.

---

## Features

### Physics-Based Dice Rolling
Dice are fully simulated using Unreal's physics engine. Each die type (D4, D20, D100, etc.) is its own actor with configurable mass, damping, impulse range, and physical material. A failsafe timer destroys dice that fail to settle, and a delegate broadcasts the final result when all dice come to rest.

### Dice Selector UI
A widget-based UI lets players choose die type and count, then roll with a single button. All logic lives in C++; Blueprint handles only layout and styling.

### Chat System
A tabbed chat widget supports multiple channels — including a persistent Server channel and closeable private channels. Features include:
- Auto-routing replies to the active private channel
- Notification indicators on tabs with unread messages
- A collapsible closed-channel panel for reopening conversations
- Incoming messages automatically reopen closed channels
- Right-click context menu on tabs for inline rename and close, with rename persisted across sessions
- Full chat log persistence — all messages and tab names are restored on session reload

### Draggable Panels & HUD
The gameplay HUD is component-driven and spawned on local clients only. Each panel (dice, chat, players) is draggable and resizable within the viewport. A taskbar manages panel visibility toggling.

### Camera Settings with Save/Load
Camera behavior (speed, pitch limits, zoom range) is configurable via a settings UI built with paired sliders and editable text fields. Settings persist between sessions using Unreal's `USaveGame` system.

### Custom Rich-Text Editor (In Progress)
A WYSIWYG rich-text notes editor built as a native Slate widget — no browser, no external libraries. Key architectural decisions:
- **Run-length document model** — text stored as an ordered list of `FRichTextRun` structs, each holding a character range and its formatting flags (bold, italic, underline, strikethrough)
- **Format-aware insertion** — typing at the boundary between two differently-formatted runs splits the run into three pieces (Left, Middle, Right), with the Middle inheriting `ActiveFormat`
- **Cursor-driven format sync** — `SyncActiveFormat` updates the toolbar checkboxes whenever the cursor moves, so the UI always reflects the format under the cursor
- **Per-run visual rendering** — `OnPaint` iterates runs, swapping `FSlateFontInfo::TypefaceFontName` for bold/italic variants; underline and strikethrough drawn as line primitives via `FSlateDrawElement::MakeLines`; trailing whitespace trimmed before measuring decoration width
- Rendering handled manually via `FSlateDrawElement::MakeText` and `MakeLines`; tab stops and multi-line layout computed by walking runs and measuring with `FSlateFontMeasure`

---

## Technical Highlights

- All gameplay systems written in C++ with Blueprint used only for visual layout
- Modular folder structure with cross-folder includes managed via `Build.cs`
- Server RPCs handle chat routing; private messages are directed per-recipient
- Input mode management ensures UI widgets receive mouse events without blocking gameplay input
- Custom Slate rich-text editor built from scratch: run-length document model, format-aware run-splitting, per-run visual rendering (bold/italic/underline/strikethrough), DPI-aware text measurement
