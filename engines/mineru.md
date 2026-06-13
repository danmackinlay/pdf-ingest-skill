# MinerU (maths co-pick / hard-document escalation)

The accuracy-maximalist document parser: formulas to LaTeX with equation numbers preserved as `\tag{…}`, tables to HTML, 109-language OCR, its own compact document VLM. As of 3.3 the default backend is `hybrid-engine`, and on Apple Silicon it auto-selects the MLX VLM engine (log line: `Using mlx-engine as the inference engine for VLM`). Wants 16+ GB RAM and a multi-GB model download on first run.

```bash
uv tool install "mineru[all]"   # once; models download on first run
mineru -p <file>.pdf -o <out_dir> -l en
```

- Budget a ~4 minute one-off predictor load per process before the first page; a 24-page paper then converts in under a minute. This amortises over batches — prefer MinerU for multi-document maths conversion, marker for one-offs.
- Measured June 2026: zero equation corruption on a proof-heavy ICLR appendix (26/26 log(1/δ) intact, `\tag{…}` numbers preserved) — co-equal with marker on maths quality.
- `--effort high` adds image/chart analysis (slower).
- On constrained machines or pure CPU: `-b pipeline` (lower accuracy, lower footprint).
- Output: Markdown + layout JSON per document under `<out_dir>/<stem>/hybrid_auto/`.
