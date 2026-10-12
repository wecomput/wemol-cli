# WeMol CLI

Connect your agent to WeMol documentation, modules, workflows, jobs, and result files. The CLI runs independently and provides a terminal UI for accounts, approvals, activity, and job tracking. Rust and Node.js are not required to use it.

CLI **1.2.0** includes the **`wemol` Skill 0.3.0**. Download it from [GitHub Releases](https://github.com/wecomput/wemol-cli/releases/tag/v1.2.0). Legacy v1.0.0/v1.1.0 packages use different commands; see [Migration](#migration).

## Install

Choose the archive for your device:

| Device | Archive |
| --- | --- |
| Apple Silicon Mac | [wemol-cli-v1.2.0-macos-arm64.zip](https://github.com/wecomput/wemol-cli/releases/download/v1.2.0/wemol-cli-v1.2.0-macos-arm64.zip) |
| Intel Mac | [wemol-cli-v1.2.0-macos-x86_64.zip](https://github.com/wecomput/wemol-cli/releases/download/v1.2.0/wemol-cli-v1.2.0-macos-x86_64.zip) |
| x64 Windows | [wemol-cli-v1.2.0-windows-x86_64.zip](https://github.com/wecomput/wemol-cli/releases/download/v1.2.0/wemol-cli-v1.2.0-windows-x86_64.zip) |
| x64 Linux | [wemol-cli-v1.2.0-linux-x86_64-musl.tar.gz](https://github.com/wecomput/wemol-cli/releases/download/v1.2.0/wemol-cli-v1.2.0-linux-x86_64-musl.tar.gz) |
| All four platforms | [wemol-cli-v1.2.0-all-platforms.zip](https://github.com/wecomput/wemol-cli/releases/download/v1.2.0/wemol-cli-v1.2.0-all-platforms.zip) |

Verify the archive against its entry in the accompanying [SHA256SUMS](https://github.com/wecomput/wemol-cli/releases/download/v1.2.0/SHA256SUMS): use `shasum -a 256 <archive>` on macOS, `sha256sum <archive>` on Linux, or `Get-FileHash -Algorithm SHA256 <archive>` on Windows. Extract it; each package includes the executable, this README, and checksums for the extracted files.

Place the executable in a permanent user directory, preserve execution permissions, and add that directory to your user PATH if needed. Recommended locations:

- macOS/Linux: `~/.local/bin/wemol-cli`.
- Windows: `%LOCALAPPDATA%\Programs\WeMolCLI\wemol-cli.exe`.

Check any existing installation before replacing it. From the final location, run:

```sh
wemol-cli --version
wemol-cli
```

The second command prints setup guidance with the executable path and data directory. No `init` command is needed. If first launch is blocked by Gatekeeper or SmartScreen, review the package source and use the operating system's normal per-file approval flow; these packages are unsigned and not notarized.

## Agent setup

Give an agent with access to the device the executable's full path:

> Set up WeMol CLI using the executable at this full path: … . Follow its setup guidance to place the program, check PATH, and install and load the embedded Skill. Preserve my existing data directory. Verify status and channel access. If login is needed, give me the command to run in my terminal. Do not submit a computation during setup.

From the final executable path, install the Skill into your agent's Skill parent directory:

```sh
wemol-cli skill install --dir /absolute/path/to/agent/skills
```

Retain the `--data-dir` printed by setup guidance if specified. The installer creates `<Skill parent>/wemol/` and binds it to this device's executable and data directory. Load it using your agent's normal mechanism. To update an existing Skill, preserve personal edits before using `--replace`, which replaces the directory without a backup. No separate Skill download is needed.

Log in from your own terminal using the same executable and data directory:

```sh
wemol-cli login
wemol-cli status
wemol-cli channels
```

Login prompts for your platform, account, and hidden password, and chooses API Key or Cookie authentication according to platform support. Use `login --api-key` to bind an existing frontend user key, or `login --channel <channel-name>` to reauthenticate. Enter credentials in the terminal prompts rather than command arguments or chat.

`status` checks the local runtime; inspect each channel's `access.state` in `channels` to verify platform access. Use `tui` to manage accounts and approval policies. New standalone channels default to no approval; existing policies are preserved during upgrades.

## Common commands

Examples assume a channel named `cloud`:

```sh
wemol-cli search "antibody humanization" --channel cloud
wemol-cli jobs query --channel cloud --limit 5
wemol-cli tui
```

| Purpose | Command |
| --- | --- |
| Check channels and local status | `channels`, `status` |
| Search modules, workflows, and public documents | `search` |
| Read a document | `document` |
| List or inspect capabilities | `capabilities`, `capability` |
| Prepare, submit, query, or cancel a job | `jobs prepare/submit/query/cancel` |
| Download published job files | `deliver` |
| Manage accounts, approvals, and activity | `tui` |

Use the installed `wemol` Skill and the command's `--help` for parameters and returned capability/job references. Preparing inputs does not submit a computation. Successful submission, completed computation, and complete file retrieval are separate facts.

## Migration

### From legacy v1.0.0/v1.1.0

CLI 1.2.0 continues the redesigned standalone CLI introduced in 0.3.x, with a different command contract from legacy v1.0.0/v1.1.0. Legacy commands include `host`, `module`, `flow`, and singular `job`; its Skill is `wemol-cli-official`.

1. Keep the old executable and configuration until the new installation works. There is no documented automatic import of the old account format.
2. Install 1.2.0, use its full path to avoid PATH conflicts, install/load its embedded `wemol` Skill, and log in again.
3. Verify version, status, and channel access. Then retire the old executable and `wemol-cli-official` Skill, preserving personal edits. Check command resolution in a new terminal (`type -a wemol-cli` or `Get-Command wemol-cli -All`).

Old installers used `~/.wemol/bin` on macOS/Linux and `$HOME\.wemol\bin` on Windows. Do not use their hosted installation scripts to install 1.2.0. Regenerate job inputs with the new `jobs prepare` flow; legacy submission payloads are not interchangeable.

### From standalone 0.3.x

1. Keep the existing data directory. Finish local calls and handle pending approvals, then use uppercase `Q` in the existing `tui` to stop the runtime. Lowercase `q` only closes the interface; stopping the local runtime does not cancel platform jobs.
2. Keep a private backup if rollback is needed, then replace the executable with 1.2.0 at its permanent location.
3. From that location and the same `--data-dir`, reinstall the Skill with `skill install --dir <Skill parent> --replace`. Preserve Skill edits first, reload it, and verify version, status, and channel access.

Default data directories are `~/Library/Application Support/WeMolCLI` on macOS, usually `~/.local/share/WeMolCLI` on Linux, and `%LOCALAPPDATA%\WeMolCLI` on Windows. A custom `--data-dir` or `WEMOL_CLI_DATA_DIR` takes precedence. Moving the executable also requires updating PATH and reinstalling the Skill to refresh its binding.

Accounts, approval settings, and history are preserved in the selected redesigned data directory. Expired sessions require reauthentication. New API-key credential files cannot be read by older CLI versions, so reverting the executable alone is not a data rollback.
