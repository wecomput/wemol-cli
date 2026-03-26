# wemol-cli-official

Official distribution and documentation entry for `wemol-cli` and the `wemol-cli-official` skill package.

This repository is designed for two usage paths:
- **CLI path**: run `wemol-cli` directly for module/flow/job operations.
- **Agent path**: install the skill package in claw (or any skill-capable agent) and let the agent apply built-in install/update workflows.

## What's new in v1.1.0

- Added the `docs` command group: `docs list`, `docs get`, and `docs search`, backed by a unified public bilingual documentation set with stable short IDs that can be reused across languages.
- Improved `module search`, `flow search`, and `job search` with Markdown/doc-based recall, hit explanations, and `matched_languages` so matches can be traced to Chinese, English, or both.
- Upgraded cache and query performance with incremental sync, local SQLite/FTS indexing, delete reconciliation, and single-flight deduplication.
- Replaced `job output` with `job result`; result previews are more readable by default, binary outputs are summarized with a download hint, and `job download` restores upstream input artifacts.
- Added safer execution guards including `job submit --dry-run`, strict unknown argument/subcommand errors, and more reliable upload/download and submit-id recovery behavior.
- Improved CLI observability with `--describe`, clearer `job wait` / `job diagnose` / list/search output, and stricter confirmation guards for write and high-risk operations.

## Who this is for

- **CLI users**: want command-line control for discovery, submission, tracking, and output download.
- **Bio/AI/chem workflow users**: want practical workflows (antibody, ADMET, flow recovery, etc.).
- **Agent users**: want to use a skill package and delegate setup/operations to an agent.

## Install

### Primary: GitHub Releases

Use release assets from:
- https://github.com/wecomput/wemol-cli/releases

For `v1.1.0`, the release assets are:
- `wemol-cli-v1.1.0-linux-x86_64-musl.tar.gz`
- `wemol-cli-v1.1.0-macos-apple-silicon.zip`
- `wemol-cli-v1.1.0-macos-intel.zip`
- `wemol-cli-v1.1.0-windows-x86_64.zip`
- `wemol-cli-official.tar.gz` (skill package)
- `SHA256SUMS`

Recommended approach:
1. Open the target release (for example `v1.1.0`).
2. Download the matching binary archive for your platform.
3. Add the binary directory to `PATH` if needed.
4. Verify with:

```bash
wemol-cli --help
wemol-cli --version
```

If your release provides installer scripts, run the script from that release asset directly.

### Fallback: Official web installer

If you prefer the hosted installer channel:

```bash
curl -LsSf https://wemol.wecomput.com/static/wemol-cli/latest/install.sh | sh
```

Windows PowerShell:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://wemol.wecomput.com/static/wemol-cli/latest/install.ps1 | iex"
```

## Quick start (CLI)

```bash
wemol-cli --help
wemol-cli host
wemol-cli lang
wemol-cli login
wemol-cli docs search antibody humanization
wemol-cli flow search antibody humanization
wemol-cli module search antibody numbering
```

Typical flow:
1. Orient with public docs: `docs search`, `docs list`, `docs get`
2. Discover execution candidates: `flow search` or `module search`
3. Inspect schema: `module get <id> --params-json` or `flow get <id> --params-template`
4. Validate before submit when needed: `job submit ... --dry-run`
5. Submit: `job submit ...`
6. Track: `job status`, `job progress`, `job wait`, `job diagnose`
7. Recover outputs: `job tasks`, `job result`, `job download`

## Use with agents (skill package)

`wemol-cli-official` can be consumed as a standalone skill package.

You can:
- install `wemol-cli-official` into claw or any compatible skill-capable agent, or
- download only `wemol-cli-official.tar.gz` from release assets and let the agent perform install/update guidance automatically.

The skill includes operational workflows for:
- install/update checks
- auth/session/host/lang handling
- docs-first capability discovery with `docs search` / `docs list` / `docs get`
- module/flow parameter discovery
- dry-run validation, job diagnosis, and result/download recovery
- agent-aware CLI usage constraints exposed through `--describe`

## Repository contents

- [Agent operating manual](SKILL.md)
- [Install reference](references/install.md)
- [Requirement to execution reference](references/requirement-to-execution.md)

## Release artifacts (suggested)

Current `v1.1.0` artifacts:
- `wemol-cli-v1.1.0-linux-x86_64-musl.tar.gz`
- `wemol-cli-v1.1.0-macos-apple-silicon.zip`
- `wemol-cli-v1.1.0-macos-intel.zip`
- `wemol-cli-v1.1.0-windows-x86_64.zip`
- `wemol-cli-official.tar.gz`
- `SHA256SUMS`

## FAQ

### Authentication required
Run:

```bash
wemol-cli login
wemol-cli account
```

### How do I search the public docs first?
Run:

```bash
wemol-cli docs search antibody humanization
wemol-cli docs list
wemol-cli docs get <doc_id>
```

### Output key is missing but job seems done
Run:

```bash
wemol-cli job result <job_id>
wemol-cli job download <job_id>
```

`job result` previews structured keys. For binary artifacts, use `job download`.

### Security prompts on first launch
Current macOS/Windows builds may trigger Gatekeeper/SmartScreen prompts on first run. This is expected until full signing/notarization rollout.
