# Make a module-specific scatterplot

Make a module-specific scatterplot

## Usage

``` r
scatter(module_code, marks_matrix, student_overall_median, has_groups)
```

## Arguments

- module_code:

  Character; the length-5 module code

- marks_matrix:

  Numeric, the matrix of marks

- student_overall_median:

  The row medians of marks_matrix

- has_groups:

  Logical. Will be true if both \`student_course.csv\` and
  \`course_mappings.csv\` exist in the working folder.

## Value

A `ggplot` object
