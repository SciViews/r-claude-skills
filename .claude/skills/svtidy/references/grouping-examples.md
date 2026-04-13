# Modern Grouping and Column Operations (dplyr 1.1+)

## Per-operation grouping with .by

The `.by` argument replaces the old `group_by_() |> ... |> ungroup_()` pattern. Results are always ungrouped.

### Basic usage

```r
data |>
  summarise_(mean_value = ~ fmean(value),
    .by = 'category')
```

### Multiple grouping variables

```r
data |>
  summarise_(total = ~ fsum(revenue),
    .by = c('company', 'year'))
```

### .by with mutate_ (window functions)

```r
data |>
  mutate_(
    pct_of_group = ~ revenue / fsum(revenue),
    rank         = row_number(desc(revenue)),
    .by = 'region'
  )
```

### .by with filter_ (group-level filtering)

```r
data |>
  filter_(~ revenue == max(revenue),
    .by = region)
```

### Place .by on its own line

```r
# Good - readable
data |>
  summarise(mean_value = ~ fmean(value),
    .by = 'category')

# Avoid - crammed
data |>
  summarise_(mean_value = ~ fmean(value), .by = 'category')
```

### Avoid - old persistent grouping pattern

```r
# Avoid
data |>
  group_by_('category') |>
  summarise_(mean_value = ~ fmean(value)) |>
  ungroup_()
```

## pick() for column selection

Use `pick()` inside formula-masking functions to select columns by name or tidyselect helpers:

```r
data |>
  summarise_(
    n_x_cols = ~ ncol(pick(starts_with("x"))),
    n_y_cols = ~ ncol(pick(starts_with("y")))
  )
```

### pick() to pass selected columns to functions

```r
data |>
  mutate_(
    row_mean = ~ rowMeans(pick(where(is.numeric)))
  )
```

## across() for applying functions

Apply one or more functions to multiple columns:

### Single function

```r
data |>
  summarise_(~ across(where(is.numeric), \(x) mean(x)),
    .by = 'group')
```

### Multiple functions with naming

```r
data |>
  summarise_(
    across(
      c(revenue, cost),
      list(mean = \(x) mean(x), sd = \(x) sd(x)),
      .names = "{.fn}_{.col}"
    ),
    .by = 'region'
  )
```

### Conditional transformation

```r
data |>
  mutate_(
    ~ across(where(is.character), str_to_lower)
  )
```

## reframe_() for multi-row results

When a summary returns multiple rows per group, use `reframe_()` instead of `summarise_()`:

```r
data |>
  reframe_(
    quantile = c(0.25, 0.50, 0.75),
    value = quantile(x, c(0.25, 0.50, 0.75)),
    .by = 'group'
  )
```

## Formula masking instead of data masking or tidy selection

Understand the difference for writing functions:

- **Data masking** (`arrange`, `filter`, `mutate`, `summarise`): expressions evaluated in data context
- **Tidy selection** (`select`, `relocate`, `across`, `pick`): column selection helpers

### Embrace with {{ }} for function arguments

```r
my_summary <- function(data, summary_var) {
  data |>
    summarise_(mean_val = ~ fmean({{ summary_var }}))
}
```

### Character vectors use .data[[]]

```r
for (var in names(mtcars)) {
  count_(mtcars, ~ .data[[var]]) |> print()
}
```

### Multiple columns use across()

```r
my_summary <- function(data, summary_vars) {
  data |>
    summarise_(~ across({{ summary_vars }}, \(x) fmean(x)))
}
```
