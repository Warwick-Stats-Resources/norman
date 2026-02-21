# List all median within-student differences between modules

List all median within-student differences between modules

## Usage

``` r
list_all_median_differences(mdd)
```

## Arguments

- mdd:

  A list

## Value

`invisible(NULL)`

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
mdd <- meddiff_for_display(x)
list_all_median_differences(mdd)
#> ------------------------------------------------------------------------------------------------
#> A --- comparisons with:
#>                  
#> Median difference
#> Count            
#> ------------------------------------------------------------------------------------------------
#> B --- comparisons with:
#>                  
#> Median difference
#> Count            
#> ------------------------------------------------------------------------------------------------
#> C --- comparisons with:
#>                  
#> Median difference
#> Count            
#> ------------------------------------------------------------------------------------------------
#> D --- comparisons with:
#>                  
#> Median difference
#> Count            
#> ------------------------------------------------------------------------------------------------
#> E --- comparisons with:
#>                  
#> Median difference
#> Count            
#> 
#> #####################################     END OF LIST     ######################################
```
