# A version of `meddiff` to compute median differences in a different format

A version of `meddiff` to compute median differences in a different
format

## Usage

``` r
meddiff_for_display(xmat, threshold = 5)
```

## Arguments

- xmat:

  A numeric matrix

- threshold:

  Numeric scalar, minimum number of pairs needed for computation of a
  median difference

## Value

A list, with one vector component for each column of `xmat`

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
meddiff_for_display(x, threshold = 1)
#> $A
#>                     B   C   E
#> Median difference -10 -20 -40
#> Count               1   1   1
#> 
#> $B
#>                    A   C   E
#> Median difference 10 -10 -30
#> Count              1   1   1
#> 
#> $C
#>                    A  B   E
#> Median difference 20 10 -20
#> Count              1  1   1
#> 
#> $D
#>                     E
#> Median difference -10
#> Count               2
#> 
#> $E
#>                    A  B  C  D
#> Median difference 40 30 20 10
#> Count              1  1  1  2
#> 
```
