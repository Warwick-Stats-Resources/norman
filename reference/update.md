# Update the `norman` package — a wrapper for `remotes::install_github`

Update the `norman` package — a wrapper for
[`remotes::install_github`](https://remotes.r-lib.org/reference/install_github.html)

## Usage

``` r
update(build_opts = "--no-build-vignettes", ...)
```

## Arguments

- build_opts:

  Character; options for `R CMD build`. Default is
  `"--no-build-vignettes"`.

- ...:

  Other arguments to pass to
  [`remotes::install_github`](https://remotes.r-lib.org/reference/install_github.html)

## Examples

``` r
if (FALSE) { # \dontrun{
norman::update()
norman::update(force = TRUE)
} # }
```
