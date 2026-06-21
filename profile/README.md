# yupsh

> **An interactive shell built entirely from [gloo-foo](https://github.com/gloo-foo) commands.**

yupsh gathers the [gloo-foo](https://github.com/gloo-foo) commands — the typed, composable pipeline pieces — into a single interactive program. That is the whole idea: every command you run is a gloo-foo command, and every pipeline you build is a gloo-foo pipeline, assembled live at a prompt instead of wired together in code.

Because it is built on that one foundation, a happy consequence follows: it feels and behaves like the shell you already know. You name a tool, hand it flags, pipe its output into the next — the familiar muscle memory works exactly as expected. But there are no external coreutils underneath and no subprocesses for the built-in tools. The whole thing is one self-contained binary, composing typed streams in a single process.

A shell by consequence, not by imitation. The commands come first; the shell-like feel is what you get for free when all of them are gloo-foo.

## Explore

- **[yup.sh](https://yup.sh)** — the project home and command reference
- **[repl](https://github.com/yupsh/repl)** — the interactive shell
- **[Commands](https://github.com/orgs/yupsh/repositories?q=bin-)** — the built-in toolset

---

<p align="center"><em>gloo-foo commands, gathered into a single-binary shell.</em></p>
