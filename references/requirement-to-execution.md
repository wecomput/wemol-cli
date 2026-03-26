# Requirement to Execution (Flow First, Condition-Gated)

Use this policy when user input is a requirement (not a known module/flow ID) and the agent needs to decide whether to submit directly.

## Step 1: Decompose Requirement

Extract:
- `goal` (what outcome user wants)
- `inputs` (provided data/files/identifiers)
- `constraints` (method, model, tags, runtime, compliance)
- `keywords` (domain terms for search)

If required inputs are missing, stop and collect them before submit.

## Step 2: Search Candidates in Parallel

```bash
wemol-cli flow search <keywords...>
wemol-cli module search <keywords...>
```

Prefer flows first because they represent complete multi-step solutions.

## Step 3: Gate Candidate Before Submit

Flow path:

```bash
wemol-cli flow get <flow_id> --params-template
```

Module fallback:

```bash
wemol-cli module get <module_id> --params-json --method <method_name>
```

Auto-submit is allowed only when all are true:
1. candidate is clear
2. required params are fillable from requirement/defaults
3. target coverage matches the user's goal

## Step 4: Ask-User Gate

Ask user before submit when:
- no clear candidate,
- params cannot be constructed,
- candidate coverage is partial or ambiguous.

Return up to 3 candidates with short differences and blocking conditions.

## Step 5: Submit and Recover

Before any auto-submit, emit execution summary:
- goal
- selected target (flow/module + id/name)
- key params
- risk tags

Failure path:

```bash
wemol-cli job diagnose <job_id>
wemol-cli job logs <job_id> --task-id <task_id> --stderr
wemol-cli job result <job_id> --task-id <task_id>
```

If failure is parameter-related, return to parameter collection and retry.
