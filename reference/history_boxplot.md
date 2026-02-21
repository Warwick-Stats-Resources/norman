# Boxplots of recent marks data

These are based on summary statistics from the \`history.csv\` file in
the working folder (if there is one), filtered on the five most recent
years. Note that because there are based on the minimum, maximum, and
quartiles of the marks data, rather than the raw marks, outliers aren't
shown.

## Usage

``` r
history_boxplot(history)
```

## Arguments

- history:

  A data frame containing marks history, as created by
  \[norman::save_history()\].

## See also

\[norman::n_recent_years()\], \[norman::save_history()\]
