---
name: pdf-ingest
description: Convert PDFs and office documents into machine-readable text or Markdown before analysis. Use whenever a task requires reading a PDF's contents. Routes between markitdown, Docling/Granite, marker, and MinerU by document type, with token- and memory-budget discipline for hosts running local LLMs.
---

# PDF ingestion

Convert the document to Markdown with the right engine, then read the conversion selectively. Never feed a raw PDF or a whole large conversion into context.

## Routing

1. **Probe the text layer first.** Run `uvx --from 'markitdown[pdf]' markitdown <file>.pdf | head -c 2000`. If real prose comes back, the PDF is born-digital; if near-empty or garbage, it is a scan and needs an OCR-capable engine (step 4).
2. **Born-digital + plain prose + simple question** → use the markitdown output directly. Details: `engines/markitdown.md`.
3. **Default for quality conversion** (structure, tables, RAG-bound output, anything multi-page that will be re-read): Docling + Granite. Smallest memory footprint of the neural options (258M model), fast on Apple Silicon. Details: `engines/docling.md`.
4. **Mathematics that must render** (LaTeX in the output) or **scanned input**: marker. Details: `engines/marker.md`.
5. **Escalation** when the above produce poor results on a hard document (complex layout, handwriting, exotic scripts): MinerU. Details: `engines/mineru.md`.

Read the matching engine file before invoking; each contains exact commands and known failure modes.

## Budget disciplines (always apply)

- **Files, not context.** Write the conversion next to the source file (or to the workspace), then read it selectively — table of contents, targeted sections, grep. Do not cat a whole conversion into context.
- **No embedded images in LLM-bound output.** Base64 data URLs can be >80% of a converted file. Docling: `--image-export-mode placeholder`. If figures matter, use `referenced` and handle the image files separately.
- **Memory.** A large chat model is probably resident on this machine. Prefer Docling (sub-GB) by default; marker adds 3-5 GB (fine singly; do not run parallel marker workers beside a large model); MinerU is the heaviest.
- **LLM refinement calls go to the host's own endpoint.** While this skill's commands run, the host model is idle (the agent loop waits on the tool), so a converter that wants LLM help (marker `--use_llm`) should call the same local OpenAI-compatible endpoint that serves the agent, not a second backend. Endpoint and model name: ask the user or check the obvious local ports (Osaurus 1337, Ollama 11434, mlx_lm/ds4 8000).
