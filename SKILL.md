---
name: wemol-cli-official
description: Use when a task may involve Wemol, a drug-discovery computing platform that integrates many biology, AI, and chemistry modules for workflows such as biologics design, small-molecule discovery, molecular simulation, ADMET prediction, virtual screening, structure prediction, antibody engineering, and immunogenicity analysis. This skill is the operating manual for the Wemol CLI client.
---

# Using wemol-cli

## Overview

`wemol-cli` is the operational client for Wemol public docs, modules, and flows. Use it to establish top-level task orientation first, then discover modules/flows, inspect real input schemas, submit jobs, track execution, and recover outputs.

Terminology guard:
- `job result` is a command for reading structured output keys.
- `output` is also a common default key name, not a universal result channel.
- Do not assume a key named `output` exists for every module/job.
- If `--name` is omitted, `job result` scans discovered keys for each task; text results are previewed, while known binary results are summarized with a download hint.

Primary rule:
- Prefer executing CLI commands and reading real output over assumptions.
- This skill is self-sufficient. When guidance conflicts, follow live `docs`, `--help`, `--doc`, and real command output.

This `SKILL.md` is now self-sufficient for normal operation.
Only open `references/` when installation details or requirement-to-submit gating need the expanded checklist.

## When To Use

Use when:
- user asks for a biology/chemistry/AI workflow that may map to Wemol modules or flows
- task requires live module params, job status, logs, outputs, downloads
- session/host/lang/install behavior affects task execution

Do not use when:
- task is SDK/CLI source implementation work
- user only asks for static code edits unrelated to running CLI

## Quick Start

When context is unknown:

```bash
wemol-cli --help
wemol-cli --version
wemol-cli host
wemol-cli lang
wemol-cli docs search antibody humanization
```

If authentication error appears, stop and run:

```bash
wemol-cli login
```

If interactive login is not possible (non-TTY), use:

```bash
wemol-cli login --username <name> --password <password>
```

## Quick Reference

| Situation | Required Action | Avoid |
|---|---|---|
| User asks capability but no ID | `docs search` first, then `flow search` / `module search` after top-level task orientation is established | guessing module/flow by name only |
| User asks requirement-to-execution | decompose requirement, run `docs search` first, use `docs list/get` when needed, then run flow/module search in parallel and gate by conditions | fixed linear script without candidate validation |
| Any submit intent | fetch schema (`--params-json` or `flow get`) first, then start from `submit_example` / `--params-template` when available | building payload from natural language |
| Need safe pre-submit validation | `job submit ... --dry-run` before real submit | assuming parsed params equal accepted params |
| Right before `job submit` | output execution summary (target, key params, risk tags); ask user only if gate fails or ambiguous | silent submit without rationale |
| Flow submit | enforce task-keyed JSON | flat JSON submit |
| Known `job_id` direct read/triage | go straight to `job status` / `job progress` / `job result` / `job logs` / `job diagnose` / `job download` | forcing docs-first before direct job operations |
| `job result` missing/empty | run `job result` without `--name` to inspect keys, then `job download` if needed | concluding job failed immediately |
| Auth/session issue | `login` / `account` / `host` / `lang` checks | continuing API probes while unauthenticated |
| User asks “need update?” | run update workflow and conclude directly | asking user to compare versions manually |

## Operating Policy (Normative)

Use this section as the default operating policy for Wemol execution work.

1. Discovery
- Decompose the requirement into `goal`, `inputs`, `constraints`, and `keywords` before selecting targets.
- If the task is a requirement-understanding, capability-selection, or workflow-planning entry point, run `docs search` first. Use `docs list/get` when needed, and do not enter `flow/module search` before docs establishes top-level task orientation.
- Use docs-first orientation to avoid drifting through `module/flow` discovery without a task-level understanding of the workflow shape.
- After docs-first orientation is complete, run `flow search` and `module search` in parallel.
- Direct operations with a known `job_id` (`job status/progress/result/logs/diagnose/download`) are exempt from the docs-first gate.
- For the same requirement, run at least two search rounds (`Chinese term + English term`) and add 2-4 aliases/synonyms.
- Treat bilingual multi-round search as required discovery work, not optional decoration. Keep going until both language directions are sampled or a blocking auth/runtime error stops the search itself.
- Merge and deduplicate results across language rounds, alias rounds, and both search surfaces before comparison.
- An empty result from one alias or from one side (`flow` or `module`) is not enough to eliminate a candidate family.
- Do not choose a candidate from a single one-shot search.

2. Selection
- Prefer flow first. User wording such as `module` or `module search` does not by itself justify skipping `flow search`.
- Use module only when flow does not fit: the flow does not satisfy the core goal, the flow input/constraints are incompatible, or the target capability only appears as an ancillary step in a broader flow and no sufficiently specialized flow exists; or when the requirement clearly allows single-module execution.
- Use `search` for discovery only; before recommendation or submit, run `flow get` / `module get` and validate parameter and input fit.
- Use `docs` to establish top-level task orientation first, then narrow terminology, workflow shape, and candidate families; treat live `flow/module search/get` as source of truth for final selection, version choice, and input/parameter fit.
- If flow candidates mix input forms, narrow with `flow search <query...> --input-type <fasta|pdb|...>`.
- Within the same module family, default to the latest stable version visible from live discovery. Older docs wording does not override a newer live candidate unless the user explicitly asks for an older version or the latest stable version is unavailable or constraint-incompatible.

3. Ask-user gate
- Ask the user first when a gate fails, candidates are materially ambiguous, or key conditions are still unconfirmed.
- When blocked, return up to 3 candidates with concrete differences and the blocking condition.

4. Pre-submit
- Before submit, emit an execution summary with `goal`, selected target, key params, and risk tags.
- Unless autonomous execution is already authorized, confirm with the user before `job submit`.
- Use `job submit ... --dry-run` when you need input-layer validation before the real submit.
- For healthy long-running jobs, prefer `job progress -> job wait` as the default path.
- Use `job diagnose` only when the job failed, stalled, timed out unexpectedly, or the next step is unclear.
- Use `job logs` after `job diagnose` identifies a suspect task, or when a specific failed task already needs stdout/stderr inspection.
- Use `job result` after `Done`, or earlier only when partial structured outputs are explicitly expected. If keys are unclear, run it without `--name` first.
- Fall back to `job download` when outputs remain unclear or file artifacts matter more than structured preview.

## Core Commands And Rules

### Global Options

```bash
wemol-cli --host <url> ...
wemol-cli --session-id <session_id> ...
wemol-cli --user-agent <ua> ...
wemol-cli --timeout <sec> ...
wemol-cli --secure ...
wemol-cli --verbose ...
wemol-cli --doc <command>
```

Environment equivalents:
- `WEMOL_HOST`
- `WEMOL_SESSION_ID`
- `WEMOL_USER_AGENT`

Rules:
- Prefer persisted `host --set` and cached login for routine work.
- Use `--host` / `--session-id` for one-off overrides.
- Use `--user-agent` only when explicit routing/debugging requires it.
- Global options must appear before subcommands.
- `--doc` should be treated as a global option and placed before subcommands. Prefer `wemol-cli --doc job result` over `wemol-cli job result --doc`.
- Unknown arguments are always rejected; there is no auto-recovery path. Fix the command from the error message and `--help` output.
- `--strict-args` is kept for compatibility but does not change parsing behavior.

### Host / Language / Session / Account

```bash
wemol-cli host
wemol-cli host --set https://wemol.wecomput.com
wemol-cli lang
wemol-cli lang --set en
wemol-cli lang --set cn
wemol-cli account
wemol-cli logout
```

Rules:
- `module get` and `module get --params-json` follow current language.
- `docs search` / `docs list` / `docs get` use the unified public docs set and do not switch to a single-language catalog based on current `lang`.
- use `account` to confirm active identity and resource summary.
- if auth fails, stop and run `wemol-cli login` before more task commands.
- use `logout` to clear current host session before relogin/switch account.

### Module Discovery And Inspection

```bash
wemol-cli module search antibody numbering
wemol-cli module search antibody numbering --all
wemol-cli module list --code anti
wemol-cli module list --name antibody --tag Biologics --tag Antibody --limit 20 --offset 0
wemol-cli module list --sort updated_at --desc true
wemol-cli module list --all
wemol-cli module get <module_id>
wemol-cli module get <module_id> --params-json
wemol-cli module get <module_id> --params-json --method "<method_name>"
```

Rules:
- `module list` and `module search` currently return enabled modules.
- `module list --tag` is repeatable and uses AND semantics.
- `module search` includes tag matching.
- `module get <module_id> --params-json` includes per-method `submit_example`; use it as a schema-aware starting template when payload shape is non-trivial.
- Before submit, always read `--params-json`; do not guess keys/types/options.

### Flow Discovery And Inspection

```bash
wemol-cli flow search antibody pipeline
wemol-cli flow search antibody pipeline --input-type fasta
wemol-cli flow list --name antibody --tag Featured --tag Humanization --limit 20 --offset 0
wemol-cli flow list --sort updated_at --desc true
wemol-cli flow list --input-type pdb
wemol-cli flow get <flow_id>
wemol-cli flow get <flow_id> --params-template
```

Rules:
- `flow list --tag` is repeatable and uses AND semantics.
- `flow search` includes tag matching.
- `flow list/search --input-type` is a narrowing tool, not a substitute for `flow get --params-template`.
- `flow get` is source of truth for task names and nested input keys.
- use `--params-template` whenever payload structure is uncertain.

### Job Submit

Module submit:

```bash
wemol-cli job submit --module-id <module_id> --method "<method_name>" --params '{"Input":"value"}'
wemol-cli job submit --module-name "<module_name>" --params-file params.json
wemol-cli job submit --module-id <module_id> --method "<method_name>" --params-file params.json --dry-run
cat params.json | wemol-cli job submit --module-id <module_id> --params @-
```

Flow submit:

```bash
wemol-cli job submit --flow-id <flow_id> --params-file flow-params.json
wemol-cli job submit --flow-name "<flow_name>" --params '{"Task A":{"Input":"value"}}'
wemol-cli job submit --flow-id <flow_id> --params-file flow-params.json --dry-run
cat flow-params.json | wemol-cli job submit --flow-id <flow_id> --params @-
```

Rules:
- Select exactly one target: `--module-id` / `--module-name` / `--flow-id` / `--flow-name`.
- Before executing any `job submit`, explicitly confirm with the user.
- `--method` is module-only; do not use with flow submit.
- If `--method` is omitted on module submit, CLI falls back to the first method. For multi-method modules, set `--method` explicitly after checking `module get`.
- `--params` and `--params-file` are mutually exclusive.
- `--dry-run` validates and previews input-layer params only; final submit may still add server-side fields.
- `job submit` supports repeatable `--tag`; CLI auto-adds `Wemol CLI` source tag.
- Module payload keys must match `--params-json` `field` values exactly.
- Flow payload must be task-keyed JSON (`{"Task Name": {"Input": value}}`).
- File args accept local file path; CLI uploads automatically.
- For file args, check `format` / `value_formats` from `--params-json` before submit.
- For `MultipleChoice`, send array even for one option (for example `{"Numbering Scheme":["imgt"]}`).

### Job Read / Track / Diagnose

```bash
wemol-cli job list --status Done --tag "Wemol CLI" --limit 20 --offset 0
wemol-cli job list --keyword antibody --limit 20
wemol-cli job search antibody numbering
wemol-cli job status <job_id>
wemol-cli job progress <job_id>
wemol-cli job get <job_id>
wemol-cli job wait <job_id>
wemol-cli job wait <job_id> --until terminal --interval 5
wemol-cli job tasks <job_id>
wemol-cli job diagnose <job_id>
wemol-cli job cancel <job_id>
```

Rules:
- use `job list` for status/history filters; `job search` for topic/module keyword recovery.
- use `job list --keyword` when you already know the result set should come from list/history rather than full-text recovery.
- `job wait <job_id>` defaults to `--until done`; use `--until terminal` to stop on `Done/Abort/Cancel`.
- `job wait` returns both a job summary and a `Tasks` list (stable ordered by sort_order/id), so task IDs are usually available immediately after waiting.
- `job progress` is preferred when `status` is too coarse.
- `job diagnose` gives focused suggestions and executable `next_commands`.

### Logs / Structured Output / Download

```bash
wemol-cli job logs <job_id>
wemol-cli job logs <job_id> --task-id <task_id> --stderr
wemol-cli job logs <job_id> --task-id <task_id1>,<task_id2> --stdout

wemol-cli job result <job_id>
wemol-cli job result <job_id> --task-id <task_id>
wemol-cli job result <job_id> --task-id <task_id> --name <output_key>
wemol-cli job result <job_id> --task-id <task_id1>,<task_id2> --dynamic

wemol-cli job download <job_id>
wemol-cli job download <job_id> --output ./result_dir
wemol-cli job download <job_id> --retry 3 --concurrency 8
wemol-cli job download <job_id> --no-resume
wemol-cli job download --all --output ./downloads
```

Rules:
- `job result` does not require `--task-id`; without it, CLI scans all tasks in the job and previews discovered keys.
- `job result` does not require `--task-id`; without it, CLI scans all tasks in the job and inspects discovered keys.
- `job result` reads structured key-value outputs; it is not the same as downloading produced files.
- `--name` means output key name. It is optional, but if used it requires `--task-id`.
- Text result keys get a short preview (first 5 lines, long lines truncated).
- Known binary result keys are summarized instead of previewed inline, even when `--name` is specified; use `job download` to retrieve the file.
- If output key missing or empty-data style errors (`DBDataNull`/similar) appear, try `job download` before declaring failure.
- warning-heavy `stdout.txt` / `stderr.txt` does not prove failure by itself; check job status and downloaded artifacts first.
- download resume is enabled by default with `.wemol-download-manifest.json`.
- `--no-resume` disables state reuse.
- summary includes skipped artifacts and transient/permanent failures.
- For downstream scripting, prefer explicit no-space output paths such as `--output ./downloads/job_<job_id>`.

## Hard Rules (Must Follow)

0. No ungated submit
- Never submit without passing the requirement-to-execution gate.
- Gate passed (clear candidate + valid params + coverage): auto-submit is allowed.
- Gate not passed or ambiguous: explicitly ask user before submit.
- In both cases, emit execution summary before `job submit`.

1. No guessed payload keys
- Never infer submit keys from natural language.
- Always derive from `module get --params-json` or `flow get`.
- When available, start from `submit_example` or `--params-template` instead of hand-assembling payload shape.

2. No flow flat JSON submit
- Flow submit must be task-keyed.
- If user gives flat JSON, fix structure first, then ask missing values.

3. No blind retry
- On validation error, re-read schema and payload shape first.
- On `JobModuleTaskMaxNumLimit`, treat as capacity/quota issue, not payload formatting.

4. No premature failure conclusion
- `job result` failure is not equal to job failure.
- Try `job result` without `--name`, then `job download`.

5. No auth-ignore probing
- After auth-required error, login/session handling comes first.

6. No update-question deflection
- For “需要更新吗 / latest?” do comparison work yourself, do not tell user to compare.

## Detection Triggers (Auto-Branch)

When these signals appear, branch automatically:
- `Authentication required` / `DBUserNoLogin` / `session null`:
  - branch to session handling (`login`, `account`, `host`, `lang`) before further task commands.
- user message contains `flow_id` / `--flow-id` / `flow submit` / `pipeline submit`:
  - branch to flow-shape validation (`flow get`, optional `--params-template`) before accepting payload.
- output errors contain `Output '<name>' was not found` or empty-data style (`DBDataNull`, `DBDataNotFound`):
  - branch to artifact recovery (`job download`) before failure conclusion.
- submit error contains `JobModuleTaskMaxNumLimit`:
  - branch to capacity/quota diagnosis, not payload rewrite.
- user asks `需要更新吗` / `latest` / `should I update`:
  - branch to version/update decision workflow.

## Version / Update Decision Workflow

When user asks whether update is needed:

1. Check installed version:

```bash
wemol-cli --version
```

2. Compare against known baseline:
- this skill baseline is `v1.1.0` (minimum)
- if operating in this repo, also read repo CLI version from `crates/cli/Cargo.toml`

3. Decision:
- installed `< v1.1.0` -> update required
- installed `< repo version` -> update required
- installed `== repo version` (or `>= v1.1.0` and no higher trusted source available) -> no mandatory update from local evidence

4. Upstream check:
- if channel access is available, check official release channel directly
- if blocked, report explicit blocker and what was checked

Allowed final statements:
- `update required`
- `no update required from current evidence`
- `cannot verify upstream latest due to <specific blocker>`

## Response Contract

When answering operational questions, include:
- what was checked (exact command(s))
- observed key evidence (version/status/error marker)
- decision/result category (for example update required / no update required / blocked)
- next concrete command when action is needed

Do not return generic uncertainty without evidence.

## When To Read References (Secondary)

Use references only when the main skill is not enough:
- `references/install.md`: installer, PATH/security, cross-platform details
- `references/requirement-to-execution.md`: expanded requirement decomposition and submit gating checklist

## Completion Checklist

Before concluding:
- Are command and payload shapes confirmed from live CLI output?
- If submit happened, were keys copied from schema (not guessed)?
- If output unclear, did you try `job tasks`/`job download` before declaring failure?
- If asked about update, did you run the full update decision workflow and return a direct conclusion?

## Self-Check Scorecard (Pass/Fail)

Run this quick scorecard before sending the final answer:

1. Evidence check (Pass/Fail)
- Did I run or quote concrete command evidence (`--version`, `status`, error marker, etc.)?

2. Decision check (Pass/Fail)
- Did I provide an explicit result category (for example update required / no update required / blocked with reason)?

3. Actionability check (Pass/Fail)
- If action is needed, did I provide the exact next command?

4. Deflection check (Pass/Fail)
- Did I avoid pushing core comparison/diagnosis work back to the user?

If any item is Fail, revise the response before sending.
