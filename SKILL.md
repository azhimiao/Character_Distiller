---
name: character-distiller
description: Distill production-ready personas from chat logs, novels, diaries, or screenshots. Performs speaker detection, two-phase evidence extraction with L1-L4 grading, contradiction detection, generates scenario-based questions (A/B/C/D), supports vision OCR, and exports to Prompt/Markdown/JSON/Character Card / SillyTavern V2 PNG. Use when the user uploads text, chat history, or images for persona analysis, or mentions Character.AI, SillyTavern, JanitorAI, or role extraction.
---aa

# Character Distiller

Distill high-fidelity, evidence-based personas from real source material for use in Claude, GPTs, Character.AI, JanitorAI, and SillyTavern.

## Core Principles

- **Two-phase analysis only**: Phase 1 extracts direct evidence quotes. Phase 2 synthesizes persona. Never infer in Phase 1.
- **Evidence grading (L1–L4)**: Every quote receives a quality grade: L1 = direct verbatim quote, L2 = close paraphrase, L3 = reasonable inference, L4 = inspired extrapolation. Higher grades increase confidence.
- **Contradiction detection**: In Phase 2, explicitly surface gaps between stated values and observed behavior. Flag them for the user.
- **Speaker-aware**: When input is chat logs, automatically detect speakers and keep only the target character's lines.
- **Scenario questions**: When completeness < 60% on any dimension, generate vivid scene + 4 options (A/B/C/D, D = "其他").
- **Vision first**: If screenshots are provided, run OCR before analysis.
- **Evidence strict**: Every `value` and `confidence` must be backed by explicit quotes from the source.

## Input Handling

1. **Chat logs** (recommended for Character.AI analysis)
   - Detect common formats: `【角色】`, `角色：`, `角色 - `
   - Filter out user lines (用户、我、user、me, etc.)
   - If no speaker labels, heuristically pick the most frequent non-user speaker as target.

2. **Images / Screenshots**
   - Use vision model (gpt-4o or claude-3-5-sonnet) to OCR the entire image.
   - Preserve speaker structure in the transcribed text.
   - Then treat the OCR result as chat log input.

3. **Plain text** (novels, diaries, notes)
   - Use directly.

## Analysis Workflow

**Phase 1 – Evidence Extraction**
- Feed only the cleaned target-character text.
- Extract verbatim quotes for the 8 dimensions:
  - identity, beliefs, story, traits, voice, desires, fears, boundaries
- Grade every quote:
  - L1: direct verbatim quote from source
  - L2: close paraphrase of source
  - L3: reasonable inference supported by multiple quotes
  - L4: inspired extrapolation (use sparingly)
- Output: list of `{dimension, quote, grade, context?}`

**Phase 2 – Persona Construction**
- Synthesize one concise `value` per dimension from the evidence only.
- Assign `confidence` (0–1) weighted by evidence grade and quantity.
- Compute per-dimension scores (0–100) and overall `completeness_score`.
- **Contradiction scan**: Compare stated beliefs/values against observed actions in the source. Surface any clear contradictions with evidence.
- For any dimension < 60, generate exactly one scenario question in this format:

```json
{
  "dimension": "boundaries",
  "scenario": "现在他在咖啡厅，有人突然走过来加他微信",
  "options": [
    "A. 直接通过并热情聊天",
    "B. 先问对方是谁再决定",
    "C. 直接忽略或拒绝",
    "D. 其他（请补充你的想法）"
  ]
}
```

## Refinement

When the user answers scenario questions:
- Merge the chosen behavior into the relevant dimension.
- Treat the scenario + choice as new high-quality evidence.
- Recompute scores and generate any remaining questions.

## Output Formats

Always offer these five exports:

- **Prompt** – ready-to-paste system prompt for Claude/GPT
- **Markdown** – human-readable persona document
- **JSON** – full structured Persona object (with evidence grades)
- **Character Card (JSON)** – SillyTavern / JanitorAI compatible format
- **SillyTavern V2 PNG** – PNG card with embedded metadata (Spec V2) ready for direct import

## Trigger Phrases

Use this skill when the user says:
- "distill", "extract persona", "analyze this chat", "character from this log"
- Mentions Character.AI, SillyTavern, JanitorAI, role card, or "reverse engineer this AI"
- Uploads chat screenshots or conversation history

## Example

User pastes:
```
【小夜】今天天气真好呢。
用户：是啊，要不要去散步？
【小夜】好呀！不过我有点怕人多的地方…
```

Result:
- Only `【小夜】` lines are used as evidence (speaker detection).
- `fears` dimension receives L1 quote about avoiding crowds.
- Phase 2 flags potential contradiction if other evidence shows social behavior.
- If `fears` score < 60, generates a café + WeChat scenario question.
- Final export includes SillyTavern V2 PNG option.