# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This repository contains Claude Code skills, specifically the `skillMaker` skill - a comprehensive skill generator that creates production-ready Claude Code skills following best practices.

## Architecture Overview

### Skill Structure
All skills live in `.claude/skills/` directory. Each skill is organized as:

```
.claude/skills/<skill-name>/
├── SKILL.md              # Main skill definition with YAML frontmatter
├── README.md             # User-facing documentation (optional)
├── templates/            # Code/content generation templates (optional)
├── examples/             # Complete example outputs (optional)
├── references/           # Detailed reference documentation (optional)
└── scripts/              # Helper shell scripts (optional)
```

### SkillMaker Skill Components

The main skill in this repo is `skillMaker`, which consists of:
- **SKILL.md** (558 lines): Core skill logic with 5-phase workflow
- **templates/basic-skill-template.md**: Starting templates for 5 skill types
- **examples/skill-structures.md**: Complete working examples
- **references/skill-patterns.md**: Comprehensive pattern catalog
- **README.md**: User documentation
- **ARCHITECTURE.md**: Visual guide and system diagrams

Total: ~2,800 lines across 5 files

## Skill Creation Workflow

SkillMaker follows a 5-phase process:

1. **Requirements Gathering**: Questions about purpose, triggers, invocation control, tools, complexity
2. **Structure Decision**: Simple (SKILL.md only) vs bundled (with supporting files)
3. **Content Generation**: Frontmatter, instructions, examples, supporting files
4. **Optimization**: Context efficiency, tool restrictions, invocation control
5. **Validation**: YAML syntax, file references, shell commands, testing

## Skill Types

SkillMaker supports creating 5 skill types:

1. **Reference Skills**: Background knowledge (user-invocable: false)
2. **Task Workflow Skills**: User-invoked processes (disable-model-invocation: true)
3. **Dynamic Context Skills**: Skills using live data via !`command`
4. **Bundled Skills**: Complex skills with templates/scripts/examples
5. **Research Skills**: Isolated exploration (context: fork, agent: Explore)

## Key Conventions

### Frontmatter Configuration

Every SKILL.md requires YAML frontmatter:
```yaml
---
name: skill-name                    # Required: kebab-case
description: [Action description]   # Required: Specific trigger phrases
version: 1.0.0                      # Optional
disable-model-invocation: true      # Optional: User-only
user-invocable: false               # Optional: Claude-only
allowed-tools: Read, Write, Edit    # Optional: Tool restrictions
model: claude-3-5-haiku             # Optional: Model override
context: fork                       # Optional: Isolated execution
agent: Explore                      # Optional: Agent type
---
```

### Description Writing Formula
- Start with verb (Create, Analyze, Generate, Review)
- Include 3+ specific trigger phrases users would say
- List relevant keywords
- Mention the domain/topic

Example:
```yaml
description: Create new Claude Code skills following best practices. Use when user asks to "create a skill", "make a new skill", "build a skill", "generate skill", mentions "skill maker", discusses creating Claude Code automations, or wants help building custom Claude skills.
```

### File Size Guidelines
- **< 200 lines**: Single SKILL.md
- **200-500 lines**: SKILL.md + examples/
- **> 500 lines**: Split into SKILL.md + supporting directories

Keep main SKILL.md under 500 lines to preserve context efficiency.

### Dynamic Context Injection

Use shell command substitution for live data:
```markdown
!`git branch --show-current 2>/dev/null || echo "Not a git repo"`
```

Always include error handling with `2>/dev/null || echo "fallback"`

### Tool Restrictions

Specify minimum necessary tools:
```yaml
# Read-only
allowed-tools: Read, Grep, Glob

# Safe modification
allowed-tools: Read, Write, Edit

# Specific commands only
allowed-tools: Read, Write, Bash(npm test:*), Bash(git *)
```

### Invocation Control

```yaml
# Default: Both user and Claude can invoke
# (omit both flags)

# User-only: For side-effect tasks (deploy, commit, delete)
disable-model-invocation: true

# Claude-only: For background knowledge/conventions
user-invocable: false
```

## Testing Skills

### Manual Invocation
```bash
/skillMaker
```

### Testing Triggers
Use exact phrases from the skill's description to test auto-invocation.

### Validation Checklist
- [ ] Valid YAML frontmatter
- [ ] Description has 3+ trigger phrases
- [ ] Tool restrictions match actual needs
- [ ] File references all exist
- [ ] Shell commands have error handling
- [ ] Examples are concrete
- [ ] SKILL.md under 500 lines

## Common Patterns

### Argument Handling
```yaml
# In SKILL.md content:
$0 - First argument
$1 - Second argument
${2:-default} - Third argument with default
```

### File References
```markdown
See [templates/example.md](templates/example.md)
Script: `${CLAUDE_SKILL_DIR}/scripts/helper.sh`
```

### Conditional Context
```markdown
Package manager: !`[ -f "package-lock.json" ] && echo "npm" || [ -f "yarn.lock" ] && echo "yarn" || echo "Unknown"`
```

## Development Guidelines

### When Modifying SkillMaker

1. **Keep main SKILL.md focused**: Move detailed content to supporting files
2. **Maintain pattern consistency**: Follow established patterns in references/
3. **Update all examples**: If changing workflow, update examples/
4. **Validate YAML**: Ensure frontmatter remains valid
5. **Test thoroughly**: Test manual and auto-invocation

### When Creating New Skills with SkillMaker

1. Invoke with `/skillMaker` or mention "create a skill"
2. Answer guided questions about purpose and requirements
3. Review generated structure and content
4. Validate against checklist
5. Test invocation methods
6. Iterate based on testing

### File Organization

- Keep supporting files logically organized
- Use descriptive filenames
- Reference all supporting files from main SKILL.md
- Include examples for complex patterns

## Installation

### Project-level
Skills in `.claude/skills/` are available in this project only.

### System-wide
```bash
cp -r .claude/skills/skillMaker ~/.claude/skills/
```
Makes skillMaker available in all projects.

## Troubleshooting

### Skill doesn't auto-trigger
- Add more specific trigger phrases to description
- Verify no `disable-model-invocation: true` (unless intended)
- Test with exact phrases from description

### Skill not in autocomplete
- Check SKILL.md in correct location
- Verify YAML frontmatter is valid
- Ensure no `user-invocable: false` (unless intended)

### Shell commands fail
- Add error handling: `2>/dev/null || echo "fallback"`
- Test commands independently
- Verify commands available in environment

## Quality Standards

### Well-Formed Skills Have
✅ Specific, trigger-rich descriptions (3+ phrases)
✅ Appropriate file structure for complexity
✅ Tool restrictions matching actual needs
✅ Error handling in shell commands
✅ Supporting files properly referenced
✅ Concrete examples (2-3 minimum)
✅ Optimization patterns applied
✅ Complete validation

### Avoid
❌ Vague descriptions ("helps with tasks")
❌ Massive single-file skills (>500 lines without supporting files)
❌ Over-broad tool permissions
❌ Missing error handling
❌ Broken file references
❌ Abstract or unclear examples
❌ Overlapping skill triggers

## Resources

- Main skill: `.claude/skills/skillMaker/SKILL.md`
- Templates: `.claude/skills/skillMaker/templates/basic-skill-template.md`
- Examples: `.claude/skills/skillMaker/examples/skill-structures.md`
- Patterns: `.claude/skills/skillMaker/references/skill-patterns.md`
- User guide: `.claude/skills/skillMaker/README.md`
- Architecture: `.claude/skills/skillMaker/ARCHITECTURE.md`
- Complete guide: `SKILLMAKER_GUIDE.md` (project root)
