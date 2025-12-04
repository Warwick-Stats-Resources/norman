# norman: Warwick Statistics Module Marks Analysis Package

## Overview

The **norman** package is a specialized R tool designed for the Department of Statistics at the University of Warwick to generate comprehensive PDF reports analyzing module examination marks. The package implements a robust statistical methodology based on median differences to identify "module effects" (relative difficulty across modules) while accounting for varying student abilities, enabling fair comparisons across different courses taken by overlapping student cohorts.

## Core Functions

### Data Input and Validation

**`print_path(folder)`**
Returns and displays the full absolute path to a specified folder, formatting it in a hierarchical structure for readability in reports.

**`print_file_listing(folder)`**
Scans a folder for CSV files and returns a formatted table showing filenames with their last modification timestamps, providing audit trail information for the data sources used in each report.

**`check_modules_expected(working_directory, module_codes)`**
Validates the marks data against an expected module list by reading `modules_expected_here.txt` and identifying any unexpected extra modules or missing expected modules, helping catch data errors early.

### Statistical Analysis Functions

**`meddiff(xmat, threshold = 5)`**
Computes a matrix of pairwise median within-student differences between all module pairs. For each pair of modules, it calculates the median of the mark differences across students who took both modules, requiring at least `threshold` students for a valid comparison. This forms the foundation of the module effects analysis.

**`meddiff_for_display(xmat, threshold = 5)`**
Produces a display-oriented version of median differences, formatting the results as a list where each module shows its comparisons with other modules along with student counts, optimized for inclusion in the report tables.

**`meddiff_fit(m)`**
Fits a weighted least-squares linear model to the median differences matrix to extract module effects. This estimates a single difficulty parameter for each module that best explains all pairwise median differences, with weights proportional to the number of student pairs underlying each median.

**`get_module_effects(module_codes, mdd)`**
Extracts and formats the fitted module effects from the model, centering them around their median (setting it to zero) and creating a ranked table showing relative difficulty from easiest to hardest, along with the count of comparisons available for each module.

**`print_module_effect(module_code, mdf)`**
Retrieves the module effect for a specific module and formats it as a human-readable string (e.g., "plus 5.2" or "minus 3.7") for inclusion in individual module reports.

**`list_all_median_differences(mdd)`**
Prints a comprehensive listing of all computed median differences between module pairs, organized by module, providing complete transparency into the underlying data used for the module effects calculation.

### Summary Statistics Functions

**`raw_mark_summaries(marks_matrix)`**
Computes an eight-number summary for each module including sample size, number of zeros, quartiles, maximum, mean, and standard deviation, providing a comprehensive overview of the mark distribution for each module.

**`raw_mark_classes(marks_matrix, dp = 0)`**
Calculates the percentage of students falling into each UK degree classification range (zero, 1-39, 40-49, 50-59, 60-69, 70+) for every module, revealing how marks distribute across conventional performance categories.

### Visualization Functions

**`stemleaf(module_code, module_marks)`**
Generates a stem-and-leaf plot for a specific module's marks, providing a compact visual representation of the distribution that preserves actual data values, useful for quickly identifying patterns and outliers.

**`scatter(module_code, marks_matrix, student_overall_median)`**
Creates a ggplot2 scatterplot comparing each student's mark in a specific module against their overall median mark across all modules taken. Points above the diagonal line indicate better-than-expected performance; points below indicate worse-than-expected performance. For final year cohorts, the plot uses color coding to distinguish between degree program types (BSc, MSc, M 3rd year, M 4th year).

### Report Generation Functions

**`make_module_pages(working_directory, module_codes, module_names, keep_tmpdir = FALSE)`**
The master function that orchestrates individual module report generation. It reads a module-specific R Markdown template, customizes it for each module by substituting module codes and names, renders each template using `knitr::knit_child()`, and returns the compiled R Markdown content ready for inclusion in the final PDF document.

### Utility Functions

**`update(build_opts = "--no-build-vignettes", ...)`**
Provides a convenient wrapper around `remotes::install_github()` for updating the norman package to the latest version from the GitHub repository, simplifying package maintenance for users.

## How the Report Generation Process Works

The norman package follows a structured workflow to transform raw CSV mark files into a comprehensive analytical report:

### 1. Data Ingestion Phase

The process begins when a user creates an R Markdown document from the norman template (`skeleton.Rmd`). This template references `report_body_material.Rmd`, which orchestrates the entire analysis. The system first calls `print_path()` and `print_file_listing()` to document the data sources, then reads all CSV files from the `marks/` subfolder, extracting `sprCode` (student identifier) and `overallMark` columns. These individual module mark files are consolidated into a single `marks_matrix` where rows represent students and columns represent modules, with `NA` values for module-student combinations where the student did not take that module.

### 2. Validation Phase

Before analysis begins, `check_modules_expected()` validates that the marks data matches expectations by comparing found modules against the `modules_expected_here.txt` file, and the system verifies that all module codes have corresponding names in `module_names.csv`. Any discrepancies are prominently reported at the top of the document to alert users to potential data issues.

### 3. Descriptive Statistics Phase

The report then provides a comprehensive overview of all modules. `raw_mark_summaries()` generates traditional statistical summaries (quartiles, means, standard deviations), while `raw_mark_classes()` shows the distribution across UK degree classification boundaries. These tables allow quick identification of modules with unusual mark distributions before diving into comparative analysis.

### 4. Module Effects Analysis Phase

This is the analytical heart of the package. The process:

- Calls `meddiff()` to compute all pairwise median differences between modules based on students who took both modules in each pair
- Calls `meddiff_fit()` to fit a least-squares model that finds a single "module effect" parameter for each module that best explains all the median differences
- Uses `get_module_effects()` to extract and rank these effects from easiest to hardest, centered around a median of zero
- Generates a Q-Q plot to assess whether the distribution of module effects is consistent with natural variation (normally distributed) or contains outliers deserving special attention
- Calls `list_all_median_differences()` to provide complete transparency by printing every pairwise comparison underlying the fitted effects

This methodology is more robust than simple mean comparisons because it uses medians (resistant to outliers) and leverages within-student differences (controlling for overall student ability differences across cohorts).

### 5. Individual Module Pages Phase

The final section creates a detailed page for each module in landscape orientation. `make_module_pages()`:

- Reads the `module-template.Rmd` template file
- For each module, creates a customized copy substituting the actual module code and name
- Saves each to a temporary directory
- Renders each using `knitr::knit_child()`
- Collects all rendered output into a single character vector

Each individual module page combines multiple visualizations and statistics:

- `scatter()` creates a scatterplot showing how each student's mark in this module compares to their overall median performance
- `stemleaf()` provides a stem-and-leaf display of the raw mark distribution
- `print_module_effect()` reports the fitted module effect in human-readable form
- Selected rows from the `raw_mark_summaries()` and `raw_mark_classes()` tables show this module's statistics

### 6. Document Assembly and Rendering

The main R Markdown document uses `knitr` to execute all code chunks in sequence, assembling:

1. Title page with report metadata, author, and remarks
2. Data source documentation
3. Data validation results
4. Overall summary tables for all modules
5. Module effects analysis with rankings and Q-Q plot
6. Complete listing of median differences
7. Individual module pages (one per module in landscape)

The final `knit` operation converts this R Markdown document to PDF via LaTeX, producing a publication-ready report suitable for examination board meetings and scaling committee decisions.

### Design Philosophy

The package embodies several key design principles:

- **Reproducibility**: All data sources and timestamps are documented
- **Transparency**: All statistical methods and raw data are exposed in the report
- **Robustness**: Median-based methods resist influence from outliers or small numbers of unusual marks
- **Fairness**: Within-student comparisons control for ability differences across cohorts
- **Usability**: Template-based approach requires minimal R knowledge from users
- **Auditability**: Validation checks catch common data errors early

This architecture enables statistics administrators to generate standardized, rigorous analytical reports with minimal manual intervention while maintaining full transparency into the methodology and underlying data.
