# Make a report page for each module

Make a report page for each module

## Usage

``` r
make_module_pages(
  working_directory,
  module_codes,
  module_names,
  keep_tmpdir = FALSE
)
```

## Arguments

- working_directory:

  Character; the full path to the folder for the report

- module_codes:

  Character; the vector of module codes used in the report

- module_names:

  Character, or NULL if no "module_names.csv" file was provided

- keep_tmpdir:

  Logical; whether to keep the working "tmp" directory

## Value

Character; R Markdown text for the module pages that were made
