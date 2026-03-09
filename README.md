# R Programming Skills for Claude Code

A comprehensive collection of Claude Code skills specifically designed for R programming, data science, and statistical computing workflows.

## 🎯 Overview

This repository contains production-ready skills that enhance Claude Code's capabilities when working with R projects. These skills provide expert guidance on R programming patterns, package development, data visualization, Bayesian analysis, performance optimization, and more.

## 📦 Available Skills

### Core R Skills

- **[r-style-guide](/.claude/skills/r-style-guide/)** - R style guide covering naming conventions, spacing, layout, and function design best practices
- **[r-performance](/.claude/skills/r-performance/)** - Performance optimization including profiling, benchmarking, vctrs, and optimization strategies
- **[r-oop](/.claude/skills/r-oop/)** - Object-oriented programming guide for S7, S3, S4, and vctrs systems
- **[r-package-development](/.claude/skills/r-package-development/)** - Package development covering dependencies, API design, testing, and documentation

### Data Science & Visualization

- **[tidyverse-patterns](/.claude/skills/tidyverse-patterns/)** - Modern tidyverse patterns including pipes, joins, grouping, purrr, and stringr
- **[ggplot2](/.claude/skills/ggplot2/)** - Expert data visualization with grammar of graphics, geoms, themes, scales, and faceting
- **[dm-relational](/.claude/skills/dm-relational/)** - Relational data modeling with {dm} package for multi-table data models
- **[rlang-patterns](/.claude/skills/rlang-patterns/)** - Metaprogramming patterns for data-masking, injection operators, and dynamic dots

### Statistics & Analysis

- **[r-bayes](/.claude/skills/r-bayes/)** - Bayesian inference patterns using brms, including multilevel models and marginal effects

### Development Workflow

- **[tdd-workflow](/.claude/skills/tdd-workflow/)** - Test-driven development workflow using testthat with 80%+ coverage enforcement
- **[r-shiny](/.claude/skills/r-shiny/)** - Expert Shiny app development covering reactive programming, UI design, modules, and performance

### Meta Skills

- **[skillMaker](/.claude/skills/skillMaker/)** - Create new Claude Code skills following best practices (used to generate these skills!)

## 🚀 Installation

### Project-Level Installation

Clone this repository into your R project:

```bash
cd your-r-project/
git clone https://github.com/yourusername/claude-r-skills.git .claude/skills
```

Or add as a git submodule:

```bash
git submodule add https://github.com/yourusername/claude-r-skills.git .claude/skills
```

### System-Wide Installation

Install skills globally for use across all projects:

```bash
# Clone the repository
git clone https://github.com/yourusername/claude-r-skills.git

# Copy skills to Claude's system directory
cp -r claude-r-skills/.claude/skills/* ~/.claude/skills/
```

## 📖 Usage

### Manual Invocation

Most skills can be invoked directly using slash commands:

```bash
/r-style-guide     # Get R style guidance
/ggplot2           # ggplot2 visualization help
/tdd-workflow      # Start test-driven development
/skillMaker        # Create a new skill
```

### Automatic Triggering

Skills automatically activate based on context:

- **Import detection**: Skills trigger when relevant packages are imported
- **File patterns**: Activated by file types (.R, .Rmd, tests/, etc.)
- **Keywords**: Mentioning specific terms (e.g., "ggplot", "shiny app", "package development")
- **Code patterns**: Detecting tidyverse pipes, ggplot layers, test files, etc.

### Example Workflows

**Creating a ggplot visualization:**
```r
# Simply start coding - the skill activates automatically
library(ggplot2)
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point()  # Claude provides expert ggplot2 guidance
```

**Developing an R package:**
```bash
/r-package-development  # Manually invoke for comprehensive guidance
```

**Building a Shiny app:**
```r
library(shiny)  # Auto-triggers r-shiny skill
# Claude provides reactive programming patterns, UI best practices, etc.
```

## 🏗️ Repository Structure

```
.claude/skills/
├── r-style-guide/
│   ├── SKILL.md           # Main skill definition
│   ├── README.md          # User documentation
│   └── examples/          # Code examples
├── ggplot2/
│   ├── SKILL.md
│   ├── templates/         # Plot templates
│   └── references/        # Geom reference guide
├── tidyverse-patterns/
│   └── SKILL.md
├── r-shiny/
│   ├── SKILL.md
│   ├── templates/         # App templates
│   └── examples/          # Complete app examples
└── skillMaker/
    ├── SKILL.md
    ├── templates/
    ├── examples/
    └── references/
```

## 🛠️ Creating Custom Skills

Use the included `skillMaker` skill to create your own:

```bash
/skillMaker
```

Follow the guided workflow to generate production-ready skills. See [CLAUDE.md](CLAUDE.md) for detailed skill development guidelines.

## 🤝 Contributing

Contributions are welcome! To add or improve a skill:

1. Fork this repository
2. Create a feature branch: `git checkout -b feature/new-skill`
3. Use `/skillMaker` to generate your skill following best practices
4. Test thoroughly in real-world scenarios
5. Submit a pull request with clear description

### Contribution Guidelines

- Follow the [skill creation conventions](CLAUDE.md#skill-creation-workflow)
- Include concrete examples and trigger phrases
- Test both manual and automatic invocation
- Document all features in README.md
- Keep SKILL.md under 500 lines (use supporting files for larger skills)

## 📋 Requirements

- **Claude Code CLI** - Install from [claude.ai/code](https://claude.ai/code)
- **R** (version 4.0+) - For R-specific skills
- **RStudio** (optional) - Enhanced integration with R skills

## 📄 License

MIT License - See [LICENSE](LICENSE) for details

## 🔗 Resources

- [Claude Code Documentation](https://docs.claude.ai/code)
- [Skill Development Guide](CLAUDE.md)
- [SkillMaker Architecture](/.claude/skills/skillMaker/ARCHITECTURE.md)
- [R Project](https://www.r-project.org/)
- [Tidyverse](https://www.tidyverse.org/)
- [Posit Community](https://community.rstudio.com/)

## 🙏 Acknowledgments

- Built with [Claude Code](https://claude.ai/code)
- Inspired by the R community's best practices
- Based on conventions from tidyverse, brms, shiny, and other excellent R packages

## 📮 Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/claude-r-skills/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/claude-r-skills/discussions)
- **Updates**: Watch this repository for new skills and improvements

---

Made with ❤️ for the R community | Powered by Claude Code
