# Save history

If a "history.csv" file exists, adds the eight-number summary and module
effect to that file, one row per module. If "history.csv" does not
exist, creates the file first.

## Usage

``` r
save_history()
```

## Details

history.csv must have the following columns:

\- Year - Module - (N) - Zeros - Min. - 1st Qu. - Median - 3rd Qu. -
Max. - Mean, - S.D.

## Examples

``` r
if (FALSE) { # \dontrun{
norman::save_history()
} # }
```
