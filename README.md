# mise upgrade --bump on one OS, mise install on the other

Expectation: a tool upgrade is OS-agnostic. `lockfile_platforms = ["linux-x64", "windows-x64"]` names both platforms,
so a `mise.lock` bumped on either OS installs on the other.

The repository holds one tool, `hk`, set to `latest`, and a committed `mise.lock` that locks the outdated `2.4.0` for
both platforms. The lock is in the format an earlier mise wrote: no `lockfile_version`, backend `aqua:jdx/hk`.

| Job | Runner | What it does |
|:---|:---|:---|
| 1. Linux: mise install the outdated hk from the committed lock | ubuntu-latest | `mise install --locked`, prints the hk version |
| 2a. Windows: mise upgrade --bump the committed lock | windows-latest | `mise upgrade --bump`, prints the diff, uploads `mise.toml` and `mise.lock` |
| 3a. Linux: mise install the lock that Windows bumped | ubuntu-latest | `mise install --locked`, `mise install`, prints the hk version, fails if either rewrote the files |
| 2b. Linux: mise upgrade --bump the committed lock | ubuntu-latest | `mise upgrade --bump`, prints the diff, uploads `mise.toml` and `mise.lock` |
| 3b. Windows: mise install the lock that Linux bumped | windows-latest | `mise install --locked`, `mise install`, prints the hk version, fails if either rewrote the files |

Jobs 2a and 2b start after job 1. Job 3a follows 2a, and job 3b follows 2b.
