---
name: pdf-ingest
description: Convert PDFs and office documents into machine-readable text or Markdown. Use for batch conversion, corpus building, long documents (convert once, read selectively), when output files are required, or when the host model cannot ingest PDFs natively. If the host reads PDFs natively (vision-capable, e.g. Claude's Read tool), prefer native reading for short interactive questions. Routes between markitdown, Docling/Granite, marker, and MinerU by document type, with token- and memory-budget discipline.
---

# PDF ingestion

Convert the document to Markdown with the right engine, then read the conversion selectively. Never feed a raw PDF or a whole large conversion into context.

## Routing

0. **Check native ingestion first.** If the host agent reads PDFs natively (vision-capable model with a page-range PDF tool), use that for short documents and interactive Q&A — highest fidelity, zero setup. Use this skill instead when: output *files* are required; the document is long enough that convert-once-read-selectively beats repeated native page reads; the job is batch/corpus conversion; or the host model is text-only.
1. **Probe the text layer first.** Run `uvx --from 'markitdown[pdf]' markitdown <file>.pdf | head -c 2000`. If real prose comes back, the PDF is born-digital; if near-empty or garbage, it is a scan and needs an OCR-capable engine (step 4).
2. **Born-digital + plain prose + simple question** → use the markitdown output directly. Details: `engines/markitdown.md`.
3. **Default for quality conversion** (structure, tables, RAG-bound output, anything multi-page that will be re-read): Docling + Granite. Smallest memory footprint of the neural options (258M model), fast on Apple Silicon. NOT for equation transcription — see step 4. Details: `engines/docling.md`.
4. **Mathematics that must render or be trusted** (LaTeX in the output, equation-heavy papers) or **scanned input**: marker or MinerU. Both measured corruption-free on equation-dense papers, while Docling silently substitutes tokens inside plausible LaTeX (measured: log(1/δ) → log(1/8)). marker is lighter and faster for one-offs; MinerU amortises a ~4 min model load over batches and preserves equation numbers. Details: `engines/marker.md`, `engines/mineru.md`.
5. **Escalation** for the remaining hard cases (complex layout, handwriting, exotic scripts): MinerU with `--effort high`. Details: `engines/mineru.md`.

Read the matching engine file before invoking; each contains exact commands and known failure modes.

## Budget disciplines (always apply)

- **Files, not context.** Write the conversion next to the source file (or to the workspace), then read it selectively — table of contents, targeted sections, grep. Do not cat a whole conversion into context.
- **No embedded images in LLM-bound output.** Base64 data URLs can be >80% of a converted file. Docling: `--image-export-mode placeholder`. If figures matter, use `referenced` and handle the image files separately.
- **Memory.** A large chat model is probably resident on this machine. Prefer Docling (sub-GB) by default; marker adds 3-5 GB (fine singly; do not run parallel marker workers beside a large model); MinerU is the heaviest.
- **LLM refinement calls go to the host's own endpoint.** While this skill's commands run, the host model is idle (the agent loop waits on the tool), so a converter that wants LLM help (marker `--use_llm`) should call the same local OpenAI-compatible endpoint that serves the agent, not a second backend. Endpoint and model name: ask the user or check the obvious local ports (Osaurus 1337, Ollama 11434, mlx_lm/ds4 8000).
