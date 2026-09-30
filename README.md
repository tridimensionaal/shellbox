# Shellbox

A small framework for organizing Zsh aliases, functions, and scripts into
domains.

It uses regular shell files and one small loader, so the whole setup is easy to
inspect.

## How it works

```text
.zshrc -> setup/init -> domains -> aliases + functions + scripts
```

`setup.sh` adds one source line to `.zshrc`. The init script then:

- sources aliases and functions;
- links scripts into `bin/`; and
- adds `bin/` to `PATH`.

That is the whole mechanism. There is no plugin manager or manifest.

### What Is a Domain

A domain is a directory that groups related aliases, functions, and scripts.

Shellbox has two domain directories:

- `domains/` contains custom helpers and is ignored by Git.
- `example-domains/` contains the helpers I use on my machines.

### List Helpers

Show every alias, function, and script found in both `example-domains/` and
`domains/`, grouped by domain:

```sh
shellbox-list
```

Example output:

```text
shellbox
└── git
    ├── aliases
    │   ├── alias gdiscard='git restore --worktree --'
    │   ├── alias gp='git push'
    │   ├── alias gpl='git pull --ff-only'
    │   └── alias gs='git status'
    ├── functions
    │   └── gd
    └── scripts
        └── gh-init
```

## Setup

```sh
git clone https://github.com/tridimensionaal/shellbox.git "$HOME/.shellbox"
cd "$HOME/.shellbox"
./setup.sh
```

Setup creates `domains/` and adds this block to `.zshrc`:

```zsh
# ---start_of_shellbox_setup---
# managed by Shellbox (do not edit inside this block)
source "/path/to/shellbox/setup/init" --domains-only
# ---end_of_shellbox_setup---
```

Open a new shell after setup.

### Example Domains vs. Custom Domains

The default setup loads only custom domains from `domains/`. To also load the
included `example-domains/`:

```sh
./setup.sh --with-examples
```

Example domains load first and custom domains load afterward. Running setup
again switches between the two modes without adding another block.

The included domains cover files, Git, grep, navigation, processes, Python,
and tmux. See [example-domains/README.md](./example-domains/README.md) for their
commands and requirements.

### Create a Domain

Create a directory under `domains/`:

```text
domains/git/
├── aliases
├── functions/
│   └── gd
└── scripts/
    └── gh-init
```

`aliases` contains normal alias definitions:

```zsh
alias gs='git status'
```

Files under `functions/` have a shebang and define a function:

```bash
#!/usr/bin/env bash

gd() {
    command git diff "$@"
}
```

These files are sourced, not executed. The shebang helps editors identify the
shell syntax, and Shellbox uses it to recognize function files. A `zsh`
shebang also tells Shellbox to skip that file when running under Bash.

Files under `scripts/` are standalone scripts with a shebang. Their filenames
become commands on `PATH`:

```bash
#!/usr/bin/env bash

gh repo create "$@"
```

No registration is needed. Open a new shell and the domain is loaded.

An example domain can also be copied as a starting point:

```sh
cp -R example-domains/git domains/git
```

### Keep Custom Domains in Git

`domains/` is ignored by the main repo. It can be its own Git repository:

```sh
git -C domains init
git -C domains add .
git -C domains commit -m "Add my shell domains"
```

Since `domains/` is ignored, deleting the Shellbox directory also deletes those
local files unless they are stored somewhere else.

### Other Setup Options

```sh
./setup.sh --dry-run
./setup.sh --rc-file "$HOME/.zshrc"
./setup.sh --shell bash
```

If you are unsure how to use `setup.sh`, run `./setup.sh --help` to see the
available options.

Zsh uses `${ZDOTDIR:-$HOME}/.zshrc` by default. Bash support is kept where it
stays simple, but Zsh is the main target.

### Upgrading from the Old Layout

The old version loaded helpers from `modules/`. Run
`./setup.sh --with-examples` to keep loading the included helpers, then move any
personal domains left in `modules/` into `domains/`.

Old generated script links are cleaned the next time Shellbox loads.
