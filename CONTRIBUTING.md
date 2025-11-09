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

yupsh uses a **modular architecture** where **each command is its own independent Go module**. This design choice is fundamental to yupsh's extensibility:

### Why Independent Modules?

- **Extensibility** - Add new commands without modifying the framework or other commands
- **Individual iteration** - Update and release commands independently
- **Third-party ecosystem** - Anyone can publish commands that work seamlessly with existing ones
- **Minimal dependencies** - Users only install the commands they need
- **Version independence** - Commands evolve at their own pace

**Your custom command is a first-class citizen** - it works exactly like built-in commands (`grep`, `sort`, etc.). The framework doesn't distinguish between "core" and "third-party" commands.

Here's how to create a new command:

### 1. Naming Convention

**Important**: yupsh follows an intentional naming pattern where the **package name matches the primary function name**:

```go
package grep      // Package is "grep"
func Grep(...) {} // Function is "Grep"

package sort      // Package is "sort"
func Sort(...) {} // Function is "Sort"
```

**Why this matters:**
- Allows standard imports: `grep.Grep("pattern")` - clear and explicit
- Enables dot imports: `import . "github.com/yupsh/grep"` then `Grep("pattern")` - cleaner for scripts
- Maintains clarity even with dot imports - `Grep()` clearly indicates the grep command
- Balances brevity with clarity for shell-style programming

When creating a new command, follow this pattern:
- Package name: lowercase command name (e.g., `package mycommand`)
- Primary function: Capitalized command name (e.g., `func Mycommand(...)`)
- Flag constructors: Descriptive names (e.g., `func IgnoreCase(...)`)

### 2. Command Structure

```
mycommand/
├── go.mod                  # Independent module
├── mycommand.go           # Main implementation
├── mycommand_test.go      # Example tests
└── opt/
    └── opt.go             # Strongly-typed flags
```

### 3. Basic Template

```go
package mycommand  // Package name: lowercase

import (
    "context"
    "io"

    gloo "github.com/gloo-foo/framework"
)

// Command implementation using StandardCommand abstraction
type command struct {
    gloo.StandardCommand[flags]
}

// Mycommand creates a new command
// Note: Function name matches package name (capitalized)
func Mycommand(parameters ...any) gloo.Line {
    args := opt.Args[string, flags](parameters...)
    return command{
        StandardCommand: gloo.StandardCommand[flags]{
            Positional: args.Positional,
            Flags:      args.Flags,
            Name:       "mycommand",
        },
    }
}

func (p command) Executor() gloo.LineExecutor {
	return func(line int64, stdin string) (stdout string, stderr string, emit bool, err error) {
		return "", "", false, nil
	}
}

```

### 4. Using Framework Abstractions

#### For Line Processing Commands
```go
func (c command) processReader(ctx context.Context, reader io.Reader, output io.Writer) error {
    return gloo.ProcessLinesSimple(ctx, reader, output,
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
func (p command) Executor() gloo.LineExecutor {
	return func(line int64, stdin string) (stdout string, stderr string, emit bool, err error) {
		return "", "", false, nil
	}
}

```

### 5. Testing Guidelines

- Use Example tests for documentation and basic functionality
- Test context cancellation for long-running operations
- Test error conditions and edge cases
- Include benchmarks for performance-critical commands

```go
func ExampleMyCommand() {
    ctx := context.Background()
    stdin := strings.NewReader("test input")

    cmd := mycommand.MyCommand()
    cmd.Execute(ctx, stdin, os.Stdout, os.Stderr)
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

- Use `gloo.ProcessLinesSimple` for line processing
- Use `gloo.ScanWithContext` instead of `scanner.Scan()`
- Add periodic `gloo.CheckContextCancellation(ctx)` in long loops
- Test cancellation behavior

## Memory Patterns

### Good Patterns
```go
// Use streaming processing
return gloo.ProcessLinesSimple(ctx, reader, output, processor)

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
