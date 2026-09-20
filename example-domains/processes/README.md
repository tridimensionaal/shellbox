# Processes domain

Shell helpers for inspecting and managing processes.
The termination helper requires Bash and Linux `pgrep` (procps-ng, with
`--ignore-ancestors` support).

## Detached commands

Launch a command without keeping it in the current Zsh job table or connecting
it to the terminal's input and output:

```zsh
detach discord
detach some-gui-program --some-option
```

`detach` is Zsh-specific. It backgrounds and immediately disowns the command,
redirects standard input from `/dev/null`, and discards standard output and
standard error. If the command does not start correctly, run it normally to see
its error output.

## Firefox

```sh
firefox-kill            # send SIGTERM
firefox-kill --force    # send SIGKILL when Firefox stays frozen
```

The alias matches `firefox`, `firefox-bin`, and `firefox-esr`, plus their child
processes even when they have different names. It includes all your matching
instances across profiles. Forced termination can lose unsaved work.

## Other programs

```sh
kill-program chromium
kill-program --force chromium
kill-program --force 'firefox(-bin|-esr)?'
```

Supply one regular expression matched against the whole process name, not the
command line. Linux process names are limited to 15 characters. Quote patterns
containing shell special characters, as in the Firefox example above.

Only your effective user's processes are targeted. The command excludes itself,
its ancestors (including the calling shell). It recursively finds children with
`pgrep -P`, collects their PIDs, then signals children before parents. Children
created after collection and detached helpers with different names may be
missed. It sends the signal without waiting or automatically escalating.

## Port owner

Show which processes use a local TCP or UDP port:

```sh
port-owner 3000
```

Requires Bash and `ss` from iproute2. The output includes the protocol, socket
state, local and peer addresses, and available process names and PIDs. It covers
IPv4 and IPv6, TCP listeners and connections, and both unconnected and connected
UDP sockets. Connections using the number only as a remote port are excluded.

Process details belonging to other users may require elevated permissions. An
unused port produces only the column headings and exits successfully. Run
`port-owner --help` for usage.

## Enable

Run `./setup.sh --with-examples`, or copy only this domain:

```sh
cp -R example-domains/processes domains/processes
```

Then open a new shell.
