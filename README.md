# Character Distiller

**Portable Persona Distillation Skill** — Distill production-ready, evidence-based personas from chat logs, novels, diaries, or screenshots.

This repository contains a single, self-contained skill definition for reverse-engineering character personalities (especially from Character.AI, JanitorAI, SillyTavern conversations). It is designed to work across multiple AI coding agents.

## The Skill

The entire skill lives in one file:

```
SKILL.md
```

All logic, instructions, and workflows are self-contained in `SKILL.md`. Any compatible AI agent can load this file to perform persona distillation.

## Key Features

- **Speaker Detection** — Automatically parses chat logs, identifies speakers, and extracts only the target character's lines.
- **Vision OCR** — Paste or upload chat screenshots; the skill runs vision models to transcribe text before analysis.
- **Two-Phase Evidence Extraction** — Phase 1: strict quote extraction. Phase 2: persona synthesis. No hallucination in evidence gathering.
- **Evidence Grading (L1–L4)** — Every quote is graded for quality:
  - L1: Direct verbatim quote
  - L2: Close paraphrase
  - L3: Reasonable inference
  - L4: Inspired extrapolation
- **Contradiction Detection** — Explicitly flags gaps between stated values and observed behavior.
- **Scenario-Based Questions** — When data is insufficient, generates vivid situational questions with A/B/C/D choices (D = "其他").
- **Multiple Export Formats** — Prompt, Markdown, JSON, Character Card (JSON), and **SillyTavern V2 PNG** (embedded metadata, ready for direct import).

## Usage

Invoke the skill when the user provides chat logs, screenshots, or text and asks to:
- Distill / extract a persona or character card
- Analyze a conversation from Character.AI, JanitorAI, or SillyTavern
- Reverse-engineer an AI character's personality
- Generate a ready-to-use role prompt or SillyTavern card

## Installation

Copy `SKILL.md` into the skills directory of your AI agent.

**Common locations:**
- Cursor: `.cursor/skills/character-distiller/SKILL.md`
- Claude Code / other agents: follow the agent's skill loading convention

After placing the file, the agent will be able to use the skill when relevant prompts appear.

## Output Example

Given a chat log like:
```
【小夜】今天天气真好呢。
用户：是啊，要不要去散步？
【小夜】好呀！不过我有点怕人多的地方…
```

The skill produces:
- Cleaned evidence from the target character only
- Graded quotes (L1 for the "怕人多的地方" line)
- Detected contradictions (if any)
- Scenario questions if needed
- Exportable SillyTavern V2 PNG + JSON

## Advantages Over Similar Tools

- Strong speaker detection and isolation from raw chat logs
- Native support for vision/OCR on screenshots
- Evidence grading (L1–L4) and explicit contradiction detection
- Scenario-based follow-up questions (A/B/C/D format)
- Direct export to SillyTavern V2 PNG with embedded metadata

## License

MIT — free to use, modify, and share.

---

**Note**: The `archive/` folder contains historical artifacts from development and is not part of the skill. Only `SKILL.md` and this README are required.
