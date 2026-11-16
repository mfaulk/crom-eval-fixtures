# crom-eval-fixtures

Pinned snapshots of upstream Rust repositories used as fixtures for the L3
capability tasks in [crom-rs](https://github.com/mfaulk/crom-rs)
(`crates/crom-eval/eval/tasks/l3-*`).

Each top-level `<task-id>/` directory is one upstream repository at the parent
commit of a historical bug fix, with its dependencies vendored (`vendor/` plus
`.cargo/config.toml`) so the task builds and tests with `--offline` and no
network. Nothing in a task directory reveals the fix: the bug report, hidden
tests, and reference patch live only in crom-rs.

`provenance/<task-id>.toml` records the upstream URL, license, base commit,
and fix commit. These files sit outside the task directories because a task
directory is copied verbatim into the agent's workspace.

## How crom uses this repository

A task's `task.toml` pins a fixture by full commit SHA:

```toml
[fixture]
kind = "git"
repo = "https://github.com/mfaulk/crom-eval-fixtures"
rev = "<40-hex commit sha>"
path = "<task-id>"
```

crom fetches that commit once into its fixture cache
(`$CROM_EVAL_FIXTURE_CACHE`, default `~/.cache/crom/eval-fixtures`), checks
that `HEAD` equals `rev`, and copies `path` into each run's workspace.
Existing commits are never rewritten; changing a fixture means a new commit and
a new `rev` in the task.

## Licenses

Each task directory is third-party code under its upstream license; the
upstream `LICENSE*` files are kept in place, and the SPDX expression is in
`provenance/<task-id>.toml`. Vendored crates under `vendor/` carry their own
licenses. This README and the `provenance/` files are available under
Apache-2.0 OR MIT.
