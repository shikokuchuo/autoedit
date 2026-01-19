# autoedit

A real-time collaborative code editor widget for R and Shiny, built on
[CodeMirror 6](https://codemirror.net/) and
[Automerge](https://automerge.org/) CRDT.

[![Ask
DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shikokuchuo/autoedit)

## Installation

``` r
pak::pak("shikokuchuo/autoedit")
```

## Usage

The editor connects to any automerge-repo compatible sync server via
WebSocket:

``` r
library(autoedit)

# Connect to a sync server
editor("ws://localhost:3030", "document-id")

# Or use the public Automerge sync server
editor("wss://sync.automerge.org", "your-document-id")
```

## Shiny Example

``` r
library(shiny)
library(automerge)
library(autosync)
library(autoedit)

server <- amsync_server(port = 3030, host = "127.0.0.1")
server$start()

doc_id <- create_document(server)
doc <- get_document(server, doc_id)
am_put(doc, AM_ROOT, "text", am_text(""))
am_commit(doc, "init")

ui <- fluidPage(editor_output("editor"))

shiny_server <- function(input, output, session) {
  output$editor <- render_editor(editor(server$url, doc_id))
}

onStop(function() server$stop())
shinyApp(ui, shiny_server)
```

Open in multiple browser windows for real-time collaboration.

## Building the JavaScript Widget

To rebuild the bundled JavaScript widget:

``` bash
cd inst/build
npm install
npm run build
```

## Related Packages

- [autosync](https://github.com/shikokuchuo/autosync) - R sync server
  for Automerge documents
- [automerge](https://github.com/posit-dev/automerge-r) - R bindings for
  Automerge CRDT
