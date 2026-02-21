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

**`scatter(module_code, marks_matrix, student_overall_median, has_groups)`**
Creates a ggplot2 scatterplot comparing each student's mark in a specific module against their overall median mark across all modules taken. Points above the diagonal line indicate better-than-expected performance; points below indicate worse-than-expected performance. When `has_groups = TRUE` and the optional grouping CSV files are present, the plot uses color coding with a colorblind-safe Okabe-Ito palette to distinguish between student groups as defined in `student_course.csv` and `course_mappings.csv`. When `has_groups = FALSE`, creates a simple grayscale scatterplot.

### Report Generation Functions

**`make_module_pages(working_directory, module_codes, module_names, keep_tmpdir = FALSE)`**
The master function that orchestrates individual module report generation. It reads a module-specific R Markdown template, customizes it for each module by substituting module codes and names, renders each template using `knitr::knit_child()`, and returns the compiled R Markdown content ready for inclusion in the final PDF document.

### History Analysis Functions

**`save_history(summaries, mdf, working_directory)`**
Saves the eight-number summaries and module effects to a `history.csv` file for longitudinal tracking of module statistics across academic years. The function reads the current year from a `year.txt` file (which must contain a single line with a year between 1000-2999), creates the history file if it doesn't exist, and appends new rows for each module with columns: Year, Module, (N), Zeros, Min., 1st Qu., Median, 3rd Qu., Max., Mean, S.D., and Effect. If data for the current year already exists, the user is prompted via an interactive menu to choose whether to overwrite or cancel. This function enables tracking of module performance trends over time.

**`n_recent_years(history, n)`**
Filters a history dataframe to return only the `n` most recent years of data, using `dplyr::dense_rank()` to handle year ordering. This is useful for focusing visualizations on recent trends while maintaining access to the full historical record.

**`history_boxplot(history)`**
Creates a ggplot2 boxplot visualization showing the distribution of marks across years for a specific module. Uses the five-number summary statistics (Min., 1st Qu., Median, 3rd Qu., Max.) from the history data to reconstruct boxplots for each year, providing a visual representation of how mark distributions have changed over time. The plot is titled "Mark distribution by year, from summary statistics".

**`history_effects(history)`**
Creates a ggplot2 line plot showing the trend of module effects over time for a specific module. Uses `scales::breaks_width()` to ensure appropriate year-based axis breaks. The plot is titled "History of module effects" and allows examination boards to identify whether a module's relative difficulty has been stable or has changed systematically over time.

### Utility Functions

**`update(build_opts = "--no-build-vignettes", ...)`**
Provides a convenient wrapper around `remotes::install_github()` for updating the norman package to the latest version from the GitHub repository, simplifying package maintenance for users.

## How the Report Generation Process Works

The norman package follows a structured workflow to transform raw CSV mark files into a comprehensive analytical report:

### 1. Data Ingestion Phase

The process begins when a user creates an R Markdown document from the norman template (`skeleton.Rmd`). This template references `report_body_material.Rmd`, which orchestrates the entire analysis. The system first calls `print_path()` and `print_file_listing()` to document the data sources, then reads all CSV files from the `marks/` subfolder, extracting `sprCode` (student identifier) and `overallMark` columns. Module codes are determined by taking the first 5 characters of each CSV filename. These individual module mark files are consolidated into a single `marks_matrix` where rows represent students and columns represent modules, with `NA` values for module-student combinations where the student did not take that module.

The system also checks for optional grouping files (`student_course.csv` and `course_mappings.csv`) which enable color-coded scatterplots by student group. If present, these files are validated to ensure correct column names: `student_course.csv` must have columns "ID" and "Course", while `course_mappings.csv` must have columns "Course" and "Group". The `has_groups` flag is set based on whether both files exist and pass validation.

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

- `scatter()` creates a scatterplot showing how each student's mark in this module compares to their overall median performance, with optional color-coding by student group
- `stemleaf()` provides a stem-and-leaf display of the raw mark distribution
- `print_module_effect()` reports the fitted module effect in human-readable form
- Selected rows from the `raw_mark_summaries()` and `raw_mark_classes()` tables show this module's statistics

If historical data exists for the module in `history.csv`, an additional landscape page is created showing:

- `history_boxplot()` displaying mark distributions for up to the 5 most recent years
- `history_effects()` showing the trend of module effects over time

These history visualizations are positioned side-by-side on the page using `\vspace` positioning, with the boxplot on the left and the effects plot on the right. The system filters the module-specific history using `n_recent_years()` to focus on the 5 most recent years of data.

### 6. Document Assembly and Rendering

The main R Markdown document uses `knitr` to execute all code chunks in sequence, assembling:

1. Title page with report metadata, author, and remarks
2. Data source documentation
3. Data validation results
4. Overall summary tables for all modules
5. Module effects analysis with rankings and Q-Q plot
6. Complete listing of median differences
7. Individual module pages (one per module in landscape)
8. For each module with history data: an additional landscape page with historical visualizations

The final `knit` operation converts this R Markdown document to PDF via LaTeX, producing a publication-ready report suitable for examination board meetings and scaling committee decisions.

### 7. Post-Report History Tracking

After generating a report, users can call `save_history()` to archive the current year's statistics to `history.csv`. This function requires a `year.txt` file in the working directory containing the academic year. The accumulated history enables longitudinal analysis in future reports, allowing examination boards to monitor trends in module difficulty and mark distributions over multiple years. This supports evidence-based decisions about module design, assessment difficulty, and the stability of scaling adjustments.

### Design Philosophy

The package embodies several key design principles:

- **Reproducibility**: All data sources and timestamps are documented
- **Transparency**: All statistical methods and raw data are exposed in the report
- **Robustness**: Median-based methods resist influence from outliers or small numbers of unusual marks
- **Fairness**: Within-student comparisons control for ability differences across cohorts
- **Usability**: Template-based approach requires minimal R knowledge from users
- **Auditability**: Validation checks catch common data errors early
- **Flexibility**: External CSV-based grouping system allows customization without code changes
- **Longitudinal awareness**: History tracking enables trend analysis across academic years
- **Accessibility**: Colorblind-safe palettes ensure visualizations are readable by all users

This architecture enables statistics administrators to generate standardized, rigorous analytical reports with minimal manual intervention while maintaining full transparency into the methodology and underlying data.

## Expected Data Files

The norman package expects the following file structure in the working directory:

### Required Files

**`marks/` folder**: Contains CSV files with module marks
- Filenames: First 5 characters are used as the module code (e.g., `ST123_marks.csv` → module code "ST123")
- Required columns: `sprCode` (student identifier), `overallMark` (numeric mark)

**`module_names.csv`**: Maps module codes to human-readable names
- Required columns: `ModuleCode`, `ModuleNames` (or similar)
- Used throughout the report to display full module titles

**`modules_expected_here.txt`**: Lists expected module codes for validation
- One module code per line
- Used by `check_modules_expected()` to identify missing or unexpected modules

### Optional Files

**`year.txt`**: Contains the academic year for history tracking
- Single line with a 4-digit year between 1000-2999
- Required for `save_history()` function

**`history.csv`**: Historical module statistics (created automatically by `save_history()`)
- Columns: Year, Module, (N), Zeros, Min., 1st Qu., Median, 3rd Qu., Max., Mean, S.D., Effect
- Enables longitudinal visualizations in module pages

**`student_course.csv`**: Maps students to course identifiers for grouping
- Required columns: `ID` (character), `Course` (character)
- Enables color-coded scatter plots when combined with `course_mappings.csv`

**`course_mappings.csv`**: Maps course identifiers to display groups
- Required columns: `Course` (character), `Group` (character)
- Works with `student_course.csv` to enable group-based visualization
- Uses colorblind-safe Okabe-Ito palette (supports up to 7 groups)
