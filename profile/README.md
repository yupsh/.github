# yupsh

> **Shell-style programming in Go**: Write powerful "shell scripts" as Go programs

[![Go Version](https://img.shields.io/badge/Go-1.21+-00ADD8?style=flat&logo=go)](https://golang.org/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/yupsh/yupsh?style=social)](https://github.com/yupsh/yupsh)

## 🎯 **What is yupsh?**

yupsh lets you write **Go programs that work like shell scripts**, combining the best of both worlds:

- **Shell's Power**: Composable commands, pipelines, and text processing glue
- **Go's Strengths**: Type safety, performance, robust error handling, and rich data structures

Instead of writing bash scripts, write Go programs with shell-like operations!

## ✨ **Why Write Shell Logic in Go?**

### 🐚 **Shell is Amazing For...**
- **Glue Code**: Connecting different tools and processing data flows
- **Text Processing**: Powerful pipelines for filtering, transforming, and analyzing
- **Automation**: Quick scripts to automate repetitive tasks

### 🔥 **But Go is Better For...**
- **Type Safety**: Catch errors at compile time instead of runtime
- **Performance**: Compiled binaries run faster and use less memory
- **Data Handling**: Rich standard library and powerful data structures
- **Error Handling**: Explicit error handling prevents silent failures
- **Testing**: Built-in testing framework with excellent tooling
- **Distribution**: Single binary deployment, no dependency issues

### 🚀 **yupsh = Shell Power + Go Advantages**

```go
// Instead of this bash script:
// #!/bin/bash
// cat logs/*.log | grep ERROR | cut -d' ' -f1,3 | sort | uniq -c | head -10

// Write this Go program:
func main() {
    ctx := context.Background()

    pipeline := yup.Pipe(
        cat.Cat("logs/*.log"),
        grep.Grep("ERROR", grep.IgnoreCase),
        cut.Cut(cut.Fields(1, 3), cut.Delimiter(" ")),
        sort.Sort(),
        uniq.Uniq(uniq.Count),
        head.Head(head.Lines(10)),
    )

    pipeline.Execute(ctx, nil, os.Stdout, os.Stderr)
}
```

## 🔬 **Pure Go Implementation Philosophy**

### **⚡ Native Go Commands, Not Subprocess Wrappers**

**yupsh commands are implemented in pure Go** - they don't spawn subprocesses to run traditional Unix tools:

```go
// ✅ What yupsh does: Pure Go implementation
func (c grepCommand) Execute(ctx context.Context, input io.Reader, output io.Writer) error {
    scanner := bufio.NewScanner(input)
    regex := regexp.MustCompile(c.pattern)  // Native Go regex

    for scanner.Scan() {
        line := scanner.Text()
        if regex.MatchString(line) {        // Pure Go pattern matching
            fmt.Fprintln(output, line)
        }
    }
    return scanner.Err()
}

// ❌ What yupsh avoids: Subprocess spawning
func badGrep(pattern string, input io.Reader) error {
    cmd := exec.Command("grep", pattern)    // Spawns external process
    cmd.Stdin = input
    return cmd.Run()
}
```

### **🎯 Why Pure Go?**

| Aspect | Subprocess Approach | Pure Go Approach |
|--------|-------------------|------------------|
| **Performance** | ❌ Process overhead | ✅ In-memory operations |
| **Portability** | ❌ Requires external tools | ✅ Single binary, works anywhere |
| **Dependencies** | ❌ Must have tools installed | ✅ Zero external dependencies |
| **Error Handling** | ❌ Complex process management | ✅ Native Go error handling |
| **Memory Usage** | ❌ Multiple processes | ✅ Shared memory space |
| **Debugging** | ❌ Cross-process debugging | ✅ Native Go debugging |
| **Testing** | ❌ Requires external tools | ✅ Pure unit testing |

### **🔧 When Subprocesses Are Acceptable**

While yupsh strongly favors pure Go, subprocesses **may** be used when:

```go
// Acceptable: Complex external tools that would be massive to reimplement
func (c gitCommand) Execute(ctx context.Context, input io.Reader, output io.Writer) error {
    // git has massive complexity - subprocess is reasonable here
    cmd := exec.CommandContext(ctx, "git", c.args...)
    cmd.Stdout = output
    cmd.Stderr = output
    return cmd.Run()
}

// Preferred: Simple operations implemented in pure Go
func (c catCommand) Execute(ctx context.Context, input io.Reader, output io.Writer) error {
    // No subprocess needed - pure Go file reading
    _, err := io.Copy(output, input)
    return err
}
```

### **💡 Design Principle**

**"Minimize subprocesses, maximize pure Go"**

- **First choice**: Implement functionality in pure Go
- **Fallback**: Use subprocess only for complex external tools (git, compilers, etc.)
- **Goal**: Self-contained Go programs that work anywhere Go runs

## 🔧 **Shell Scripting vs Go Programs**

Explore our **[examples directory](https://github.com/yupsh/examples/)** with real, executable comparisons:

### **📊 [Log Processor](https://github.com/yupsh/examples/tree/main/log-processor)**
**Shell pattern**: `grep | while read | cut` for log analysis
**yupsh equivalent**: Pipeline with custom `While()` command and field extraction

```bash
# Shell version
./examples/log-processor/process-logs.sh

# yupsh version
cd examples/log-processor && go run main.go
```

### **📈 [File Statistics](https://github.com/yupsh/examples/tree/main/file-stats/)**
**Shell pattern**: `find | awk | sort | uniq` for file analysis
**yupsh equivalent**: Native Go file operations with yupsh command composition

```bash
# Shell version
./examples/file-stats/analyze-files.sh

# yupsh version
cd examples/file-stats && go run main.go
```

### **🎯 Shell Script Patterns → yupsh Commands**

| Shell Pattern | yupsh Equivalent | Benefits |
|---------------|------------------|----------|
| `for file in *.log` | `find.Find(".", find.Name("*.log"))` | Type-safe file matching |
| `while read line` | `while.While(func(line string) {...})` | Context cancellation built-in |
| `cut -d' ' -f1` | `cut.Cut(cut.Fields(1), cut.Delimiter(" "))` | Reusable, testable command |
| `grep "pattern"` | `grep.Grep("pattern")` | Pure Go regex, no subprocess |
| `[[ -f "$file" ]]` | `IfFileExists(file, ...)` | Explicit error handling |
| `>> results.csv` | `tee.Tee("results.csv")` | Composable output redirection |
| `awk '{print $1}'` | `awk.Awk("{print $1}")` | Pure Go awk implementation |

### **🚀 yupsh Advantages Demonstrated**

- **🔗 Shell-like Composition**: Commands pipe together naturally, just like shell
- **🧱 Reusable Building Blocks**: Use existing `grep`, `cut`, `find` commands directly
- **⚡ Easy Custom Commands**: Create reusable commands like `While()` in minutes
- **🎯 Direct Translation**: Shell patterns have clear yupsh equivalents
- **🔧 Pure Go Benefits**: Type safety, error handling, context cancellation built-in
- **📦 Command Packaging**: Custom commands can be packaged and reused across projects
- **🧪 Testable**: Each command and pipeline easily unit tested
- **🔄 Familiar Patterns**: `while read line` → `While(func(line string)...)`
- **🌍 Cross-platform**: Same code works on Windows, macOS, Linux
- **📦 Zero Dependencies**: No external tools required

**The result**: Programs that *think* like shell scripts but *work* like robust Go applications!

**[➤ Explore All Examples](https://github.com/yupsh/examples/)** - Real shell scripts with their yupsh equivalents

## 🏗️ **Core Concepts**

### **1. Composable Commands**
```go
// Build complex operations from simple commands
pipeline := yup.Pipe(
    find.Find(".", find.Name("*.go")),
    grep.Grep("func.*Test", grep.Regex),
    wc.Wc(wc.Lines),
)
```

### **2. Type-Safe Configuration**
```go
// No magic strings - compile-time safety
cmd := grep.Grep("pattern",
    grep.IgnoreCase,      // Type-safe flag
    grep.LineNumber,      // IDE autocompletion
    grep.Count(100),      // Parameterized options
)
```

### **3. Structured Data Flow**
```go
// Process data through pipelines while maintaining Go types
type ProcessedData struct {
    Filename string
    LineCount int
    ErrorCount int
}

func processFiles(files []string) ([]ProcessedData, error) {
    var results []ProcessedData

    for _, file := range files {
        // Use shell-like operations but keep structured data
        lineCount, err := countLines(file)
        if err != nil {
            return nil, err
        }

        errorCount, err := countErrors(file)
        if err != nil {
            return nil, err
        }

        results = append(results, ProcessedData{
            Filename: file,
            LineCount: lineCount,
            ErrorCount: errorCount,
        })
    }

    return results, nil
}
```

### **4. Context and Cancellation**
```go
// All operations support cancellation and timeouts
ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
defer cancel()

// Operations respect context cancellation
err := pipeline.Execute(ctx, input, output, stderr)
if err == context.DeadlineExceeded {
    fmt.Println("Operation timed out")
}
```

## 📚 **Common Use Cases**

### **🔍 Log Analysis**
```go
// Replace: grep ERROR *.log | cut -d: -f2 | sort | uniq -c
func analyzeErrors(logFiles []string) map[string]int {
    errorCounts := make(map[string]int)

    for _, file := range logFiles {
        pipeline := yup.Pipe(
            cat.Cat(file),
            grep.Grep("ERROR", grep.IgnoreCase),
            cut.Cut(cut.Delimiter(":"), cut.Fields(2)),
        )

        var output strings.Builder
        pipeline.Execute(context.Background(), nil, &output, os.Stderr)

        for _, line := range strings.Split(output.String(), "\n") {
            if line = strings.TrimSpace(line); line != "" {
                errorCounts[line]++
            }
        }
    }

    return errorCounts
}
```

### **📊 Data Processing**
```go
// Replace complex bash with type-safe Go
func processCSVData(filename string) ([]Record, error) {
    var records []Record

    // Use shell-like text processing
    pipeline := yup.Pipe(
        cat.Cat(filename),
        tail.Tail(tail.Skip(1)),  // Skip header
        cut.Cut(cut.Delimiter(","), cut.Fields(1, 3, 5)),
    )

    var output strings.Builder
    err := pipeline.Execute(context.Background(), nil, &output, os.Stderr)
    if err != nil {
        return nil, err
    }

    // Process with Go's type system
    for _, line := range strings.Split(output.String(), "\n") {
        if record, err := parseRecord(line); err == nil {
            records = append(records, record)
        }
    }

    return records, nil
}
```

### **🔧 System Administration**
```go
// Replace bash system scripts with robust Go programs
func cleanupOldLogs(maxAge time.Duration) error {
    cutoff := time.Now().Add(-maxAge)

    // Find old log files
    var oldFiles []string
    pipeline := yup.Pipe(
        find.Find("/var/log",
            find.Name("*.log"),
            find.OlderThan(cutoff),
        ),
    )

    var output strings.Builder
    err := pipeline.Execute(context.Background(), nil, &output, os.Stderr)
    if err != nil {
        return fmt.Errorf("finding old logs: %w", err)
    }

    // Process results with proper error handling
    for _, file := range strings.Split(output.String(), "\n") {
        if file = strings.TrimSpace(file); file != "" {
            if err := os.Remove(file); err != nil {
                log.Printf("Failed to remove %s: %v", file, err)
                // Continue processing instead of failing completely
            } else {
                log.Printf("Removed old log: %s", file)
            }
        }
    }

    return nil
}
```

## 🚀 **Getting Started**

### **1. Install yupsh**
```bash
go mod init my-shell-script
go get github.com/yupsh/framework
go get github.com/yupsh/cat
go get github.com/yupsh/grep
# ... install commands as needed
```

### **2. Write Your First Go "Shell Script"**
```go
// main.go
package main

import (
    "context"
    "os"

    "github.com/yupsh/cat"
    "github.com/yupsh/grep"
    "github.com/yupsh/framework"
)

func main() {
    ctx := context.Background()

    // Simple pipeline: find all Go files with "TODO" comments
    pipeline := yup.Pipe(
        find.Find(".", find.Name("*.go")),
        grep.Grep("TODO", grep.LineNumber),
    )

    err := pipeline.Execute(ctx, nil, os.Stdout, os.Stderr)
    if err != nil {
        fmt.Fprintf(os.Stderr, "Error: %v\n", err)
        os.Exit(1)
    }
}
```

### **3. Build and Run**
```bash
go build -o find-todos .
./find-todos
```

## 💡 **Key Advantages Over Shell Scripts**

| Feature | Shell Scripts | yupsh Go Programs |
|---------|---------------|-------------------|
| **Implementation** | ❌ Spawns external processes | ✅ **Pure Go - no subprocesses** |
| **Dependencies** | ❌ Requires Unix tools installed | ✅ **Zero external dependencies** |
| **Portability** | ❌ Unix/Linux only | ✅ **Runs anywhere Go runs** |
| **Type Safety** | ❌ Runtime errors | ✅ Compile-time checking |
| **Error Handling** | ❌ Fragile `set -e` | ✅ Explicit error handling |
| **Testing** | ❌ Difficult to test | ✅ Built-in testing framework |
| **Performance** | ❌ Process overhead | ✅ **In-memory operations** |
| **Data Structures** | ❌ Text manipulation only | ✅ Rich Go types |
| **IDE Support** | ❌ Limited tooling | ✅ Full Go tooling support |
| **Distribution** | ❌ Dependency management | ✅ **Single self-contained binary** |
| **Debugging** | ❌ Difficult to debug | ✅ Excellent debugging tools |

## 🌟 **Available Commands**

yupsh provides **pure Go implementations** of common Unix commands for use in your programs:

**Text Processing**: `cat`, `grep`, `head`, `tail`, `cut`, `sort`, `uniq`, `wc`, `tr`, `sed`, `awk`
**File Operations**: `find`, `ls`, `split`, `join`, `comm`, `diff`
**Control Flow**: `while`
**Utilities**: `echo`, `yes`, `seq`, `shuf`, `base64`, `hexdump`, `tee`, `xargs`

### **🔬 Pure Go Implementation**
- **No external dependencies** - commands implemented in native Go
- **No subprocess spawning** - all operations happen in-memory
- **Self-contained** - single binary contains all functionality
- **Cross-platform** - works anywhere Go compiles
- **Composable** - designed for use in Go programs, not as standalone CLI tools

## 📖 **Learn More**

- **[🚀 Examples](https://github.com/yupsh/examples/)**: Real shell scripts vs yupsh Go programs
- **[🔧 Framework Documentation](https://github.com/yupsh/framework/README.md)**: Build your own commands
- **[🤝 Contributing Guide](https://github.com/yupsh/.github/CONTRIBUTING.md)**: Join the community
- **[💬 Discussions](/orgs/yupsh/discussions)**: Ask questions and share ideas

## 🎯 **Philosophy**

**"Shell scripting is powerful, but Go makes it robust."**

yupsh brings the compositional power of shell scripting into Go programs, giving you:
- The **expressiveness** of shell pipelines
- The **reliability** of Go's type system
- The **performance** of compiled code with **pure Go implementations**
- The **portability** of zero-dependency binaries
- The **maintainability** of structured programming

### **Core Principles**
1. **Pure Go First** - Implement functionality natively, avoid subprocesses
2. **Zero Dependencies** - Self-contained binaries that work anywhere
3. **Shell Composability** - Commands work together like Unix pipes
4. **Type Safety** - Leverage Go's type system for robust programs
5. **Performance** - In-memory operations beat process spawning

Write your automation, data processing, and system administration logic as **Go programs** that feel like shell scripts but work like robust, portable software.

---

<p align="center">
  <strong>yupsh</strong> - Shell-style programming in Go
</p>
<p align="center">
  <a href="https://github.com/yupsh/yupsh">GitHub</a> •
  <a href="https://github.com/yupsh/yupsh/discussions">Discussions</a> •
  <a href="https://github.com/yupsh/yupsh/issues">Issues</a> •
  <a href=".github/CONTRIBUTING.md">Contributing</a>
</p>
