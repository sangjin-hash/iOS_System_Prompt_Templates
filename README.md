# Swift Claude Code Templates

Sub-agent templates for Claude Code iOS/Swift development.

![Swift 6+](https://img.shields.io/badge/Swift-6+-orange.svg)
![iOS 17+](https://img.shields.io/badge/iOS-17+-blue.svg)

## Architecture

<img width="852" height="842" alt="Architecture Diagram" src="https://github.com/user-attachments/assets/57287d3d-5eb2-4554-9c0f-2cbbba4bae47" />

- **Core References** (`conventions/`, `HIG/`) - Coding standards and Apple guidelines
- **Reusable Processes** (`skills/`) - Workflows (TDD, code review, memory safety)
- **Code Templates** (`templates/`) - Ready-to-use templates (TCA, LiquidGlass)
- **Agents** (`agents/`) - Domain-specific sub-agents for specialized tasks

## Directory Structure

| Directory | Purpose |
|-----------|---------|
| `agents/` | Domain-specific sub-agents |
| `conventions/` | Swift coding conventions (**customizable** - replace with your own) |
| `HIG/` | Apple Human Interface Guidelines reference |
| `skills/` | Reusable skills (TDD workflow, memory safety, etc.) |
| `templates/` | Swift code templates (TCA, LiquidGlass, etc.) |

## Agents (more coming soon)

| Category | Agents |
|----------|--------|
| UI | `swiftui-agent`, `uikit-agent`, `liquid-glass-ui-builder` |
| Architecture | `tca-feature-generator` *(more coming soon)* |
| Test | `xctest-agent`, `uitest-agent`, `tca-test-writer` |
| Network | `urlsession-agent`, `combine-agent`, `swift-concurrency-agent` |
| LocalDB | `swiftdata-agent`, `coredata-agent` |
| Code Review | `swift-code-reviewer` |

## Customization

The `conventions/` directory contains **sample** coding conventions. You can:

- Replace with your own team's coding standards
- Customize rules per project requirements
- Add additional conventions as needed

All agents will automatically reference your customized conventions.

## Contributing

Contributions are welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
