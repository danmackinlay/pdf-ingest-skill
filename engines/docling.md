# Docling + Granite-Docling (default quality engine)

End-to-end document VLM (Granite-Docling-258M). On Apple Silicon the MLX build is auto-selected. First run downloads the checkpoint automatically.

```bash
uv tool install docling   # once
docling --pipeline vlm --vlm-model granite_docling \
  --image-export-mode placeholder \
  --output <out_dir> <file>.pdf
```

Known behaviours (all verified):

- **Silent.** No progress output, nothing on stdout; ~45 s for a typical paper. Do not assume it hung. `-v` shows progress. An interrupted run leaves no output at all.
- **Output location**: `<out_dir>/<input-stem>.md`. Without `--output` it writes to the current directory.
- **`--image-export-mode placeholder` is mandatory for LLM-bound output** — the default embeds figures as base64 (6x file size). `referenced` writes figures as separate files if they are needed.
- Warnings about HF_TOKEN and transformers `use_fast` are noise.
- Other formats: `--to json|yaml|html|html_split_page|text|doctags` (stackable). JSON/YAML is the full structured document (layout, reading order, table cells) for programmatic use.
