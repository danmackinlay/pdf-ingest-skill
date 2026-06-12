# markitdown

Text-layer extraction only: instant, no neural networks, no GPU. Fails on scans, drops layout, mangles complex tables and math.

```bash
uvx --from 'markitdown[pdf]' markitdown <file>.pdf -o <file>.md
```

No install step needed (`uvx` fetches on demand). If output is near-empty the PDF has no text layer: go back to the routing step and pick an OCR-capable engine.
