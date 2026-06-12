# pdf-ingest — an agent skill for offline PDF conversion

An [agent skill](https://agentskills.io) that routes PDFs to the right local converter before an LLM reads them: probe the text layer, then choose between [markitdown](https://github.com/microsoft/MarkItDown) (instant text-layer extraction), [Docling](https://github.com/docling-project/docling) + Granite-Docling (default quality engine, MLX-accelerated on Apple Silicon), [marker](https://github.com/datalab-to/marker) (maths → LaTeX, scans), and [MinerU](https://github.com/opendatalab/MinerU) (accuracy-maximalist escalation) — with token- and memory-budget discipline for hosts that are simultaneously running a local model.

- `SKILL.md` — the routing policy and budget disciplines
- `engines/*.md` — per-engine commands and known failure modes, loaded on demand

Design rationale and the empirical legwork behind the recipes: [danmackinlay.name/notebook/pdf_ingestion](https://danmackinlay.name/notebook/pdf_ingestion.html).

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
