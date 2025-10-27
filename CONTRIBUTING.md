# Contributing to OSAM

First off, thank you for considering contributing to OSAM! It's people like you that make OSAM such a great tool.

## Code of Conduct

This project and everyone participating in it is governed by our commitment to providing a welcoming and inspiring community for all. Please be respectful and constructive.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the [issue list](https://github.com/thezeus123/OSAM/issues) as you might find out that you don't need to create one. When you are creating a bug report, please include as many details as possible:

* **Use a clear and descriptive title** for the issue
* **Describe the exact steps to reproduce the problem**
* **Provide specific examples** to demonstrate the steps
* **Describe the behavior you observed** and what behavior you expected
* **Include screenshots** if possible
* **Include your Home Assistant version** and OSAM version
* **Include relevant logs** from Home Assistant (enable Debug Mode)

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion, please include:

* **Use a clear and descriptive title**
* **Provide a detailed description of the suggested enhancement**
* **Explain why this enhancement would be useful**
* **List any similar features in other projects** if applicable

### Pull Requests

1. Fork the repository
2. Create a new branch from `main`:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. Make your changes
4. Test thoroughly with Home Assistant
5. Update documentation (README.md, CHANGELOG.md)
6. Commit your changes with clear commit messages:
   ```bash
   git commit -m "Add feature: brief description"
   ```
7. Push to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```
8. Open a Pull Request

### Testing Checklist

Before submitting a PR, please verify:

- [ ] Blueprint loads without errors in Home Assistant
- [ ] All existing features still work
- [ ] New features work as expected
- [ ] Debug mode produces appropriate logs
- [ ] No YAML syntax errors
- [ ] Documentation updated (if applicable)
- [ ] CHANGELOG.md updated

## Development Guidelines

### YAML Style

* Use 2 spaces for indentation
* Use descriptive variable names
* Add comments for complex logic
* Keep line length reasonable (< 120 characters when possible)

### Variable Naming

* Use snake_case for variables
* Prefix boolean variables with `enable_`, `is_`, or `has_`
* Use descriptive names (not `temp` but `outdoor_temperature_sensor`)

### Documentation

* Update README.md for new features
* Add entries to CHANGELOG.md following Keep a Changelog format
* Include inline comments for complex Jinja2 templates
* Provide examples for new configuration options

### Testing

When testing your changes:

1. Create a new automation from the blueprint
2. Test with different sensor types (binary_sensor, input_boolean)
3. Test edge cases (sensors changing rapidly, midnight rollover, etc.)
4. Enable Debug Mode and check logs
5. Test with different Home Assistant versions if possible

### Commit Messages

Follow these guidelines:

* Use present tense ("Add feature" not "Added feature")
* Use imperative mood ("Move cursor to..." not "Moves cursor to...")
* First line should be 50 characters or less
* Reference issues and pull requests when applicable

Examples:
```
Add temperature check feature

Add support for temperature-based notifications.
Only send alerts when outdoor temperature is below
the configured threshold.

Closes #123
```

## Project Structure

```
OSAM/
├── osam_v3.12.1.yaml      # Main blueprint file
├── README.md              # Documentation
├── CHANGELOG.md           # Version history
├── LICENSE                # MIT License
├── CONTRIBUTING.md        # This file
└── examples/              # Example configurations
    ├── basic_door.yaml
    ├── window_temp.yaml
    └── critical_door.yaml
```

## Feature Requests

Have an idea for a new feature? Great! Please:

1. Check if it's already been suggested in [Issues](https://github.com/thezeus123/OSAM/issues)
2. Open a new issue with the `enhancement` label
3. Clearly describe the feature and its benefits
4. Include use cases and examples

## Questions?

Feel free to:
* Open an issue with the `question` label
* Start a discussion in [GitHub Discussions](https://github.com/thezeus123/OSAM/discussions)
* Ask in the [Home Assistant Community Forum](https://community.home-assistant.io/)

## Recognition

Contributors will be recognized in:
* README.md contributors section
* Release notes
* GitHub contributors page

Thank you for contributing to OSAM! 🎉
