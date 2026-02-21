# Extract module effects from the median differences

Extract module effects from the median differences

## Usage

``` r
meddiff_fit(m)
```

## Arguments

- m:

  A numeric matrix of median differences as computed by `meddiff`

## Value

A `lm` model object

## Examples

``` r
#
# Toy example from
# https://davidfirth.github.io/blog/2019/04/26/robust-measurement-from-a-2-way-table/
#
x <- structure(c(NA, NA, 10, NA, NA, 20, NA, NA, 30, 45, 55, NA, 60, 60, 50),
  .Dim = c(3L, 5L), .Dimnames = structure(list(student = c("i", "j", "k"),
  module = c("A", "B", "C", "D", "E")), .Names = c("student", "module")))
print(x)
#>        module
#> student  A  B  C  D  E
#>       i NA NA NA 45 60
#>       j NA NA NA 55 60
#>       k 10 20 30 NA 50
md <- meddiff(x, threshold = 1)
the_fit <- meddiff_fit(md)$coef
names(the_fit) <- gsub("^X", "", names(the_fit))
the_fit
#>   A   B   C   D   E 
#> -40 -30 -20 -10   0 
```
