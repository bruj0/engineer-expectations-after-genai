# What Engineers Are Expected to Do After Generative AI

Source for the public essay hosted at
<https://bruj0.github.io/engineer-expectations-after-genai>.

- [`docs/index.md`](docs/index.md) is the canonical Markdown source.
- The site is built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/)
  and the [`mkdocs-panzoom-plugin`](https://github.com/lichtwellicht/mkdocs-panzoom-plugin)
  through `.github/workflows/docs.yml`, which deploys to GitHub Pages on every
  push to `main`.

## Build locally

```bash
uv sync --extra docs
uv run mkdocs serve
```

Open <http://127.0.0.1:8000>.
