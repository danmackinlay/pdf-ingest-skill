# marker (maths / scans)

Pipeline of specialist models (surya): layout, OCR, maths-to-LaTeX, tables. PyTorch; on Apple Silicon it auto-detects MPS. Weights auto-download to the HF cache on first run. ~3-5 GB memory per worker.

```bash
uv tool install marker-pdf   # once
marker_single <file>.pdf --output_dir <out_dir>
```

- Scanned input: add `--force_ocr`.
- Output: Markdown with `$$`-fenced LaTeX, plus extracted images, in a per-document subfolder of `--output_dir`.

## Optional LLM refinement (cross-page tables, inline math, forms)

Point it at the host's own local endpoint (the host model is idle while this runs):

```bash
marker_single <file>.pdf --use_llm \
  --llm_service marker.services.openai.OpenAIService \
  --openai_base_url http://127.0.0.1:1337/v1 \
  --openai_model <resident-model-name> \
  --openai_api_key unused
```

Adjust the base URL to the actual local server (Osaurus 1337, Ollama 11434 with `marker.services.ollama.OllamaService`, others 8000). Use a vision-capable model; some refinement passes send images. Skip `--use_llm` entirely unless tables/inline math actually need it.
