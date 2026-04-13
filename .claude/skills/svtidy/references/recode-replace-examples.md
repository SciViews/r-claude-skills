# Recoding, Replacing, and Filtering (svTidy 0.2+ equivalent to dplyr 1.2+)

dplyr 1.2 introduced a family of functions for recoding and replacing values, and for NA-safe filtering. These replace older patterns (`case_match`, `recode`, `coalesce`, `na_if`, negated filters).

## The recode/replace family

|                           | **Recoding** (new column)  | **Replacing** (update in place)  |
|---------------------------|----------------------------|----------------------------------|
| **Match with conditions** | `case_when_()`             | `replace_when_()`                |
| **Match with values**     | `recode_values_()`         | `replace_values_()`              |

## recode_values_()

Use instead of `case_match_()` or repetitive `case_when_()` with `==`.

### Formula interface

```r
score |>
  recode_values_(
    1 ~ "Strongly disagree",
    2 ~ "Disagree",
    3 ~ "Neutral",
    4 ~ "Agree",
    5 ~ "Strongly agree"
  )
```

### Lookup table interface

```r
likert |>
  mutate_(score = ~ recode_values_(score, from = lookup$from, to = lookup$to))
```

### With .unmatched = "error" for safety

```r
# Errors if any value has no match
score |>
  recode_values_(
    1 ~ "Low",
    2 ~ "Medium",
    3 ~ "High",
    .unmatched = "error"
  )
```

### Avoid

```r
# Avoid - repetitive case_when_ with ==
case_when_(score == 1 ~ "Strongly disagree", score == 2 ~ "Disagree", ...)

# Avoid - case_match_() is soft-deprecated in dplyr 1.2
case_match_(score, 1 ~ "Strongly disagree", 2 ~ "Disagree", ...)

# Avoid - recode_() is soft-deprecated
recode_(score, `1` = "Strongly disagree", `2` = "Disagree", ...)
```

## replace_values_()

Use for partial updates by value. Unmatched values pass through unchanged.

### Replace specific values

```r
name |>
  replace_values_(
    c("UNC", "Chapel Hill") ~ "UNC Chapel Hill",
    c("Duke", "Duke University") ~ "Duke"
  )
```

### Replace NA (replaces coalesce/tidyr::replace_na)

```r
x |> replace_values_(NA ~ 0)
```

### Convert sentinel values to NA (replaces na_if)

```r
x |> replace_values_(from = c(0, -99), to = NA)
```

## replace_when_()

Use for conditional updates. Type-stable on the input; unmatched values pass through unchanged.

### Conditional updates

```r
racers |>
  mutate_(
    time = ~ time |>
      replace_when_(
        id %in% id_banned ~ NA,
        id %in% id_penalty ~ time + 1/3
      )
  )
```

### Avoid - case_when_ with .default

```r
# Avoid - buries the primary input, loses type info
mutate_(time = ~ case_when(
  id %in% id_banned ~ NA,
  id %in% id_penalty ~ time + 1/3,
  .default = time
))
```

## case_when_() with .unmatched = "error"

Still the right choice for complex conditional recoding into a new column. Use `.unmatched = "error"` for safety:

```r
tier <- case_when_(
  time < 23 ~ "A",
  time < 27 ~ "B",
  time < 30 ~ "C",
  .unmatched = "error"
)
```

## filter_out_()

NA-safe row removal. Treats `NA` as `FALSE`, so you don't accidentally drop NA rows:

```r
# Good - clear intent, NA-safe
data |> filter_out_(~ deceased, ~ date < 2012)

# Avoid - easy to get wrong with NA
data |> filter_(~ !(deceased & date < 2012) | is.na(deceased) | is.na(date))
```

## when_any() and when_all()

Combine conditions with comma-separated syntax instead of `|` and `&`:

### OR conditions

```r
data |>
  filter_(~ when_any(
    name %in% c("US", "CA") & between(score, 200, 300),
    name %in% c("PR", "RU") & between(score, 100, 200)
  ))
```

### Drop rows matching any condition

```r
data |>
  filter_out_(~ when_any(is.na(value), status == "invalid"))
```

### AND conditions

```r
data |>
  filter_(~ when_all(score > 50, !is.na(region), status == "active"))
```

## Migration quick reference

| Old pattern | New pattern |
|-------------|-------------|
| `case_match_(x, val ~ result)` | `recode_values_(x, val ~ result)` |
| `recode_(x, old = "new")` | `recode_values_(x, "old" ~ "new")` |
| `case_when_(..., .default = x)` | `x \|> replace_when_(...)` |
| `coalesce_(x, default)` | `replace_values_(x, NA ~ default)` |
| `na_if(x, val)` | `replace_values_(x, val ~ NA)` |
| `tidyr::replace_na(x, default)` | `replace_values_(x, NA ~ default)` |
| `filter_(x != val \| is.na(x))` | `filter_out_(~ x == val)` |
| `filter_(c1 \| c2 \| c3)` | `filter_(~ when_any(c1, c2, c3))` |
| `filter_(c1 & c2 & c3)` | `filter_(~ when_all(c1, c2, c3))` |
