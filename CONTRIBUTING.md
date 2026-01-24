# Contributing

Thank you for your interest in contributing! This guide will help you get started.

## Requirements

- **Swift 6+**
- **iOS 17+** (macOS 14+, watchOS 10+, visionOS 1+)

## Overview

| Type | Purpose | Format |
|------|---------|--------|
| **Agents** | Instruction-focused guidance for specific domains | Markdown with directives |
| **Skills** | Repetitive, consistent processes that follow the same workflow | Step-by-step procedures |
| **Templates** | Code-focused scripts and boilerplate | `.swift.template` files |

You are encouraged to create new agents, skills, and templates that fit your workflow!

## Naming Conventions

- Use lowercase with hyphens (e.g., `swiftui-agent`, `tca-feature-generator`)
- Be specific about the technology focus
- Keep names concise but descriptive

## Adding an Agent

Agents provide **instruction-focused guidance** for specific tasks. When adding a new agent to `agents/`:

1. **YAML frontmatter is required**
   ```yaml
   ---
   name: your-agent-name
   description: Brief description
   version: 1.0.0
   ---
   ```

2. **Use 3-phase structure**
   - Phase 1: Context gathering
   - Phase 2: Implementation
   - Phase 3: Validation

3. **Place in appropriate category directory**
   - `agents/ui/` - UI-related agents
   - `agents/architecture/` - Architecture agents
   - `agents/test/` - Testing agents
   - `agents/network/` - Network agents
   - `agents/localDB/` - Local database agents
   - `agents/code-review/` - Code review agents

## Adding a Skill

Skills define **repetitive, consistent processes** that should follow the same workflow every time. When adding a new skill to `skills/`:

1. Define clear trigger conditions
2. Provide step-by-step workflow
3. Include validation criteria

## Adding a Template

Templates provide **code-focused scripts** and boilerplate. When adding a new template to `templates/`:

1. Create a dedicated directory
2. Include a README.md explaining usage
3. Use `.swift.template` extension for template files
4. Document all placeholders

## Quality Standards

All contributions must:

- **Use modern Swift** (Swift 6+ features, async/await, actors)
- **Follow Apple guidelines** (HIG, API design guidelines)
- **Include platform-specific considerations**
- **Provide clear examples** with Swift code

## Testing

Before submitting:

1. Test with Claude Code on actual Swift projects
2. Verify compatibility across different Apple platforms
3. Ensure Xcode integration works properly

## Improving Existing Items

To improve an existing agent, skill, or template:

1. Identify the issue or enhancement
2. Test your changes thoroughly
3. Document the improvements in your PR
4. Maintain backward compatibility when possible

## Pull Request Guidelines

When submitting a PR:

- **Title**: `Add [name]` or `Edit [name]`
- **Description**: Include what it does, platforms supported, and example use cases

### PR Checklist

- [ ] Targets Swift 6+ / iOS 17+
- [ ] Files are placed in correct directories
- [ ] YAML frontmatter included (for agents)
- [ ] README updated if adding new category
- [ ] Tested with Claude Code