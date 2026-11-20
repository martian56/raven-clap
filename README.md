# raven-clap

[![CI](https://github.com/martian56/raven-clap/actions/workflows/ci.yml/badge.svg)](https://github.com/martian56/raven-clap/actions/workflows/ci.yml)

Command-line argument parsing for Raven. You describe the flags, options, and
positionals your program takes, hand it the process arguments, and get back a
small object to read the values out of. The same description also builds a
`--help` screen. No dependencies.

## Install

```toml
[dependencies]
"github.com/martian56/raven-clap" = "v0.1.0"
```

## Usage

```rust
from "github.com/martian56/raven-clap" import Command, Arg
from std.env import args

fun main() {
    let cmd = Command.new("greet").version("1.0").about("a tiny greeter").args(
        [
            Arg.flag("loud", "l", "shout the greeting"),
            Arg.option("times", "n", "how many times to greet"),
            Arg.positional("name", "who to greet"),
        ],
    )

    match cmd.parse(args()) {
        case Ok(m) {
            if m.help() {
                print(cmd.usage())
            } else {
                let who = m.get_or("name", "world")
                let loud = m.flag("loud")
            }
        }
        // ...
        case Err(e) { print(e.to_string()) } // "unknown argument: --whoops"
    }
}
```

## What it parses

`--name value`, `--name=value`, and the short `-n value` for options; `--verbose`
or `-v` for flags; and positionals, which are matched to their specs in the order
you declared them. `--help` and `-h` are recognized whether or not you declared
them, so check `m.help()` and print `cmd.usage()`.

Parsing stops at the first problem and says what it was: an unknown argument, an
option with nothing after it, or a required option that wasn't given.

## Describing an argument

| Constructor | What it makes |
|-------------|---------------|
| `Arg.flag(name, short, help)` | A boolean flag |
| `Arg.option(name, short, help)` | An option that takes a value |
| `Arg.required(name, short, help)` | An option that must be given |
| `Arg.positional(name, help)` | A positional argument |

Pass `""` for `short` when there isn't a short form.

## Reading the result

| Method | Description |
|--------|-------------|
| `m.flag(name)` | Was the flag set (or the option/positional given)? |
| `m.get(name)` | The value, as `Option[str]` |
| `m.get_or(name, fallback)` | The value, or `fallback` |
| `m.positional(i)` | The i-th positional, as `Option[str]` |
| `m.rest()` | Every positional, in order |
| `m.help()` | Was `--help` or `-h` passed? |

## Help output

`cmd.usage()` gives you something like this:

```
greet 1.0
a tiny greeter

USAGE:
  greet [OPTIONS] <name>

OPTIONS:
  -l, --loud   shout the greeting
  -n, --times  how many times to greet
  <name>       who to greet
  -h, --help   show this help
```

## License

MIT
