# Contributing to yupsh

We love your input! We want to make contributing to yupsh as easy and transparent as possible, whether it's:

- Reporting a bug
- Discussing the current state of the code
- Submitting a fix
- Proposing new features
- Becoming a maintainer

## Development Process

We use GitHub to host code, to track issues and feature requests, as well as accept pull requests.

## Pull Requests

Pull requests are the best way to propose changes to the codebase. We actively welcome your pull requests:

1. Fork the repo and create your branch from `main`.
2. If you've added code that should be tested, add tests.
3. If you've changed APIs, update the documentation.
4. Ensure the test suite passes.
5. Make sure your code lints.
6. Issue that pull request!

## Writing a New Command

yupsh uses a modular architecture where each command is an independent Go module. Here's how to create a new command:

### 1. Command Structure

```
mycommand/
├── go.mod                  # Independent module
├── mycommand.go           # Main implementation
├── mycommand_test.go      # Example tests
└── opt/
    └── opt.go             # Strongly-typed flags
```

### 2. Basic Template

```go
package mycommand

import (
    "context"
    "io"

    yup "github.com/yupsh/framework"
    "github.com/yupsh/framework/opt"
    localopt "github.com/yupsh/mycommand/opt"
)

// Command implementation using StandardCommand abstraction
type command struct {
    yup.StandardCommand[localopt.Flags]
}

// MyCommand creates a new command
func MyCommand(parameters ...any) yup.Command {
    args := opt.Args[string, localopt.Flags](parameters...)
    return command{
        StandardCommand: yup.StandardCommand[localopt.Flags]{
            Positional: args.Positional,
            Flags:      args.Flags,
            Name:       "mycommand",
        },
    }
}

func (c command) Execute(ctx context.Context, input io.Reader, output, stderr io.Writer) error {
    // Your command logic here
    return nil
}
```

### 3. Using Framework Abstractions

#### For Line Processing Commands
```go
func (c command) processReader(ctx context.Context, reader io.Reader, output io.Writer) error {
    return yup.ProcessLinesSimple(ctx, reader, output,
        func(ctx context.Context, lineNum int, line string, output io.Writer) error {
            // Process each line
            fmt.Fprintln(output, processLine(line))
            return nil
        },
    )
}
```

#### For File Processing Commands
```go
func (c command) Execute(ctx context.Context, input io.Reader, output, stderr io.Writer) error {
    return c.ProcessFiles(ctx, input, output, stderr,
        func(ctx context.Context, source yup.InputSource, output io.Writer) error {
            return c.processReader(ctx, source.Reader, output)
        },
    )
}
```

### 4. Testing Guidelines

- Use Example tests for documentation and basic functionality
- Test context cancellation for long-running operations
- Test error conditions and edge cases
- Include benchmarks for performance-critical commands

```go
func ExampleMyCommand() {
    ctx := context.Background()
    input := strings.NewReader("test input")

    cmd := mycommand.MyCommand()
    cmd.Execute(ctx, input, os.Stdout, os.Stderr)
    // Output: expected output
}
```

## Code Style

- Follow Go conventions and use `gofmt`
- Use meaningful variable and function names
- Add comments for exported functions and complex logic
- Prefer composition over inheritance
- Keep functions small and focused

## Context Cancellation

All commands must support context cancellation:

- Use `yup.ProcessLinesSimple` for line processing
- Use `yup.ScanWithContext` instead of `scanner.Scan()`
- Add periodic `yup.CheckContextCancellation(ctx)` in long loops
- Test cancellation behavior

## Memory Patterns

### Good Patterns
```go
// Use streaming processing
return yup.ProcessLinesSimple(ctx, reader, output, processor)

// Use framework helpers
if err := c.RequireArgs(1, stderr); err != nil {
    return err
}
```

### Avoid Patterns
```go
// Don't load entire files into memory
data, _ := io.ReadAll(reader) // ❌

// Don't ignore context cancellation
for scanner.Scan() { // ❌ Use ScanWithContext
    // ...
}
```

## Reporting Bugs

We use GitHub issues to track public bugs. Report a bug by [opening a new issue](https://github.com/yupsh/yupsh/issues).

**Great Bug Reports** tend to have:

- A quick summary and/or background
- Steps to reproduce
- What you expected would happen
- What actually happens
- Notes (possibly including why you think this might be happening, or stuff you tried that didn't work)

## License

By contributing, you agree that your contributions will be licensed under its MIT License.

## References

- [Framework Documentation](../framework/README.md)
- [Command Examples](../cat/README.md)
- [Architecture Overview](../yupsh/README.md)
