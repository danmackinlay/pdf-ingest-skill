# pdf-ingest — an agent skill for offline PDF conversion on Apple Silicon

An [agent skill](https://agentskills.io) for agents that must read PDFs **without cloud help**: no vision API to lean on, often no network at all, and a local chat model already occupying most of the machine's memory.

It is deliberately specialised, on two axes:

- **Offline-first.** Every engine runs fully locally. The skill assumes the host agent is backed by a local model (Osaurus, Ollama, mlx_lm, ds4), so it treats PDF conversion as a fallback discipline — probe cheaply, convert once to a file, read selectively — rather than assuming an unlimited-context cloud model that can swallow raw PDFs.
- **Apple Silicon-biased.** Engine choice and configuration favour Macs: Docling auto-selects its MLX engine for Granite-Docling, marker runs on MPS, MinerU's MLX-ecosystem path is preferred, and the memory-budget rules are written for unified memory shared between the converter and a resident multi-gigabyte chat model. Everything still works on Linux/CUDA; it just isn't what the recipes are tuned for.

What the skill actually does: route each PDF to the cheapest adequate local converter. Probe the text layer, then choose between [markitdown](https://github.com/microsoft/MarkItDown) (instant text-layer extraction), [Docling](https://github.com/docling-project/docling) + Granite-Docling (default quality engine), [marker](https://github.com/datalab-to/marker) (maths → LaTeX, scans), and [MinerU](https://github.com/opendatalab/MinerU) (accuracy-maximalist escalation) — with token- and memory-budget discipline throughout.

- `SKILL.md` — the routing policy and budget disciplines
- `engines/*.md` — per-engine commands and known failure modes, loaded on demand

Design rationale and the empirical legwork behind the recipes: [danmackinlay.name/notebook/pdf_ingestion](https://danmackinlay.name/notebook/pdf_ingestion.html).

## What it is not

Not a general document-AI toolkit, and not the right tool when the host model reads PDFs natively (a vision-capable cloud model answering a quick question about a short PDF should just read it — the skill says so in step 0). It earns its keep on batch conversion, corpus building, long documents, and text-only local models.

## Install

**Claude Code** — clone (or submodule) into a skills directory:

```bash
git clone https://github.com/danmackinlay/pdf-ingest-skill ~/.claude/skills/pdf-ingest
```

**Hermes Agent**:

```bash
git clone https://github.com/danmackinlay/pdf-ingest-skill ~/.hermes/skills/pdf-ingest
```

**pi**:

```bash
pi install git:github.com/danmackinlay/pdf-ingest-skill
```

The converters themselves install separately (each engine file states its one-liner; all are `uv tool install` / `uvx` style).

## Provenance

This repo is published via `git subtree` from a private monorepo where the skill is actively edited and tested. Issues and PRs are welcome here and will be subtree-pulled back.

MIT licensed.
