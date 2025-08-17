# Security Policy

## Supported Versions

yupsh is currently in active development. We provide security updates for:

| Version | Supported          |
| ------- | ------------------ |
| main    | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

We take security seriously. If you discover a security vulnerability in yupsh, please report it responsibly:

### How to Report

1. **Do not** create a public GitHub issue for security vulnerabilities
2. Email security reports to: [security@yupsh.dev](mailto:security@yupsh.dev)
3. Include as much detail as possible:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

### What to Expect

- **Acknowledgment**: We'll acknowledge receipt within 24 hours
- **Assessment**: We'll assess the vulnerability within 5 business days
- **Updates**: We'll provide regular updates on our progress
- **Resolution**: We aim to resolve critical vulnerabilities within 30 days

### Security Considerations in yupsh

yupsh processes user input and executes commands. Key security areas include:

- **Input Validation**: All user input is validated before processing
- **Context Cancellation**: All operations respect context cancellation to prevent resource exhaustion
- **Memory Safety**: Streaming processing prevents memory exhaustion attacks
- **Command Injection**: Commands are isolated and don't execute arbitrary shell commands

### Responsible Disclosure

We follow responsible disclosure practices:

1. We'll work with you to understand and validate the issue
2. We'll develop and test a fix
3. We'll coordinate the disclosure timeline with you
4. We'll publicly acknowledge your contribution (unless you prefer to remain anonymous)

## Security Best Practices for Contributors

When contributing to yupsh:

- Always validate user input
- Use context cancellation for long-running operations
- Prefer streaming over loading entire files into memory
- Follow the principle of least privilege
- Don't execute arbitrary shell commands
- Test edge cases and error conditions

## Security Updates

Security updates will be:

- Released as soon as possible
- Clearly marked in release notes
- Accompanied by detailed advisory information
- Communicated through GitHub Security Advisories

## Questions?

For security-related questions that don't involve vulnerabilities, please:

- Open a [GitHub Discussion](https://github.com/yupsh/yupsh/discussions)
- Tag your question with "security"
- We'll respond publicly when appropriate
