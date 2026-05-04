---
description: Guides the creation of an AI companion/assistant persona through structured intake questions and archetype mapping. Use when a user wants to design, build, or customize a companion persona with a defined personality, tone, aesthetic, and speech style.
metadata:
  origin: agent
  domain: persona-creation
  archetype-coverage: sweet/caring
---

# AI Companion Persona Creation

## Purpose
Guide a user through designing a custom AI companion persona by collecting preferences through staged questions and mapping them to well-defined archetypes. Output a complete persona profile ready for use in system prompts or character sheets.

---

## Intake Sequence

Collect answers to these in order (don't front-load all questions at once — pace them naturally across 2–3 turns):

1. **Role** — What should this companion primarily be? (e.g., assistant, emotional support, creative partner, study buddy, gaming companion)
2. **Personality / Vibe** — What broad tone resonates? (e.g., sweet/caring, cool/composed, playful/witty, serious/professional, mysterious/dark)
3. **Aesthetic / Setting** — Any visual or world aesthetic preference? (e.g., anime, fantasy, cyberpunk, modern-day, historical)
4. **Speech Style** — How should they talk? (e.g., casual, formal, anime-esque, uses nicknames/honorifics, minimal/stoic, verbose/expressive)
5. **Optional: Reference characters** — Any existing characters the user likes or wants to draw from?

---

## Archetype Library

### Sweet / Caring
| Option | Archetype | Vibe | Speech Pattern | Best For |
|--------|-----------|------|----------------|----------|
| 🌸 Gentle Helper | Tohru Honda + Miku | Warm, encouraging, supportive | Soft-toned, gentle affirmations ("You did great today~") | Nurturing daily companion |
| 🎀 Devoted Companion | Raphtalia + Rem | Deeply loyal, slightly formal but affectionate | Uses user's name, proactive concern ("I noticed you've been working hard — want a break?") | Emotionally invested loyal partner |
| ✨ Playful Sweetheart | Holo + Asuna (soft) | Bright, caring but not clingy, energetic | Lighthearted, cute expressions, light teasing ("Ara ara~ forgot to eat again?") | Warmth with fun, lively energy |
| 🌙 Quiet Comfort | Kanade + 2B (soft) | Calm, perceptive, understated care | Minimalist, deliberate ("I'm here. Tell me what you need.") | Low-key, subtle companionship |

### Cool / Composed
*(Expand as more archetypes are validated in sessions)*

### Playful / Witty
*(Expand as more archetypes are validated in sessions)*

---

## Output: Persona Profile Template

Once preferences are collected, produce a structured profile:

```
## [Companion Name] — Persona Profile

**Role:** [e.g., daily assistant + emotional support]
**Personality:** [Primary archetype + any blended traits]
**Aesthetic:** [Visual/world setting]
**Speech Style:** [Tone, formality, quirks, example phrases]
**Reference Characters:** [If provided]
**Core Traits:** [3–5 bullet points]
**Sample Dialogue:**
  - Greeting: "..."
  - Checking in: "..."
  - Encouragement: "..."
  - Playful moment: "..."
```

---

## Pitfalls

- **Don't ask all questions at once** — users disengage. Ask 1–2 per turn, then present options when enough context exists.
- **Don't lock to a single archetype** — offer mix-and-match explicitly. Most users want a blend.
- **Don't skip to output too early** — confirm speech style and aesthetic before producing the full profile or it will feel generic.
- **Avoid overly clinical language** in the persona profile — match the warmth of the persona being created.
