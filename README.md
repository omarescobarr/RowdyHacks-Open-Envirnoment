# Portable Agent Toolkit

A repository-scoped toolkit for carrying one set of personal operating principles and reusable agent workflows into different codebases without flattening their local context.

The model is deliberately asymmetric:

- `main` is the generic, portable base.
- Each initialized repository owns its tailored `AGENTS.md` and `.agents/skills/` after onboarding.
- Long-lived `workspace/<host>/<owner>/<repo>` branches in this repository are standard completion snapshots for comparison and sustainable upstream improvements. They are not live dependencies or automatic synchronization channels.

Initialization is a one-time deterministic inventory and staging pass followed by an agent-guided reconciliation. Existing repository instructions are reported, not silently replaced. Repository-specific technical, safety, ownership, architecture, and validation rules take precedence where they conflict with generic preferences.

## What ships

- `AGENTS.md`: the personal operating-preferences template, tailored during onboarding.
- `skills/`: the generic `build-from-plan`, `discovery`, `grilling`, `show-me`, `writing-session`, and `workspace-onboarding` skills.
- `toolkit.json`: the canonical manifest used by the workspace helper and verifier.
- `scripts/workspace_toolkit.py`: inventory, initialization, validation, guarded snapshot commands, and explicit X companion setup/diagnostics.
- `overlay/`: an optional read-only X/Twitter discovery companion. It is not installed or configured by workspace initialization.

`writing-session` owns its PRD and specification guidance as references. `discovery` routes ordinary research, OpenAI documentation, explicit Deep Research, and optional X discovery without copying runtime-managed specialist skills into this repository. The intentionally retired `web-perf`, `write-prd`, and `write-spec` top-level skills are not shipped.

## Initialize a repository

Requirements are Python 3.9 or newer and Git. Start from a clone of this toolkit:

```sh
git clone git@github.com:minh-mhl-le/portable-agent-toolkit.git
cd portable-agent-toolkit
python3 scripts/verify.py

python3 scripts/workspace_toolkit.py init /path/to/repository --dry-run
python3 scripts/workspace_toolkit.py init /path/to/repository
```

The initializer records the repository's existing agent instructions and skills, stages the generic template and declared skills, and vendors the helper into `.agent-toolkit/bin/`. It does not mutate user-level Codex configuration, install global skills, overwrite existing repository instructions, commit, branch, or push.

Then start or restart Codex in the target repository and invoke:

```text
$workspace-onboarding
```

If initialization reported an existing skill with that same name, direct the agent to read `.agent-toolkit/staged/skills/workspace-onboarding/SKILL.md` instead of assuming the existing route has this workflow.

The onboarding skill reads the full inventory and staged candidates, inspects the repository, reports meaningful overlaps or conflicts, and works through user-owned choices one high-leverage question at a time. Only after the user confirms the end state does it tailor and validate the repo-owned `AGENTS.md` and `.agents/skills/` suite, mark onboarding ready, and publish the standard workspace snapshot. That narrow snapshot push is part of finishing unless the user explicitly opts out.

Validate the result from the repository root:

```sh
python3 .agent-toolkit/bin/workspace_toolkit.py validate .
```

The target control plane is:

```text
AGENTS.md                         tailored repository instructions
.agents/skills/                  tailored repository skills
.agent-toolkit/inventory.json    deterministic instruction/skill inventory
.agent-toolkit/state.json        provenance and onboarding lifecycle
.agent-toolkit/staged/           generic candidates used for reconciliation
.agent-toolkit/bin/              vendored workspace helper
```

The initializer is idempotent: rerunning it refreshes its own generated inventory and staging while preserving repo-owned materialized files. Use inventory independently when you only need a current report:

```sh
python3 scripts/workspace_toolkit.py inventory /path/to/repository
```

## Workspace snapshots

Finishing onboarding or a later repository-toolkit reconciliation publishes the target's agent configuration into a dedicated branch of the toolkit remote. This makes per-repository tailoring reviewable without turning that branch into the workspace's source of truth.

`$workspace-onboarding` runs this after the ready state validates:

```sh
python3 .agent-toolkit/bin/workspace_toolkit.py snapshot . --push
```

The helper uses the toolkit origin recorded at initialization and returns the workspace branch and snapshot commit. Supply another toolkit remote only when intentionally overriding that provenance:

```sh
python3 .agent-toolkit/bin/workspace_toolkit.py snapshot . \
  --toolkit-repo git@github.com:minh-mhl-le/portable-agent-toolkit.git \
  --push
```

Snapshotting uses an isolated temporary clone, refuses unsafe exports, and never switches or commits the target repository. Only root `.agents/skills/<name>/` skill trees are snapshot-eligible; other discovered `SKILL.md` files remain visible in inventory but cannot pull broad product directories into an export. The snapshot also excludes secrets, credentials, runtime data, and unrelated product files. This standard completion push is the only automatic Git exception: later global improvements still must be reasoned about and deliberately ported, with no background pull, merge, or target-repository push.

Snapshot identity comes from the target's canonical Git remote. Nested hosted namespaces are preserved in the branch owner component. An unremoted repository falls back to `local/unowned/<directory-name>`; give such workspaces unique directory names or add a canonical remote before finishing to avoid identity ambiguity. Authentication failures leave the local lifecycle ready but snapshot publication pending; authenticate externally and rerun the command.

## Develop the generic toolkit

Keep `main` repository-agnostic. Make durable improvements on a review branch, update `toolkit.json` when the shipped skill set changes, and keep repository-specific adaptations on workspace snapshot branches.

Run the offline checks before publishing:

```sh
python3 scripts/verify.py
python3 -m unittest discover -s tests
```

See [MANIFEST.md](MANIFEST.md) for packaging boundaries and [DEPENDENCIES.md](DEPENDENCIES.md) for the core and optional runtime chain.

## Optional X discovery

The X companion remains opt-in because it needs a separately installed pinned CLI and authenticated runtime secrets. Workspace initialization does not install it or edit user-level Codex configuration. Check the host at any time:

```sh
python3 scripts/workspace_toolkit.py doctor-x
```

Run the explicit setup command from a toolkit clone to install or repair the pinned `twitter` CLI, copy the credential-free companion into a stable versioned location under `${CODEX_HOME}`, and register its MCP launcher in `${CODEX_HOME}/config.toml`:

```sh
python3 scripts/workspace_toolkit.py setup-x
```

An onboarded repository can run the vendored helper instead. It uses the nearby toolkit source when available and otherwise fetches the exact toolkit commit recorded in `.agent-toolkit/state.json`:

```sh
python3 .agent-toolkit/bin/workspace_toolkit.py doctor-x
python3 .agent-toolkit/bin/workspace_toolkit.py setup-x .
```

Setup never accepts or prints credentials. Provide both `TWITTER_AUTH_TOKEN` and `TWITTER_CT0` through the environment or the untracked `${CODEX_HOME}/secrets/x-discovery.env` file, then start a new Codex task before using the MCP tools. A successful setup with missing credentials reports `needs_auth`; it does not copy secrets into the repository. See [DEPENDENCIES.md](DEPENDENCIES.md) and [the companion README](overlay/lib/x-discovery/README.md) for the pinned driver and read-only authentication boundary.
