# mise upgrade --bump on Windows, mise install on Linux

Expectation: a tool upgrade is OS-agnostic. `lockfile_platforms = ["linux-x64", "windows-x64"]` names both platforms,
so a `mise.lock` bumped on Windows installs on Linux.

The repository holds one tool, `hk`, set to `latest`, and a committed `mise.lock` that locks the outdated `2.4.0` for
both platforms. The lock is in the format an earlier mise wrote: no `lockfile_version`, backend `aqua:jdx/hk`.

The workflow is one chain. Each job starts after the one above it.

| Job | Runner | What it does |
|:---|:---|:---|
| 1. Linux: mise install the outdated hk from the committed lock | ubuntu-latest | `mise install --locked`, prints the hk version |
| 2. Windows: mise upgrade --bump, then mise install | windows-latest | `mise upgrade --bump`, prints the diff, `mise install --locked`, prints the hk version, uploads `mise.toml` and `mise.lock` |
| 3. Linux: mise install the lock that Windows bumped | ubuntu-latest | `mise install --locked`, `mise install`, prints the hk version, fails if either rewrote the files |
