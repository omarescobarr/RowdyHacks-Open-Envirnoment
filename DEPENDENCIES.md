# Dependencies

## Core workspace flow

- Python 3.9 or newer. The initializer, inventory, validator, snapshot helper, writing-preservation check, and tests use the standard library.
- Git for repository identity and isolated workspace snapshot branches.
- Codex or another agent runtime that supports repository-scoped `AGENTS.md` and `SKILL.md` folders.

No Node.js package chain, plugin installation, global skill installation, or user-level Codex configuration change is required. Codex discovers repository skills under `.agents/skills`; see the [official skills documentation](https://learn.chatgpt.com/docs/build-skills#where-to-save-skills).

## Optional X/Twitter discovery

The bundled wrapper has no third-party Python package dependencies. Functional X access additionally requires:

- Python 3.10 or newer for the upstream command driver.
- [`uv`](https://docs.astral.sh/uv/) for the documented pinned installation path.
- [`public-clis/twitter-cli`](https://github.com/public-clis/twitter-cli) at commit [`7c634e0d396b1e7af9f63315b414925fe4f29ae7`](https://github.com/public-clis/twitter-cli/commit/7c634e0d396b1e7af9f63315b414925fe4f29ae7), reported version `0.8.6`. Its executable is `twitter`.
- `TWITTER_AUTH_TOKEN` and `TWITTER_CT0` supplied through the environment or an untracked secret file. Credentials are never part of this toolkit or a workspace snapshot.

Install the pinned command driver explicitly:

```sh
uv tool install --force \
  'twitter-cli @ git+https://github.com/public-clis/twitter-cli.git@7c634e0d396b1e7af9f63315b414925fe4f29ae7'
```

The optional launchers require a POSIX-compatible shell. `gh` or a GitHub-native connector can improve GitHub discovery but is not required for the toolkit to initialize or validate.

Run `python3 scripts/workspace_toolkit.py setup-x` (or the vendored helper's equivalent) to install the pinned driver and register the stable MCP launcher. The command supports macOS and Linux, preserves other Codex MCP sections, and may fetch the exact toolkit commit recorded by an initialized workspace when no local toolkit clone is available. `doctor-x` is read-only and returns distinct `missing_runtime`, `missing_cli`, `missing_mcp`, and `needs_auth` states. A setup run does not make credentials available automatically; authentication remains an explicit environment or untracked secret-file concern.
