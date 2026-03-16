# wemol-cli-official

Official distribution and documentation entry for `wemol-cli` and the `wemol-cli-official` skill package.

This repository is designed for two usage paths:
- **CLI path**: run `wemol-cli` directly for module/flow/job operations.
- **Agent path**: install the skill package in claw (or any skill-capable agent) and let the agent apply built-in install/update workflows.

## Who this is for

- **CLI users**: want command-line control for discovery, submission, tracking, and output download.
- **Bio/AI/chem workflow users**: want practical workflows (antibody, ADMET, MHC-I, flow recovery, etc.).
- **Agent users**: want to use a skill package and delegate setup/operations to an agent.

## Install

### Primary: GitHub Releases

Use release assets from:
- https://github.com/wecomput/wemol-cli/releases

For `v1.0.0`, the release assets are:
- `wemol-cli-v1.0.0-linux-x86_64-musl.tar.gz`
- `wemol-cli-v1.0.0-macos-apple-silicon.zip`
- `wemol-cli-v1.0.0-macos-intel.zip`
- `wemol-cli-v1.0.0-windows-x86_64.zip`
- `wemol-cli-official.tar.gz` (skill package)
- `SHA256SUMS`

Recommended approach:
1. Open the target release (for example `v1.0.0`).
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
wemol-cli module search antibody
```

Typical flow:
1. Discover capability: `module search` or `flow search`
2. Inspect schema: `module get <id> --params-json` or `flow get <id> --params-template`
3. Submit: `job submit ...`
4. Track: `job status`, `job progress`, `job wait`
5. Recover outputs: `job tasks`, `job output`, `job download`

## Use with agents (skill package)

`wemol-cli-official` can be consumed as a standalone skill package.

You can:
- install `wemol-cli-official` into claw or any compatible skill-capable agent, or
- download only `wemol-cli-official.tar.gz` from release assets and let the agent perform install/update guidance automatically.

The skill includes operational workflows for:
- install/update checks
- auth/session/host/lang handling
- module/flow parameter discovery
- job diagnosis and output recovery

## Examples

- [Antibody numbering (variable region)](wemol-cli-official/examples/antibody-numbering-variable-region.md)
- [Protein physicochemical properties](wemol-cli-official/examples/protein-physicochemical-properties.md)
- [ADMET AI](wemol-cli-official/examples/admet-ai.md)
- [MHC-I binding prediction](wemol-cli-official/examples/mhc-i-binding-prediction.md)
- [Flow submit + download recovery](wemol-cli-official/examples/flow-submit-and-download-recovery.md)
- [Job history recovery](wemol-cli-official/examples/job-history-recovery.md)
- [Run-limit error handling](wemol-cli-official/examples/module-run-limit-error.md)

## References

- [Install](wemol-cli-official/references/install.md)
- [Session and host](wemol-cli-official/references/session-and-host.md)
- [Module workflow](wemol-cli-official/references/module-workflow.md)
- [Flow workflow](wemol-cli-official/references/flow-workflow.md)
- [Job workflow](wemol-cli-official/references/job-workflow.md)
- [Output and agent notes](wemol-cli-official/references/output-and-agent-notes.md)

## Release artifacts (suggested)

Current `v1.0.0` artifacts:
- `wemol-cli-v1.0.0-linux-x86_64-musl.tar.gz`
- `wemol-cli-v1.0.0-macos-apple-silicon.zip`
- `wemol-cli-v1.0.0-macos-intel.zip`
- `wemol-cli-v1.0.0-windows-x86_64.zip`
- `wemol-cli-official.tar.gz`
- `SHA256SUMS`

## FAQ

### Authentication required
Run:

```bash
wemol-cli login
wemol-cli account
```

### Output key is missing but job seems done
Run:

```bash
wemol-cli job tasks <job_id>
wemol-cli job download <job_id>
```

### Security prompts on first launch
Current macOS/Windows builds may trigger Gatekeeper/SmartScreen prompts on first run. This is expected until full signing/notarization rollout.
