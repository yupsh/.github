# yupsh

> **Shell-style programming in Go**: Write powerful "shell scripts" as Go programs

[![Go Version](https://img.shields.io/badge/Go-1.25+-00ADD8?style=flat&logo=go)](https://golang.org/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/gloo-foo/framework?style=social)](https://github.com/gloo-foo/framework)

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
// cat logs/*.log \
// | grep ERROR \
// | cut -d' ' -f1,3 \
// | sort \
// | uniq -c \
// | head -10

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

## 📦 **Complete Command Reference**

yupsh provides **pure Go implementations** of common Unix commands. Each command is independently maintained with comprehensive examples.

### Text Processing Commands

| Command | Shell Example | yupsh Equivalent | Examples |
|---------|---------------|------------------|----------|
| **awk** | `awk '{print $2}'` | `awk.Awk(customProgram, input)` | [📚 awk-examples](https://github.com/yupsh/yupsh/tree/main/awk-examples) |
| **cat** | `cat file.txt` | `cat.Cat("file.txt")` | [📚 cat-examples](https://github.com/yupsh/yupsh/tree/main/cat-examples) |
| **cut** | `cut -d',' -f1,3` | `cut.Cut(cut.Fields(1, 3), cut.Delimiter(","))` | [📚 cut-examples](https://github.com/yupsh/yupsh/tree/main/cut-examples) |
| **grep** | `grep "pattern"` | `grep.Grep("pattern")` | [📚 grep-examples](https://github.com/yupsh/yupsh/tree/main/grep-examples) |
| **head** | `head -n 10` | `head.Head(head.Lines(10))` | [📚 head-examples](https://github.com/yupsh/yupsh/tree/main/head-examples) |
| **nl** | `nl file.txt` | `nl.Nl("file.txt")` | [📚 nl-examples](https://github.com/yupsh/yupsh/tree/main/nl-examples) |
| **paste** | `paste file1 file2` | `paste.Paste("file1", "file2")` | [📚 paste-examples](https://github.com/yupsh/yupsh/tree/main/paste-examples) |
| **rev** | `rev` | `rev.Rev()` | [📚 rev-examples](https://github.com/yupsh/yupsh/tree/main/rev-examples) |
| **sed** | `sed 's/old/new/'` | `sed.Sed("s/old/new/")` | [📚 sed-examples](https://github.com/yupsh/yupsh/tree/main/sed-examples) |
| **sort** | `sort -n` | `sort.Sort(sort.Numeric)` | [📚 sort-examples](https://github.com/yupsh/yupsh/tree/main/sort-examples) |
| **tac** | `tac file.txt` | `tac.Tac("file.txt")` | [📚 tac-examples](https://github.com/yupsh/yupsh/tree/main/tac-examples) |
| **tail** | `tail -n 20` | `tail.Tail(tail.Lines(20))` | [📚 tail-examples](https://github.com/yupsh/yupsh/tree/main/tail-examples) |
| **tr** | `tr 'a-z' 'A-Z'` | `tr.Tr("a-z", "A-Z")` | [📚 tr-examples](https://github.com/yupsh/yupsh/tree/main/tr-examples) |
| **uniq** | `uniq -c` | `uniq.Uniq(uniq.Count)` | [📚 uniq-examples](https://github.com/yupsh/yupsh/tree/main/uniq-examples) |
| **wc** | `wc -l` | `wc.Wc(wc.Lines)` | [📚 wc-examples](https://github.com/yupsh/yupsh/tree/main/wc-examples) |

### File Operations Commands

| Command | Shell Example | yupsh Equivalent | Examples |
|---------|---------------|------------------|----------|
| **basename** | `basename /path/file.txt` | `basename.Basename("/path/file.txt")` | [📚 basename-examples](https://github.com/yupsh/yupsh/tree/main/basename-examples) |
| **comm** | `comm file1 file2` | `comm.Comm("file1", "file2")` | [📚 comm-examples](https://github.com/yupsh/yupsh/tree/main/comm-examples) |
| **diff** | `diff file1 file2` | `diff.Diff("file1", "file2")` | [📚 diff-examples](https://github.com/yupsh/yupsh/tree/main/diff-examples) |
| **dirname** | `dirname /path/file.txt` | `dirname.Dirname("/path/file.txt")` | [📚 dirname-examples](https://github.com/yupsh/yupsh/tree/main/dirname-examples) |
| **find** | `find . -name "*.go"` | `find.Find(".", find.Name("*.go"))` | [📚 find-examples](https://github.com/yupsh/yupsh/tree/main/find-examples) |
| **join** | `join file1 file2` | `join.Join("file1", "file2")` | [📚 join-examples](https://github.com/yupsh/yupsh/tree/main/join-examples) |
| **ls** | `ls -la` | `ls.Ls(ls.Long, ls.All)` | [📚 ls-examples](https://github.com/yupsh/yupsh/tree/main/ls-examples) |
| **split** | `split -l 100 file` | `split.Split("file", split.Lines(100))` | [📚 split-examples](https://github.com/yupsh/yupsh/tree/main/split-examples) |

### Utility Commands

| Command | Shell Example | yupsh Equivalent | Examples |
|---------|---------------|------------------|----------|
| **base64** | `base64 file.txt` | `base64.Base64("file.txt")` | [📚 base64-examples](https://github.com/yupsh/yupsh/tree/main/base64-examples) |
| **echo** | `echo "hello"` | `echo.Echo("hello")` | [📚 echo-examples](https://github.com/yupsh/yupsh/tree/main/echo-examples) |
| **hexdump** | `hexdump -C file` | `hexdump.Hexdump("file", hexdump.Canonical)` | [📚 hexdump-examples](https://github.com/yupsh/yupsh/tree/main/hexdump-examples) |
| **seq** | `seq 1 10` | `seq.Seq(1, 10)` | [📚 seq-examples](https://github.com/yupsh/yupsh/tree/main/seq-examples) |
| **shuf** | `shuf file.txt` | `shuf.Shuf("file.txt")` | [📚 shuf-examples](https://github.com/yupsh/yupsh/tree/main/shuf-examples) |
| **tee** | `tee output.txt` | `tee.Tee("output.txt")` | [📚 tee-examples](https://github.com/yupsh/yupsh/tree/main/tee-examples) |
| **xargs** | `xargs -n 1 echo` | `xargs.Xargs(xargs.MaxArgs(1))` | [📚 xargs-examples](https://github.com/yupsh/yupsh/tree/main/xargs-examples) |
| **yes** | `yes "string"` | `yes.Yes("string")` | [📚 yes-examples](https://github.com/yupsh/yupsh/tree/main/yes-examples) |

### Control Flow Commands

| Command | Description | yupsh Equivalent | Examples |
|---------|-------------|------------------|----------|
| **capture** | Capture command output to buffers | `capture.Capture(cmd, capture.Stdout, capture.Stderr)` | [📚 capture-examples](https://github.com/yupsh/yupsh/tree/main/capture-examples) |
| **emit** | Emit strings as command output | `emit.Emit("line1", "line2")` | [📚 emit-examples](https://github.com/yupsh/yupsh/tree/main/emit-examples) |
| **exec** | Execute external commands | `exec.Exec("command", args...)` | [📚 exec-examples](https://github.com/yupsh/yupsh/tree/main/exec-examples) |
| **pipe** | Compose commands in pipelines | `pipe.Pipe(cmd1, cmd2, cmd3)` | Built into framework |
| **while** | Loop through input lines | `while.While(func(line string) {...})` | [📚 while-examples](https://github.com/yupsh/yupsh/tree/main/while-examples) |

### External Tool Integration

| Command | Description | yupsh Equivalent | Examples |
|---------|-------------|------------------|----------|
| **git** | Git version control operations | `git.Git("status")` | [📚 git-examples](https://github.com/yupsh/yupsh/tree/main/git-examples) |
| **perl** | Execute Perl scripts | `perl.Perl(script)` | [📚 perl-examples](https://github.com/yupsh/yupsh/tree/main/perl-examples) |
| **json** | JSON processing utilities | `json.Parse(input)` | Command available |

**Pure Go implementations** - No subprocess spawning for core commands. Zero external dependencies.

## ✨ **Why yupsh?**

- ✅ **Shell's composability** with Go's type safety
- ✅ **Pure Go implementations** - no subprocess overhead
- ✅ **Single binary** - works anywhere Go runs
- ✅ **Testable & maintainable** - full IDE support
- ✅ **Extensible** - each command is independent

## 🚀 **Quick Start**

```bash
# Install the framework and commands
go get github.com/gloo-foo/framework
go get github.com/yupsh/grep
go get github.com/yupsh/sort
```

```go
// Write shell-like Go code
pipeline := yup.Pipe(
    grep.Grep("ERROR"),
    sort.Sort(),
    head.Head(head.Lines(10)),
)

pipeline.Execute(ctx, input, os.Stdout, os.Stderr)
```

## 📖 **Learn More**

- **[📘 Documentation](https://github.com/yupsh/yupsh/wiki)**: Core concepts, philosophy, and detailed guides
- **[🚀 Examples](https://github.com/yupsh/yupsh/tree/main/examples)**: Real shell scripts vs yupsh Go programs
- **[🔧 Framework](https://github.com/yupsh/yupsh/tree/main/framework)**: Build your own commands
- **[💬 Discussions](https://github.com/yupsh/yupsh/discussions)**: Ask questions and share ideas

---

<p align="center">
  <strong>yupsh</strong> - Shell-style programming in Go<br>
  <a href="https://github.com/yupsh/yupsh">GitHub</a> •
  <a href="https://github.com/yupsh/yupsh/wiki">Documentation</a> •
  <a href="https://github.com/yupsh/yupsh/tree/main/examples">Examples</a>
</p>
