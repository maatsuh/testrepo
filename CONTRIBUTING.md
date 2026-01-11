# Contributing to testrepo

Thank you for your interest in contributing to testrepo! We welcome contributions from everyone. This document provides guidelines and instructions for contributing to the project.

## Code of Conduct

Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

## How to Contribute

### Reporting Bugs

Before creating bug reports, please check the issue list as you might find out that you don't need to create one. When you are creating a bug report, please include as many details as possible:

- **Use a clear and descriptive title**
- **Describe the exact steps which reproduce the problem**
- **Provide specific examples to demonstrate the steps**
- **Describe the behavior you observed after following the steps**
- **Explain which behavior you expected to see instead and why**
- **Include screenshots and animated GIFs if possible**
- **Include your environment details** (OS, version, etc.)

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion, please include:

- **Use a clear and descriptive title**
- **Provide a step-by-step description of the suggested enhancement**
- **Provide specific examples to demonstrate the steps**
- **Describe the current behavior and expected behavior**
- **Explain why this enhancement would be useful**

### Pull Requests

- Fill in the required template
- Follow the styleguides
- End all files with a newline
- Avoid platform-dependent code
- Document new code based on the Documentation Styleguide

## Development Setup

1. Fork the repository
2. Clone your fork:
   ```bash
   git clone https://github.com/YOUR-USERNAME/testrepo.git
   cd testrepo
   ```
3. Add upstream remote:
   ```bash
   git remote add upstream https://github.com/maatsuh/testrepo.git
   ```
4. Create a new branch for your feature:
   ```bash
   git checkout -b feature/your-feature-name
   ```

## Making Changes

1. Make your changes in your branch
2. Test your changes thoroughly
3. Commit your changes with clear, descriptive messages:
   ```bash
   git commit -m "Add feature: description of what was added"
   ```
4. Push to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```
5. Create a Pull Request to the main repository

## Pull Request Process

1. Update the README.md with details of changes to the interface, if applicable
2. Increase version numbers in any examples files and the README.md to the new version that this Pull Request would represent
3. Ensure your code follows the project's style guidelines
4. Ensure all tests pass
5. Your Pull Request will be reviewed by the maintainers

## Styleguides

### Git Commit Messages

- Use the present tense ("Add feature" not "Added feature")
- Use the imperative mood ("Move cursor to..." not "Moves cursor to...")
- Limit the first line to 72 characters or less
- Reference issues and pull requests liberally after the first line
- When only changing documentation, include `[ci skip]` in the commit description

### Code Style

- Follow the existing code style in the repository
- Use meaningful variable and function names
- Add comments for complex logic
- Keep lines reasonably short and readable

### Documentation Style

- Use clear and concise language
- Include code examples where appropriate
- Keep documentation up-to-date with code changes
- Use proper markdown formatting

## Additional Notes

### Issue and Pull Request Labels

- `bug` - Something isn't working
- `enhancement` - New feature or request
- `documentation` - Improvements or additions to documentation
- `good first issue` - Good for newcomers
- `help wanted` - Extra attention is needed
- `question` - Further information is requested

## Recognition

Contributors will be recognized in the project README and release notes.

## Questions?

Feel free to open an issue with the label `question` if you have any questions about contributing.

Thank you for contributing to testrepo! 🎉
