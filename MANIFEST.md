# Toolkit Manifest

[`toolkit.json`](toolkit.json) is the machine-readable source of truth for the portable base. This document records the human-facing packaging decisions.

## Included skills

| Skill | Role | Packaging decision |
| --- | --- | --- |
| `build-from-plan` | Core | General autonomous implementation workflow. |
| `discovery` | Core router | Routes source-dependent work to focused references or runtime-managed specialists. |
| `grilling` | Core | One-question-at-a-time alignment and blind-spot workflow. |
| `show-me` | Core | Portable visual-explanation workflow without platform-specific open commands. |
| `writing-session` | Core router | Owns PRD, specification, editorial, and approved voice guidance as focused references. |
| `workspace-onboarding` | Onboarding | Reconciles staged preferences and skills with an inventoried repository after deterministic initialization. |

## Included behavior and tooling

- Root `AGENTS.md` is the generic personal-preferences template. It is a starting point for repository onboarding, not a file to overwrite local instructions with verbatim.
- `scripts/workspace_toolkit.py` performs deterministic inventory, staging, validation, guarded snapshot export, and explicit host-level X companion setup/diagnostics. A copy is vendored into each initialized repository.
- `.agent-toolkit/` holds the target repository's inventory, staging, provenance, and onboarding lifecycle.
- The target repository's resulting `AGENTS.md` and `.agents/skills/` are authoritative after onboarding.
- Workspace snapshot branches are standard completion records of target-repository agent configuration. They are not deployment branches or synchronization authorities.
- `overlay/lib/x-discovery` is an optional generalized read-only companion with no runtime data or credentials. Its launchers resolve packaged source without machine-specific paths.

## Intentionally excluded

- `web-perf`, which was intentionally removed upstream.
- Top-level `write-prd` and `write-spec` compatibility skills; their guidance belongs under `writing-session`.
- Any repository-specific skill, command, ownership boundary, infrastructure fact, or local path from the source workspace.
- Plugin packaging and global installers. Repository initialization is the supported deployment flow.
- User-level Codex configuration mutation during ordinary initialization, lifecycle hooks, target-repository Git checkpoints, or background snapshot synchronization. `setup-x` is the explicit opt-in exception for the optional X companion; the validated workspace snapshot push remains the sole narrow automatic Git exception.
- Codex-managed system and plugin-cache skills, which remain owned by their runtime or plugin.
- Credentials, secret files, authenticated caches, cookies, databases, raw X responses, exports, logs, sessions, histories, generated caches, bytecode, and product source unrelated to agent configuration.

## Workspace snapshot boundary

The toolkit repository may be private, but snapshot exports still use a narrow allowlist and secret checks. Individual discovered agent-instruction files and canonical root `.agents/skills/<name>/` trees are eligible; noncanonical `SKILL.md` locations are inventory-only. Repo-specific operational instructions and tailored skills may be recorded because preserving their evolution is the point of the branch. Credentials, runtime state, and unrelated repository contents may not be recorded.

The upstream X command driver is referenced, not vendored. See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
