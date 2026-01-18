# Shiny bindings for editor

Output and render functions for using editor within Shiny applications
and interactive R Markdown documents.

## Usage

``` r
editor_output(outputId, width = "100%", height = "400px")

render_editor(expr, env = parent.frame(), quoted = FALSE)
```

## Arguments

- outputId:

  Output variable to read from.

- width, height:

  Widget dimensions (must be valid CSS unit or a number which will be
  coerced to a string and have "px" appended).

- expr:

  An expression that generates an editor widget.

- env:

  The environment in which to evaluate `expr`.

- quoted:

  Logical, whether `expr` is a quoted expression.

## Value

`editor_output()` returns a Shiny output element. `render_editor()`
returns a Shiny render function.
