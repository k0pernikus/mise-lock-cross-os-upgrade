# mise lock written by `mise upgrade --bump` on one OS, installed on the other

Expectation: a tool upgrade is OS-agnostic. `lockfile_platforms = ["linux-x64", "windows-x64"]` names both platforms,
so a `mise.lock` bumped on either OS installs on the other.

The repository holds one tool, `hk`, pinned to the outdated `2.4.0`, and the `mise.lock` that mise 2026.10.19 wrote on
Linux for it.

```sh
mise install
```

The workflow has four jobs.

| Job | Runner | Command |
|:---|:---|:---|
| `bump-on-windows` | windows-latest | `mise upgrade --bump`, uploads `mise.toml` and `mise.lock` |
| `install-on-linux-the-windows-lock` | ubuntu-latest | `mise install --locked`, `mise install`, then fails if either rewrote the files |
| `bump-on-linux` | ubuntu-latest | `mise upgrade --bump`, uploads `mise.toml` and `mise.lock` |
| `install-on-windows-the-linux-lock` | windows-latest | `mise install --locked`, `mise install`, then fails if either rewrote the files |
