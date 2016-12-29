
-   [reprex](#reprex)
-   [Install and load](#install-and-load)
-   [Quick start](#quick-start)
-   [More control](#more-control)
    -   [knitr options](#knitr-options)
    -   [Embedded prose](#embedded-prose)
-   [What is a reprex?](#what-is-a-reprex)
-   [Package philosophy](#package-philosophy)
-   [Other work](#other-work)

<!-- README.md is generated from README.Rmd. Please edit that file -->
[![Travis-CI Build Status](https://travis-ci.org/jennybc/reprex.svg?branch=master)](https://travis-ci.org/jennybc/reprex) [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/jennybc/reprex?branch=master&svg=true)](https://ci.appveyor.com/project/jennybc/reprex) [![Project Status: Wip - Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](http://www.repostatus.org/badges/0.1.0/wip.svg)](http://www.repostatus.org/#wip) [![](http://www.r-pkg.org/badges/version/reprex)](http://www.r-pkg.org/pkg/reprex) [![Coverage Status](https://img.shields.io/codecov/c/github/jennybc/reprex/master.svg)](https://codecov.io/github/jennybc/reprex?branch=master)

reprex
------

<a href="https://nypdecider.files.wordpress.com/2014/08/help-me-help-you.gif"> <img src="https://raw.githubusercontent.com/jennybc/reprex/master/img/help-me-help-you-still-500-c256.png" width="275" align="right"> </a>

Prepare reproducible examples for posting to [GitHub issues](https://guides.github.com/features/issues/), [StackOverflow](http://stackoverflow.com/questions/tagged/r), etc.

-   Given R code on the clipboard, as an expression (quoted or not), or in a file ...
-   run it via `rmarkdown::render()`,
-   with deliberate choices re: arguments and setup chunk.
-   Get resulting runnable code + output as Markdown,
-   formatted for target venue, e.g. `gh` or `so`,
-   on the clipboard and, optionally, in a file.
-   Preview an HTML version in RStudio viewer or default browser.

Install and load
----------------

``` r
devtools::install_github("jennybc/reprex")
library(reprex)
```

Quick start
-----------

Let's say you copy this code onto your clipboard:

    (y <- 1:4)
    mean(y)

Then call `reprex()`, where the default target venue is GitHub:

``` r
reprex()
```

A nicely rendered HTML preview will display in RStudio's Viewer (if you're in RStudio) or your default browser otherwise.

![html-preview](https://raw.githubusercontent.com/jennybc/reprex/master/img/README-viewer-screenshot.png)

The relevant bit of GitHub-flavored Markdown is ready to be pasted from your clipboard:

    ``` r
    (y <- 1:4)
    #> [1] 1 2 3 4
    mean(y)
    #> [1] 2.5
    ```

Here's what that Markdown would look like rendered in a GitHub issue:

``` r
(y <- 1:4)
#> [1] 1 2 3 4
mean(y)
#> [1] 2.5
```

Anyone else can copy, paste, and run this immediately.

But wait, there's more!

-   Set the target venue to StackOverflow with `reprex(venue = "so")`.
-   By default, figures are uploaded to [imgur.com](http://imgur.com) and resulting URL is dropped into an inline image tag.
-   Alternatives to clipboard input:
    -   `reprex({(y <- 1:4); mean(y)})` gets code from expression
    -   `reprex(input = c("(y <- 1:4)", "mean(y)"))` gets code from character vector (detected via length or terminating newline)
    -   `reprex(input = "my_reprex.R")` gets code from file
-   Undo `reprex()` with `reprex_clean()` and `reprex_invert()`.

More control
------------

Examples of how to take greater control of your reprex.

### knitr options

Supplement or override reprex defaults for [knitr chunk and package options](http://yihui.name/knitr/options/) via the arguments `opts_chunk` and `opts_knit`. Example: change the prefix used to comment out the output. This call:

``` r
reprex({y <- 1:4; mean(y)}, opts_chunk = list(comment = "#¯\\_(ツ)_/¯"))
```

yields this output:

``` r
y <- 1:4
mean(y)
#¯\_(ツ)_/¯ [1] 2.5
```

### Embedded prose

Sometime you want to mingle rendered code and prose. Put the embedded prose in as roxygen comments, i.e. comment lines that start with `#'`. This reprex code:

    ## a regular comment
    x <- 1:100
    #' Here is some embedded prose, as a roxygen comment.
    mean(x)

renders to this this result:

``` r
## a regular comment
x <- 1:100
```

Here is some embedded prose, as a roxygen comment.

``` r
mean(x)
#> [1] 50.5
```

What is a reprex?
-----------------

What is a `reprex`? It's a {repr}oducible {ex}ample. Coined by Romain Francois [on twitter](https://twitter.com/romain_francois/status/530011023743655936).

Where and why are they used?

-   A StackOverflow question that includes a proper reprex is [much more likely to get answered](http://stackoverflow.com/help/no-one-answers), by the most knowledgeable (and therefore busy!) people.
-   A [GitHub issue](https://guides.github.com/features/issues/) that includes a proper reprex is more likely to achieve your goal: getting a bug fixed or getting a new feature, in a finite amount of time.

What are the main requirements?

-   Use the smallest, simplest, most [built-in data](https://stat.ethz.ch/R-manual/R-patched/library/datasets/html/00Index.html) possible.
    -   Think: `iris` or `mtcars`. Bore me.
    -   If you must make some objects, minimize their size and complexity.
    -   Get just a bit of something with `head()` or by indexing with the result of `sample()`. If anything is random, consider using `set.seed()` to make it repeatable.
    -   `dput()` is a good way to get the code to create an object you have lying around. Copy and paste the *result* of this into your reprex.
    -   Look at official examples and try to write in that style. Consider adapting one.
-   Include commands on a strict "need to run" basis.
    -   Ruthlessly strip out anything unrelated to the specific matter at hand.
    -   Include every single command that is required, e.g. loading specific packages via `library(foo)`.
-   Consider including so-called "session info", i.e. your OS and versions of R and add-on packages, if it's conceivable that it matters. Use `reprex(..., si = TRUE)` for this.
-   Whitespace rationing is not in effect. Use good [coding style](http://adv-r.had.co.nz/Style.html).
-   Pack it in, pack it out, and don't take liberties with other people's computers. You are asking people to run this code!
    -   If you change options, store original values at the start, do your thing, then restore them: `opar <- par(pch = 19) <blah blah blah> par(opar)`.
    -   If you create files, delete them when you're done: `write(x, "foo.txt") <blah blah blah> file.remove("foo.txt")`.
    -   Don't delete files or objects that you didn't create in the first place.
    -   Don't mask built-in functions, i.e. don't define a new function named `c`.
    -   Take advantage of R's built-in ability to create temporary files and directories. Read up on [`tempfile()` and `tempdir()`](https://stat.ethz.ch/R-manual/R-devel/library/base/html/tempfile.html).
    -   Don't start with `setwd("C:\Users\jenny\path\that\only\I\have")`, because it won't work on anyone else's computer.
    -   Don't start with `rm(list = ls())`, because it is anti-social to clobber other people's workspaces.

But won't that take time and effort?

-   Yes, yes it will!
-   80% of the time you will solve your own problem in the course of writing an excellent reprex. YMMV.
-   The remaining 20% of the time, you will create a reprex that is more likely to elicit the desired behavior in others.

Get more concrete details here:

-   [How to make a great R reproducible example?](http://stackoverflow.com/questions/5963269/how-to-make-a-great-r-reproducible-example/16532098) thread on StackOverflow
-   [How to write a reproducible example](http://adv-r.had.co.nz/Reproducibility.html) from Hadley Wickham's [Advanced R book](http://adv-r.had.co.nz)

Package philosophy
------------------

The reprex code:

-   Must run and, therefore, should be run **by the person posting**. No faking it.
-   Should be easy for others to digest, so **they don't necessarily have to run it**. You are encouraged to include selected bits of output. :scream:
-   Should be easy for others to copy + paste + run, **iff they so choose**. Don't let inclusion of output break executability.

Accomplished like so:

-   use `rmarkdown::render` or, under the hood, `knitr::spin` to run the code and capture output that would display in R console
-   use chunk option `comment = "#>"` to include the output while retaining executability

Other work
----------

If I had known about [`formatR::tidy_eval()`](http://yihui.name/formatR/), I probably would never had made reprex! But alas I did not. AFAICT here are the main differences:

-   `reprex()` accepts an expression as primary input, in addition to code on the clipboard, in a character vector, or in a file.
-   `reprex()` runs the reprex in a separate R process, via [callr](https://cran.r-project.org/package=callr). `tidy_eval()` uses the existing R process and offers an `envir` argument.
-   `reprex()` writes the code to a `.R` file and calls `rmarkdown::render()`. `tidy_eval()` runs the code line-by-line via `capture.output(eval(..., envir = envir))`.
-   `reprex()` uploads figures to imgur and inserts the necessary link.
