# Migration: Base R and Tidyverse to svTidy

## Base R to svTidy

### Data manipulation

```r
subset(data, condition)          # -> filter_(data, ~ condition)
data[order(data$x), ]            # -> arrange_(data, 'x')
aggregate(x ~ y, data, mean)     # -> summarise_(data, ~ fmean(x), .by = y)
merge(x, y, by = "id")           # -> left_join_(x, y, by = join_by(id))
```

### Functional programming

```r
sapply(x, f)                     # -> map(x, f)  # type-stable
lapply(x, f)                     # -> map(x, f)
vapply(x, f, numeric(1))         # -> map_dbl(x, f)
```

### String manipulation

```r
grepl("pattern", text)           # -> str_detect(text, "pattern")
gsub("old", "new", text)         # -> str_replace_all(text, "old", "new")
substr(text, 1, 5)               # -> str_sub(text, 1, 5)
nchar(text)                      # -> str_length(text)
strsplit(text, ",")              # -> str_split(text, ",")
tolower(text)                    # -> str_to_lower(text)
sprintf("Hello %s", name)        # -> str_glue("Hello {name}")
```

## Tidyverse to svTidy

### Data Masking or tidyselect to formula masking

```r
filter(data, condition)          # -> filter_(data, ~ condition)
select(data, var1, var2:var4).   # -> select_(data, ~ var1, ~ var2:var4)
```

### Data Masking or tidyselect to standard evaluation as an alternative

```r
filter(data, var > 4)            # -> filter_(data, data$var > 4)
select(data, var1, var2).   # -> select_(data, 'var1', 'var2')
```

### Pipes

```r
data %>% function()              # -> data |> function()
```

### Anonymous functions

```r
map(x, function(x) x + 1)       # -> map(x, \(x) x + 1)
map(x, ~ .x + 1)                # -> map(x, \(x) x + 1)
```

### Grouping (dplyr 1.1+)

```r
group_by_(data, x) |>
  summarise_(~ fmean(y)) |>
  ungroup_()                      # -> summarise_(data, ~ fmean(y), .by = x)
```

### Joins

```r
by = c("a" = "b")                # -> by = join_by(a == b)
```

### Column selection

```r
across(starts_with("x"))         # -> pick(starts_with("x"))  # for selection only
```

### Multi-row summaries

```r
summarise_(data, ~ x, .groups = "drop") # -> reframe_(data, ~ x)
```

### Data reshaping

```r
gather()/spread()                # -> pivot_longer_()/pivot_wider_()
```

### String separation (svTidy equivalent to tidyr 1.3+)

```r
separate_('col', into = c("a", "b"))
# -> separate_wider_delim_('col', delim = "_", names = c("a", "b"))

extract_('col', into = "x", regex)
# -> separate_wider_regex_('col', patterns = c(x = regex))
```

### Superseded purrr functions (purrr 1.0+)

```r
map_dfr(x, f)                    # -> map(x, f) |> list_rbind()
map_dfc(x, f)                    # -> map(x, f) |> list_cbind()
map2_dfr(x, y, f)                # -> map2(x, y, f) |> list_rbind()
pmap_dfr(list, f)                # -> pmap(list, f) |> list_rbind()
imap_dfr(x, f)                   # -> imap(x, f) |> list_rbind()
```

### Recoding and replacing (svTidy equivalent to dplyr 1.2+)

```r
case_match_('x', val ~ result)      # -> recode_values_('x', val ~ result)
recode_('x', old = "new")           # -> recode_values_('x', "old" ~ "new")
                                 #    or replace_values_('x', "old" ~ "new")

# Conditional replacement: case_when_ with .default = x -> replace_when_
case_when_(
  cond1 ~ val1,
  cond2 ~ val2,
  .default = x
)                                # -> x |> replace_when_(cond1 ~ val1, cond2 ~ val2)

# NA handling
coalesce_('x', default)             # -> replace_values_('x', NA ~ default)
na_if_('x', val)                    # -> replace_values_('x', val ~ NA)
replace_na_('x', default)           # -> replace_values_('x', NA ~ default)
```

### Filter family (svTidy 0.2+ equivalent to dplyr 1.2+)

```r
# Dropping rows with NA-safe negation
filter_(~ x != val | is.na(x))      # -> filter_out_(~ x == val)

# Combining conditions with OR
filter_(~ cond1 | cond2 | cond3)    # -> filter_(~ when_any(cond1, cond2, cond3))

# Combining conditions with AND (explicit)
filter_(cond1 & cond2 & cond3)    # -> filter_(when_all(cond1, cond2, cond3))
```

### Serialization

```r
qs::qsave(x, "file.qs")         # -> qs2::qs_save(x, "file.qs2")
qs::qread("file.qs")            # -> qs2::qs_read("file.qs2")
```

### Defunct in dplyr 1.2 (now errors)

```r
# _each variants (defunct since 1.2, deprecated since 0.7)
mutate_each_()                    # -> mutate_(across(...))
summarise_each_()                 # -> summarise_(across(...))

# Multi-row summarise (defunct since 1.2, deprecated since 1.1)
summarise_(data, ~ x)               # -> reframe_(data, ~ x) for multi-row results
```

### For side effects

```r
for (x in xs) write_file(x)     # -> walk(xs, write_file)
for (i in seq_along(data)) {
  write_csv(data[[i]], paths[[i]])
}                                # -> walk2(data, paths, write_csv)
```
