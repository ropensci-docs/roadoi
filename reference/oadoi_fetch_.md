# Get open access status information.

In general, use
[`oadoi_fetch`](https://docs.ropensci.org/roadoi/reference/oadoi_fetch.md)
instead. It calls this method, returning open access status information
from all your requests.

## Usage

``` r
oadoi_fetch_(doi = NULL, email = NULL)
```

## Arguments

- doi:

  character vector,a DOI

- email:

  character vector, mandatory! Unpaywall requires your email address, so
  that they can track usage and notify you when something breaks. Set
  email address in your \`.Renviron\` file with the option
  \`roadoi_email\` `options(roadoi_email = "najko.jahn@gmail.com")`. You
  can open your \`.Renviron\` file calling \`file.edit("~/.Renviron")\`.
  Save the file and restart your R session. To stop sharing your email
  when using rcrossref, delete it from your \`.Renviron\` file.

## Value

A tibble

## Examples

``` r
if (FALSE) { # \dontrun{
oadoi_fetch_(doi = c("10.1016/j.jbiotec.2010.07.030"))
} # }
```
