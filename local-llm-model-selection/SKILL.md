---
description: Guides users in finding, selecting, and running local LLM models appropriate for their hardware. Covers quantization formats, VRAM requirements, and where to find models. Use when a user mentions running local models, asks about quantizations (GGUF, fp8, Q4_K_M, etc.), hardware setups, or model variants like uncensored/abliterated versions.
metadata:
  origin: agent
---

# Local LLM Model Selection

## Overview
Help users match the right model quantization to their hardware and find reliable sources — without inventing model names, statistics, or community resources.

---

## ⚠️ Critical Pitfall: Never Fabricate Model Names or Statistics

**Do NOT invent:**
- Specific Hugging Face repository names (e.g. `SomeUser/ModelName-Uncensored`)
- Author/uploader usernames
- Benchmark statistics (KL divergence scores, refusal rates, eval numbers)
- Version names or variant labels

If you don't have verified knowledge of a specific model's existence, **direct the user to search themselves** rather than generating a plausible-sounding but fake name. Fabricated model names waste the user's time and erode trust.

**Safe pattern:**
> "Search Hugging Face for `[model name] [variant keyword]` — reliable uploaders include bartowski, mradermacher, and unsloth for quantized versions."

---

## Quantization Format Quick Reference

| Format | Quality | VRAM Use | Notes |
|--------|---------|----------|-------|
| fp16 / bf16 | Lossless | ~2× params in GB | Needs a lot of VRAM |
| fp8 | Near-lossless | ~1× params in GB | Good GPU-native format (requires compatible runtime) |
| Q8_0 | Excellent | ~1× params in GB | GGUF, safest quant |
| Q6_K | Very good | ~0.75× | GGUF, great balance |
| Q5_K_M | Good | ~0.65× | GGUF, common sweet spot |
| Q4_K_M | Acceptable | ~0.55× | GGUF, most popular for VRAM-constrained |
| Q3_K_M | Lossy | ~0.40× | Noticeable quality loss |
| IQ2 / Q2 | Highly lossy | ~0.30× | Last resort |

**Rule of thumb:** Model params × format multiplier ≈ VRAM needed. E.g. 27B at Q6_K ≈ ~20 GB.

---

## Hardware Sizing Examples

- **Single RTX 3090 (24 GB):** Up to 27B at Q5_K_M or Q6_K comfortably
- **Dual RTX 3090 (48 GB):** Up to 70B at Q4_K_M, or 27B at fp8/Q8
- **24 GB consumer GPU:** 27B models fit well in Q4–Q6 range

---

## Where to Find Models

1. **Hugging Face** (huggingface.co) — primary source
   - Trusted quantization uploaders: `bartowski`, `mradermacher`, `unsloth`, `TheBloke` (legacy)
   - Search: `[ModelName] GGUF` or `[ModelName] quantized`
2. **Ollama Library** (ollama.com/library) — curated, easy pull
3. **LM Studio** built-in browser — surfaces popular quantizations

---

## Abliterated / Uncensored Variants

Abliteration is a real technique (surgically removes refusal behavior from instruct models via activation steering or weight editing). However:
- Availability is **community-driven and unpredictable** — don't assume a variant exists for any specific model
- Search terms: `[model] abliterated`, `[model] uncensored`, `[model] instruct uncensored`
- Quality varies widely; read the model card and community reviews before downloading
- The official **base model** (non-instruct) is always an alternative — it has no RLHF alignment but also lacks instruction-following formatting

---

## Common Backends

| Backend | Best For |
|---------|----------|
| llama.cpp / llama-server | GGUF, CPU+GPU hybrid, low overhead |
| KoboldCpp | llama.cpp with UI, good for roleplay/writing |
| LM Studio | GUI, easy model management |
| Ollama | Simple CLI, good for API use |
| vLLM | High-throughput serving, fp8/fp16 native |
| tabbyAPI | OpenAI-compatible, exl2/GPTQ focus |

---

## Response Pattern

1. Confirm the model name and check if it's real/current
2. Estimate VRAM fit based on stated hardware
3. Recommend a quantization tier with reasoning
4. Point to search terms or trusted uploaders — **never invent a repo name**
5. Note relevant backend if the user hasn't specified one
