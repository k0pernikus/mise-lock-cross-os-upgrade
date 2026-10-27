# mise install on Linux and Windows, mise upgrade --bump on Windows

Expectation: a tool upgrade is OS-agnostic. `lockfile_platforms = ["linux-x64", "windows-x64"]` names both platforms,
so a `mise.lock` bumped on Windows installs on Linux. `mise install --locked` installs exactly the locked version
and fails when the lock lacks it.

The repository holds one tool, `hk`, set to `latest`, and a committed `mise.lock` that locks the outdated `2.4.0` for
both platforms. The lock is in the format an earlier mise wrote: no `lockfile_version`, backend `aqua:jdx/hk`.

The workflow is one chain. Each job starts after the one above it. Job 3 runs even when job 2 fails.

| Job | Runner | What it does |
|:---|:---|:---|
| 1. Linux: mise install --locked (outdated hk, committed lock) | ubuntu-latest | `mise install --locked`, prints the hk version |
| 2. Windows: mise install --locked (same committed lock) | windows-latest | `mise install --locked`, prints the hk version |
| 3. Windows: mise upgrade --bump, then mise install --locked | windows-latest | `mise upgrade --bump`, prints the diff, `mise install --locked`, prints the hk version, uploads `mise.toml` and `mise.lock` |
| 4. Linux: mise install --locked with the lock Windows bumped | ubuntu-latest | `mise install --locked`, prints the hk version, fails if the install rewrote the files |
