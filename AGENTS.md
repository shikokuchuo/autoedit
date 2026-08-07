# AGENTS.md

Guidance for AI coding agents working **on** the shinysync package. shinysync provides collaborative Shiny components built on Automerge CRDT. R >= 4.4.

Claude Code users: add `.claude/CLAUDE.md` containing `@../AGENTS.md` to import this file (`.claude/` is gitignored).

## Commands

```bash
R CMD check .
Rscript -e "testthat::test_local()"                              # full suite (testthat ed 3, some snapshot tests)
Rscript -e "testthat::test_file('tests/testthat/test-editor.R')" # single test file
Rscript -e "devtools::document()"                                # roxygen2 -> man/, NAMESPACE
cd inst/build && npm install && npm run build                    # rebuild JS widget after editing inst/build/src
```

## Architecture

Three sync architectures coexist:

- **Browser-owned sync** — the `editor()` htmlwidget (CodeMirror 6 + JS `@automerge/automerge-repo`/`automerge-codemirror`) connects the browser directly to a sync server.
- **Server-free in-process sync** — `sync_inputs()`, `textarea_*`, `kanban_*`, `replay_*` keep a per-`doc_id` master Automerge document in R and sync each Shiny session to it (no external sync server).
- **R-owned WebSocket sync** — the `project_*` family (`project_open()`, `project_app()`, `project_edit()`) browses/edits a project document served by an `autosync` sync server, with R owning the sync via `autosync::sync_client()` and a bslib editor in the browser. Moved here from autosync.

### R layer (`R/`)

- `editor.R` — htmlwidget functions: `editor()`, `editor_output()`, `editor_render()` (bridge R and JS via htmlwidgets).
- `sync.R`, `textarea.R`, `kanban.R`, `replay.R` — server-free in-process collaborative modules.
- `project.R`, `app.R`, `edit.R` — the `project_*` family; `utils.R` holds their small helpers.

### JavaScript widget

- `inst/htmlwidgets/shinysyncEditor.js` — bundled widget (build output; never edit directly).
- `inst/htmlwidgets/shinysyncEditor.yaml` — widget dependency configuration.
- `inst/build/` — esbuild source and tooling (bundles CodeMirror + Automerge); excluded from the package build via `.Rbuildignore`.

### Key dependencies

- R Imports: automerge, autosync, bslib, htmlwidgets, later, shiny, tools. `autosync` is a GitHub remote (`Remotes: shikokuchuo/autosync`), not on CRAN.
- JS: @automerge/automerge-repo, @automerge/automerge-codemirror, codemirror.

## Automerge document structure

The Automerge document must have a "text" field of type Automerge text. In Shiny, editor content is available at `input$<outputId>_content`.

## Packaging notes

- roxygen2 with markdown; `NAMESPACE` is generated — never hand-edit.
- Vignettes are Quarto (`.qmd`), `VignetteBuilder: quarto`.
- Version is `major.minor.patch.dev` (current dev tag `.9000`).
- `AGENTS.md`, `.claude/`, and `.posit/` are in `.Rbuildignore` and don't ship to CRAN.
