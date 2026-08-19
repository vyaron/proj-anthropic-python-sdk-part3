# proj-anthropic-python-sdk-part3

Jupyter notebooks working through the Anthropic Python SDK, part 3: extended thinking, multimodal
inputs, citations, prompt caching, built-in tools, and code execution.

Notebooks are numbered in teaching order and are mostly self-contained — each one re-declares its
own client and helper functions, so you can open any single notebook and run it top to bottom.

## Setup

1. Install dependencies:
   ```bash
   pip install anthropic python-dotenv jupyter
   ```
2. Add your API key to `.env`:
   ```
   ANTHROPIC_API_KEY=your-key-here
   ```
3. Launch Jupyter, or open a notebook directly in VS Code:
   ```bash
   jupyter lab
   ```

## Notebooks

### Model capabilities

- **10_extended_thinking.ipynb** — Enabling `thinking` with a token budget, reading `thinking` blocks off the response, and triggering redacted thinking with the magic test string.
- **20_images.ipynb** — Vision. Base64-encodes a satellite image from `images/` and runs a structured wildfire risk assessment over it.
- **30_pdf.ipynb** — The same pattern for PDFs, sending `pdfs/earth.pdf` as a `document` block.
- **40_citations.ipynb** — Turning on `citations` so Claude grounds claims in the source, plus a pretty-printer that resolves page, character, and block citation locations.
- **50_prompt_caching.ipynb** — `cache_control` breakpoints over a ~6k-token system prompt and ~1.7k tokens of tool schemas, with a `show_cache` helper reporting cache creation vs. read tokens per call.

### Tool use

- **60_tools_text_editor.ipynb** — Anthropic's built-in text editor tool (`text_editor_20250728`). Implements a `TextEditorTool` class backing `view`/`create`/`str_replace` against the local filesystem, then has Claude write and test a `pie.py`.
- **61_tools_web_search.ipynb** — The server-side `web_search_20250305` tool, scoped with `max_uses` and an `allowed_domains` allowlist. No local execution needed — results come back as `server_tool_use` blocks.
- **70_code_execution.ipynb** — The Files API plus the `code_execution_20250825` sandbox. Uploads `streaming.csv`, has Claude run a churn analysis in the sandbox, and downloads the generated plot.

## Supporting files

| Path | Used by |
| --- | --- |
| `images/` | `20` — satellite property images (the notebook loads `prop7.png`; the others are alternates you can swap in) |
| `pdfs/earth.pdf` | `30`, `40` — source PDF |
| `streaming.csv` | `70` — churn dataset for the code execution sandbox |
| `outputs/` | Sample results, plus where `70` downloads files back from the sandbox |
| `.backups/` | Written by the `60` text editor tool before it edits a file (gitignored) |

Notebook `60` has Claude author `pie.py` and `test-pie.py` in the project root during the demo;
those are generated and gitignored. Copies of an earlier run are kept in `outputs/` for reference.

## Notes

- Every notebook here uses `claude-sonnet-4-5`.
- `70` needs beta headers (`code-execution-2025-08-25`, `files-api-2025-04-14`), set on the client via `default_headers`.
- Code execution requests are a single blocking call that runs the whole server-side loop, so `70` can take several minutes with no output until it finishes.
- The `60` text editor tool is sandboxed to `base_dir` (defaults to the notebook's working directory) and refuses paths that escape it.
