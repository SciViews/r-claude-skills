# svtidy skill

This skill is derived from https://github.com/statzhero/tidy-r-skill commit fc39f29 on 2026-03-31 and reworked in the {svTidy} style, but look also at tidyverse-expert and tidyverse-patterns to build it solidly.

An LLM skill for an alternate tidyverse-like style using {svTidy} with R 4.4+ and svTidy 0.2+.

## What does it do?

When you ask an LLM to write R code, it draws on its training data, which mixes base R, old StackOverflow posts, deprecated tidyverse APIs, and current best practice. This skill gives the model a structured reference for {svTidy} patterns so it produces clean, idiomatic code with less back-and-forth.

The skill covers: native pipe and lambda syntax, `join_by()` joins, `.by` grouping, the dplyr 1.2 `recode()`/`replace()` family, tidy selection, `stringr`, error handling with `cli`, and migration from base R or older tidyverse APIs.

## Installation

### Claude Code (one command)

Clone directly into your skills directory:

```bash
git clone https://github.com/SciViews/r-claude-skills.git ~/.claude/skills/r-claude-skills
```

That's it. The skill is available immediately as `/svtidy` in any Claude Code session.

If you prefer not to use the terminal, you can add skills from the Claude desktop app:

1. [Download this repository as a ZIP](https://github.com/SciViews/r-claude-skills/archive/refs/heads/main.zip) from GitHub.
2. Open the Claude desktop app and switch to the **Code** tab.
3. Click **Customize** in the left sidebar, then select **Skills**.
4. Click the **+** button, choose **Upload a skill**, and select the ZIP file.

### Codex

Clone into your user skills directory (available across all projects):

```bash
git clone https://github.com/SciViews/r-claude-skills.git ~/.agents/skills/r-claude-skills
```

If you prefer not to use the terminal, [download the ZIP](https://github.com/SciViews/r-claude-skills/archive/refs/heads/main.zip), unzip it, and move the folder to `~/.agents/skills/r-claude-skills/` inside your project.

### Other LLMs

Paste the contents of `SKILL.md` into your system prompt or attach it as context. The reference files in `references/` can be appended when you need coverage of a specific topic.

## Test the skill

After installing, try this prompt in Claude Code:

```
/svtidy Rewrite this using svTidy:
penguins %>% group_by(species) %>% summarise(avg_bill = mean(bill_length_mm)) %>% ungroup()
```

The skill should guide the model toward using `summarise_()` with `.by` grouping and native pipe.

## Acknowledgements

This skill stands on the shoulders of giants, but I built it over many iterations and can no longer trace any single influence. I regret not keeping better records. If you recognize your ideas here, please open an issue so I can credit you properly.

## License

CC-BY-4.0
