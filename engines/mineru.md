# MinerU (escalation)

The accuracy-maximalist document parser: formulas to LaTeX, tables to HTML, 109-language OCR, own compact document VLM. Heavier than the alternatives (wants 16+ GB RAM, ~20 GB disk for models). Use when Docling and marker both disappoint on a hard document.

```bash
uv pip install -U "mineru[all]"   # once
mineru -p <file>.pdf -o <out_dir>
```

- On constrained machines or pure CPU: `mineru -p <file>.pdf -o <out_dir> -b pipeline` (lower accuracy, lower footprint).
- Output: Markdown + JSON per document under `<out_dir>`.
