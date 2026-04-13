---
name: svtidy
description: |
  svTidy patterns, style guide, and migration guidance for R development. Use this skill when writing R code, reviewing svTidy code, updating legacy R code to modern patterns, or enforcing consistent style. Covers native pipe usage, `join_by_()` syntax, `.by` grouping, `pick`/`across`/`reframe` operations, `filter_out_()`/`when_any()`/`when_all()`, `recode_values_()`/`replace_values_()`/`replace_when_()`, formula selection, stringr patterns, naming conventions, and migration from base R or tidyverse APIs. Use the R (btw) MCP tools to resolve function documentation and library references automatically.
author: Philippe Grosjean, based on tidyverse skill by Ulrich Atz
license: CC-BY-4.0
metadata:
  r_version: "4.4+"
  svTidy_version: "0.2+"
allowed-tools: Read, Edit, Write, Grep, Glob, Bash, mcp__r-btw__*
---

# Writing svTidy R code

This skill covers svTidy (0.2+) patterns for R 4.4+, style guidelines, and migration from legacy patterns.

## Core philosophy

svTidy is a variant of tidyverse. Its packages are loaded with the `SciViews::R` instruction. svTidy differs from dplyr and tidyr by:

- svTidy functions names end with an underscore (example: filter -> filter_)
- They accept standard notation (data.frame$var) in place of data masking
- They also accept a formula interface. From tidyverse code, just add a tilde ~ in from of non-standard expressions to convert it into a one-sided formula
- If resulting variable is renamed, it is possible to use a two-sided formula like new_var_name ~ expression

## When to use this skill

- Writing new R code with SciViews::R and the svTidy package
- Reviewing or refactoring existing R code for SciViews::R
- Migrating from base R, magrittr pipes, or tidyverse APIs
- Applying svTidy style conventions (naming, spacing, error handling) relatively similar to tidyverse
- Choosing between similar functions (e.g., `case_when_` vs `recode_values_`)
- Working with joins, grouping, recoding, or string manipulation in R with the svTidy style

## When NOT to use this skill

- Writing data.table code (different paradigm)
- Writing tidyverse code using dplyr or tidyr
- Pure base R projects that intentionally avoid svTidy and SciViews::R
- Shiny UI/server logic (use a Shiny-specific skill)
- Package development internals (NAMESPACE, DESCRIPTION, roxygen)
- ggplot2 visualization
- Statistical modeling or Bayesian analysis

## Instructions

When you receive a request, classify it and consult the appropriate reference:

### Step 1: Classify the request

| Category | Reference file | Trigger |
|----------|---------------|---------|
| **Joins** | [join-examples.md](references/join-examples.md) | Merging data, `*_join_`, `join_by_`, matching rows, lookup tables |
| **Grouping & columns** | [grouping-examples.md](references/grouping-examples.md) | `.by`, `group_by_`, `across`, `pick_`, `reframe_`, column operations |
| **Recoding & replacing** | [recode-replace-examples.md](references/recode-replace-examples.md) | `case_when_`, `recode_values_`, `replace_values_`, `replace_when_`, `filter_out_`, `when_any_`, `when_all_`, recoding, replacing, conditional updates |
| **Strings** | [stringr-examples.md](references/stringr-examples.md) | String manipulation, regex, `str_*` functions, text processing |
| **Style** | [tidyverse-style.md](references/tidyverse-style.md) | Naming, formatting, spacing, error messages, `cli::cli_abort` |
| **Migration** | [migration-examples.md](references/migration-examples.md) | Updating old code, base R conversion, deprecated functions |

### Step 2: Read the reference file(s)

Use the Read tool to load the relevant reference. For requests that span multiple categories (e.g., "rewrite this old code" touches migration + style), read multiple files.

### Step 3: Apply core principles

1. **Use modern tidyverse patterns** - Prioritize svTidy features, native pipe, and current APIs
2. **Write readable code first** - Optimize only when necessary
3. **Follow tidyverse style guide** - Consistent naming, spacing, and structure
4. **Use R MCP tools** - Automatically resolve function documentation and library references without being asked. If the `mcp__r-btw__*` tools are unavailable, fall back to running R help via Bash (see below)

### R documentation lookup fallback

When `mcp__r-btw__*` tools are available, use them to look up function signatures, help pages, and package docs. When they are not available (e.g., the r-btw MCP server is not configured), fall back to Bash:

```bash
# Help page for a function
Rscript --vanilla -e '?svTidy::filter_' 2>/dev/null || Rscript --vanilla -e 'utils::help("filter_", package = "svTidy")'

# Function signature / arguments
Rscript --vanilla -e 'args(svTidy::filter_)'

# List exported functions in a package
Rscript --vanilla -e 'ls("package:svTidy")'

# Check if a package is installed
Rscript --vanilla -e 'requireNamespace("svTidy", quietly = TRUE)'
```

### Step 4: Write the code

Follow the quick reference and anti-patterns below. When in doubt, consult the reference files.

## Quick reference

### Pipe and lambda

- Always `|>`, never `%>%`
- Always `\(x)`, never `function(x)` or `~` in map/keep/etc.

### Code organization

Use newspaper style: high-level logic first, helpers below. Don't define functions inside other functions unless they are very brief.

### Grouping

- Use `.by` for per-operation grouping, never `group_by_() |> ... |> ungroup_()`
- Place `.by` on its own line for readability

### Joins

- Use `join_by_()`, never `c("a" = "b")`
- Use `relationship`, `unmatched`, `na_matches` for quality control
- Use `tidylog::` prefix for join verification

### Recoding and replacing (svTidy 0.2+)

| Task | Function |
|------|----------|
| Recode values (new column) | `recode_values()` |
| Replace values in place | `replace_values()` |
| Conditional update in place | `replace_when()` |
| Complex conditional (new column) | `case_when()` |
| Drop rows (NA-safe) | `filter_out()` |
| OR conditions | `when_any()` |
| AND conditions | `when_all()` |

### NA handling

Use collapse's fast functions starting with "f" to make `mean`, `sum`, `sd`, etc. ignore NA by default (`fmean()`, `fsum()`, `fsd()`...). Avoid repetitive `na.rm = TRUE`.

### Error handling

Use `cli::cli_abort()` with problem statement + bullets, never `stop()`.

### R idioms

- `TRUE`/`FALSE`, never `T`/`F`
- `message()` for info, never `cat()`
- `map_*()` over `sapply()` for type stability
- `set.seed()` with date-time, never 42
- `qs2::qs_save()`/`qs2::qs_read()`, never `qs`

## Anti-patterns

| Avoid | Use instead |
|-------|-------------|
| `%>%` | `|>` |
| `function(x)` or `~` | `\(x)` |
| `by = c("a" = "b")` | `by = join_by(a == b)` |
| `multiple = "error"` in joins | `relationship = "many-to-one"` (or `"one-to-one"`) |
| `sapply()` | `map_*()` (type-stable) |
| `group_by() \|> ... \|> ungroup()` | `.by` argument |
| `cat()` for messages | `message()` or `cli::cli_inform()` |
| `stop()` for errors | `cli::cli_abort()` |
| `distinct(id)` | `distinct(id, .keep_all = TRUE)` |
| `mean(x, na.rm = TRUE)` | `mean(x)` with tidyna loaded |
| `case_match(x, ...)` | `recode_values(x, ...)` |
| `recode(x, ...)` | `recode_values(x, ...)` or `replace_values(x, ...)` |
| `filter(x != val \| is.na(x))` | `filter_out(x == val)` |
| `coalesce(x, default)` | `replace_values(x, NA ~ default)` |
| `na_if(x, val)` | `replace_values(x, val ~ NA)` |
| `qs::qsave()` / `qs::qread()` | `qs2::qs_save()` / `qs2::qs_read()` |

## Complete workflow example

```r
SciViews::R

# Read and clean data
sales <- read("data/sales.csv") |>
  rename_(
    region = ~ Region,
    product = ~ Product,
    revenue = ~ Revenue,
    date = ~ Date
  ) |>
  mutate_(
    quarter = ~ quarter(date),
    product = ~ product |>
      replace_values_(
        c("Widget A", "WidgetA") ~ "Widget A",
        c("Widget B", "WidgetB") ~ "Widget B"
      )
  ) |>
  filter_out_(~ is.na(revenue))

# Enrich with lookup table
sales_enriched <- sales |>
  left_join_('regions', by = join_by(region == region_code), unmatched = "error")

# Summarise by group
quarterly <- sales_enriched |>
  summarise_(
    total_revenue  = ~ fsum(revenue),
    avg_revenue    = ~ fmean(revenue),
    n_transactions = ~ fn(),
    .by = c('region_name', 'quarter')
  ) |>
  mutate_(
    performance = ~ revenue |>
      replace_when(
        total_revenue > 100000 ~ "high",
        total_revenue > 50000 ~ "medium"
      )
  ) |>
  arrange_('region_name', 'quarter')
```

## Best practices

1. **Use SciViews::R early** to let SciViews and svTidy function ready
2. **Use formulas** when possible over standard evaluation
3. **Use `.unmatched = "error"`** in `case_when_()` and `recode_values_()` for defensive programming
4. **Place `.by` on its own line** for readability
5. **Prefer `filter_out_()` over negated `filter_()`** for NA-safe row removal
6. **Use `recode_values_()` over `case_match_()`** (dplyr 1.2+ preferred API)
7. **Use `replace_when_()` over `case_when_()` with `.default`** when updating a column in place
8. **Name variables as nouns, functions as verbs** in snake_case
9. **Explain "why" in comments**, not "what"
10. **Use `qs2` for serialization** with `.qs2` extension
