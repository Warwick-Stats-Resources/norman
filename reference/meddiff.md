# Compute a matrix of median differences

Compute a matrix of median differences

## Usage

``` r
meddiff(xmat, threshold = 5)
```

## Arguments

- xmat:

  A numeric matrix

- threshold:

  Numeric scalar, minimum number of pairs needed for computation of a
  median difference

## Value

A square numeric matrix, with size equal to the number of columns in
`xmat`

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
meddiff(x, threshold = 1)
#>    A   B   C  D   E
#> A NA -10 -20 NA -40
#> B  1  NA -10 NA -30
#> C  1   1  NA NA -20
#> D  0   0   0 NA -10
#> E  1   1   1  2  NA
```
