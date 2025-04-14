# Frequency Table Formatting

This projects contains functions that help automatically format simple to complex summary/frequency tables outputted by R's summarize function. 


## Custom Color Selection

Purpose:

The function is designed to choose a color scheme by setting a series of global color variables. These colors can be used for setting up plots, themes, or any visualization that requires a consistent color palette.

Parameters:

color_scheme_custom: This parameter allows you to select one of several predefined color schemes. The default is "Navy". Valid options include: "Red-Orange", "Navy", "Nature", "Fade_Olive_to_Orange", "Fade_GreenGray_to_Peach", "Fade_Olive_to_Peach", "Neutrals", "Grays", and "Strawberry".

Return Value:
The function doesn’t return any value; instead, it sets global variables (level0 to level4, and in the "Navy" scheme, level5 as well).

Usage Example:
To use the function, simply call it with the desired color scheme:




## Expand and Summarize Categorical Data Tables

A flexible function to transform and summarize categorical data tables, optionally preading categorical values into separate columns, calculating totals, and adding custom aggregation columns.

Function Overview: summ_expand

Purpose:

summ_expand is a flexible R function designed to transform and summarize categorical data tables. It provides features for spreading categorical values into separate columns, calculating hierarchical totals, and appending custom aggregation columns such as percentages or means. It's particularly useful for generating compact, summary-style tables from structured categorical count data. This function works as the first step to the summ_cascade function which formats the expanded table that the summ_expand function sets up. 

Function Inputs

* data- A data frame that includes one or more categorical columns and a count column (commonly named n). The function assumes that categorical columns are positioned before the count column.
* total_label- (default = "Total") A character label used for the overall total row in the output table.
* spread- (default = FALSE) Logical flag. If TRUE, the last categorical column before the count column is spread into wide format, creating one column per category level.
* spread_labels- (optional) A vector of custom labels to use for the spread columns. If not provided, labels are automatically derived from the data.
* add_agg_columns- (optional) A character vector specifying additional summary statistics to be calculated. For example, "perc" to calculate percentages.
* arrange_on_n- (optional) If "desc", the output is sorted in descending order of the n column. If "ascend", sorted in ascending order. If NULL, no sorting is performed.
* remove_cols- (optional) A vector of column names to be excluded from the final output.
* n_col- (default = "n") The name of the column in the data that contains the counts.
* n_cat- (optional) An integer specifying the number of categorical columns before the count column. If not supplied, it is inferred automatically.

Function Output

A data frame with:

* An expanded hierarchical structure (grand total, category totals, subcategories).
* Optional spread columns if spread = TRUE.
* Optional additional metrics such as percentages (perc), percentages of categories (perc_cat), or averages (mean, min, max).
* Aggregation rows at different levels of the categorical hierarchy.

Details & Behavior

Category Handling:
* The function dynamically detects how many category columns precede the count column unless specified. It can group and nest data accordingly.

Spreading:
* When spread = TRUE, the final category column is transformed into multiple columns using tidyr::spread, allowing for side-by-side comparison of subcategories.

Aggregation Types:
* Various aggregation operations are supported via add_agg_columns. These can include:
* perc: Percentage of the row relative to its group or total.
* perc_cat: Percentage within the same category.
* perc_act: Percentage relative to the full dataset.
* mean, min, max: Descriptive statistics.

Totaling & Arrangement:

* A grand total row is always added (label set by total_label). The data can optionally be sorted based on count values.



## Format Cascade Table

Function Overview: summ_cascade_table

Purpose:

summ_cascade_table is a flexible formatting and styling function that takes a data frame with categorized and aggregated data (from the summ_expand function) and returns a styled, structured table using the flextable package. It highlights hierarchical groupings, applies custom color schemes, formats numeric columns, and supports merged headers and value columns. It is ideal for producing polished cascade-style summary tables for reports or presentations.

Function Inputs

* data: A data frame where: 
     * The first column (cat) contains hierarchical labels using dashes to indicate nesting.
     * The subsequent columns include one or more categorical columns (e.g., col1, col2, ...) and numeric columns such as counts or percentages (e.g., n, perc).
* color_scheme_custom: (default = "Navy") A string indicating the color scheme to use for row-level highlighting (e.g., "Navy", "Teal", etc.). This is passed to an external choose_color_scheme function.
* width_cat_col: (default = 3) Width (in inches) for the last category column, which typically contains the most detailed group labels.
* width_n_cols: (default = 0.4) Width (in inches) for the summary metric columns (e.g., n, perc).
* merge_headers: (default = FALSE) Logical flag. If TRUE, additional header rows are added using merge_labels and merge_values.
* merge_values: (optional) A numeric vector indicating how many columns each label in merge_labels should span.
* merge_labels: (optional) A character vector of labels to use in the merged header row.

Function Output

Returns a flextable object styled as a hierarchical cascade table with:

* Percentage formatting applied to relevant columns.
* Background coloring based on category nesting.
* Custom column widths and row heights.
* Optional merged headers and relabeling for presentation.
* Final touches like padding, borders, and centered bold headers.

Details & Behavior
1. Preprocessing and Formatting
All columns with names containing "perc", "pct", or "%" are converted to formatted percentage strings (e.g., "23.5%").

The first column (cat) is removed for display purposes but used internally for styling.

2. Color Assignment by Hierarchy
Each row is styled based on its nesting level in the hierarchy (number of - in the cat column):

cat_num = 0 → total row

cat_num = 1 → top-level group

cat_num = 2 → second-level group

etc.

Color levels are assigned dynamically (e.g., level1, level2, etc.) via the choose_color_scheme() function.

3. Merging and Styling
For each row, appropriate columns are merged across category levels.

Borders are applied around cells in each group.

Background color is applied according to hierarchy level.

4. Table Layout Adjustments
Column widths:

First (n_cat-1) columns: 0.4 inches

Last category column: width_cat_col

Remaining summary columns: width_n_cols

Row heights are fixed at 0.25 inches for consistency.

Padding and horizontal rules are applied.

5. Column Renaming
Common columns are renamed for clarity:

n → "N"

perc → "%", and so on.

6. Optional Header Merging
If merge_headers = TRUE, a custom top header row is added using:

merge_labels: Labels for each group of columns.

merge_values: Number of columns each label should span.

7. Final Touches
Header row is styled with:

Bold white text on a colored background (level0)

Center-aligned labels

Border all around
## Format Flex Table

Formats a summary data table (typically categorical with N counts and optional additional metrics like percent, mean, etc.) into a styled flextable object. Automatically handles aggregation (row and column totals), reshaping (via spread), and aesthetic customizations for publication-ready output.

Function Inputs

* data- A data frame that includes one or more categorical columns and a count column (commonly named n). The function assumes that categorical columns are positioned before the count column.
* total_label- (default = "Total") A character label used for the overall total row in the output table.
* spread- (default = FALSE) Logical flag. If TRUE, the last categorical column before the count column is spread into wide format, creating one column per category level.
* spread_labels- (optional) A vector of custom labels to use for the spread columns. If not provided, labels are automatically derived from the data.
* add_agg_columns- (optional) A character vector specifying additional summary statistics to be calculated. For example, "perc" to calculate percentages.
* arrange_on_n- (optional) If "desc", the output is sorted in descending order of the n column. If "ascend", sorted in ascending order. If NULL, no sorting is performed.
* remove_cols- (optional) A vector of column names to be excluded from the final output.
* n_col- (default = "n") The name of the column in the data that contains the counts.
* n_cat- (optional) An integer specifying the number of categorical columns before the count column. If not supplied, it is inferred automatically.
* color_scheme_custom: (default = "Navy") A string indicating the color scheme to use for row-level highlighting (e.g., "Navy", "Teal", etc.). This is passed to an external choose_color_scheme function.
* width_cat_col: (default = 3) Width (in inches) for the last category column, which typically contains the most detailed group labels.
* width_n_cols: (default = 0.4) Width (in inches) for the summary metric columns (e.g., n, perc).
* merge_headers: (default = FALSE) Logical flag. If TRUE, additional header rows are added using merge_labels and merge_values.
* merge_values: (optional) A numeric vector indicating how many columns each label in merge_labels should span.
* merge_labels: (optional) A character vector of labels to use in the merged header row.

Function Behavior Summary:

1. Preprocessing:

* Converts any percent-related columns (e.g., "pct", "perc", "%”) to proper percentage strings.
* Detects the number of categorical columns if not provided.

2. Optional Spreading:

* Reshapes data wide by a selected category level if spread=TRUE, converting counts (n) into n_<category>.

3. Sorting:

* Orders rows based on total counts (n) if specified.

4. Aggregation:

* Adds a total row at the top of the table (with sums, means, max, etc.).
* Computes additional aggregates as specified in add_agg_columns.

6. Column Ordering & Cleaning:

* Automatically orders non-category columns by defined logic.

* Optionally removes specific columns (via remove_cols).

7. Styling (with flextable):
* Applies colors from a custom scheme (choose_color_scheme() must be defined externally).
* Formats column widths, row heights, padding, alignment, borders, and background colors.
* Customizes header labels and optionally merges header cells.

Output:

Returns a flextable object with:
* Styled and aligned headers and body rows.
* Aggregated top row labeled with total_label.
* Reshaped structure if spread = TRUE.
* Clean and customizable display of count/percent/statistics columns.
