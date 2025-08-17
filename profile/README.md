# yup

> **A shell for [gloo-foo](https://github.com/gloo-foo) commands**

yup is an interactive shell that parses standard Unix pipeline syntax and executes it entirely within a single Go process using [gloo-foo](https://github.com/gloo-foo) commands. No subprocesses, no coreutils — just gloo-foo.

## How It Works

Type shell commands the way you always have. yup parses the pipeline, resolves each command to its gloo-foo implementation, and runs the entire pipeline in-process:

```
yup> cat access.log | grep 503 | cut -d' ' -f1 | sort | uniq -c | sort -rn | head -5
```

Every command in the pipeline resolves to a gloo-foo function call. yup is the translation layer between shell syntax and those calls.

## Syntax

yup accepts a subset of POSIX shell syntax, mapped directly to gloo-foo:

### Commands and Arguments

```
command [flags] [args...]
```

```
yup> grep -i error server.log
       ^^^^                        → command name
            ^^                     → flag (case-insensitive)
               ^^^^^              → pattern argument
                     ^^^^^^^^^^   → file argument
```

### Pipelines

```
command1 | command2 | command3
```

Each `|` connects stdout of the left command to stdin of the right — no intermediate buffering, no subprocess pipes.

### Flags

Short flags (`-n`, `-i`) and long flags (`--count`, `--ignore-case`) work the way you'd expect:

```
yup> sort -rn
yup> cut -d',' -f1,3
yup> head -n 20
yup> uniq -c
yup> wc -l
```

### Quoting

Single quotes, double quotes, and backslash escaping work as expected:

```
yup> grep "hello world" file.txt
yup> echo 'single quoted'
yup> grep hello\ world file.txt
```

### Redirections (planned)

```
command > output.txt       # redirect stdout to file
command >> output.txt      # append stdout to file
command < input.txt        # redirect file to stdin
command 2> errors.txt      # redirect stderr to file
```

Until redirections are implemented, use `tee` for output capture:

```
yup> grep ERROR log.txt | tee matches.txt
```

## What yup Is Not

yup is not a general-purpose shell. There are no variables, no control flow, no job control, no process management. It is a REPL for composing and running gloo-foo pipelines using familiar shell syntax.

---

[yup.sh](https://yup.sh)
