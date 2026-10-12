# WeMol CLI

Official distribution entry for WeMol CLI: connect your agent to WeMol documentation, modules, workflows, jobs, and result files.

The redesigned CLI runs independently, starts its local runtime when needed, and includes a terminal UI for accounts, approvals, activity, and job tracking. Users do not need Bridge, Rust, or Node.js. WeMol Agent Bridge uses the same CLI and business implementation with a graphical management interface.

**0.3.9 has been built and packaged locally; it has not been published to GitHub.** Check [GitHub Releases](https://github.com/wecomput/wemol-cli/releases) for available downloads. Legacy v1.1.0/v1.0.0 assets cannot run the redesigned commands below. Use an explicitly identified local 0.3.9 package while publication is pending.

## Versions

| Component | Version |
| --- | --- |
| WeMol CLI | 0.3.9 |
| Embedded business Skill (`wemol`) | 0.3.0 |
| Companion Bridge | 0.3.2 |

These components have separate version numbers. The CLI can run without Bridge. Legacy v1.0.0/v1.1.0 and redesigned 0.3.x are different product generations with different command/configuration contracts. The lower new version number does not mean an older legacy release. Read [Migration](#migration) before replacing an existing installation.

## What's new in 0.3.9

This is the first planned GitHub distribution of the redesigned CLI:

- One executable supports independent CLI/TUI use and Bridge integration through shared business logic. Standalone commands start the local runtime when needed.
- The TUI provides account management, approvals, activity, and job tracking. Discovery uses `search`, `document`, `capabilities`, and `capability`; jobs use `jobs prepare/submit/query/cancel`; published files are retrieved with `deliver`.
- The business Skill is embedded in the executable. `skill install` installs `wemol` with this device's executable path and standalone data directory. There is no separate repository setup Skill or business Skill archive to install.
- Password login detects backend capability: enabled frontend API keys use a verified device key; explicitly identified older backends or disabled frontend-key support use a verified Cookie session.
- Saved valid Cookies are replaced only after a new API key has been verified. Account bindings, approval settings, and operation history are preserved. Older/disabled backends keep Cookies and can be checked again on a later runtime startup.
- Detection, network, permission, malformed-response, and key-operation failures preserve existing local credentials. HTTP errors are not treated as proof of legacy support, and key failures on a backend declaring support do not silently downgrade authentication.
- Device keys use a stable `CLI_XXXXXXXX` name. Repeated login reuses the device record; rebinding after clearing local credentials can reset its platform key. `login --api-key` binds an existing frontend user key; backend system-user keys are not supported.

## Download and install

Download the archive for your device and the release's `SHA256SUMS`:

| Device | Planned 0.3.9 asset |
| --- | --- |
| Apple Silicon Mac | `wemol-cli-v0.3.9-macos-arm64.zip` |
| Intel Mac | `wemol-cli-v0.3.9-macos-x86_64.zip` |
| x64 Windows | `wemol-cli-v0.3.9-windows-x86_64.zip` |
| x64 Linux (static musl binary) | `wemol-cli-v0.3.9-linux-x86_64-musl.tar.gz` |
| All four platforms | `wemol-cli-v0.3.9-all-platforms.zip` |

There are no Linux ARM64 or Windows ARM64 assets in this release. `release-manifest.json` records source commits, the build toolchain, lockfile digest, binary digests, and archive digests; release-level `SHA256SUMS` covers the five archives and manifest.

Calculate the selected archive's SHA256 using `shasum -a 256 <archive>` on macOS, `sha256sum <archive>` on Linux, or `Get-FileHash -Algorithm SHA256 <archive>` on Windows. Compare it to the exact filename's entry in the downloaded `SHA256SUMS`. A checksum establishes file integrity relative to that checksum file; it is not a release signature.

Extract the verified archive. Each platform package contains one executable, this README, and an inner `SHA256SUMS`; the all-platform ZIP contains four platform directories. From the extracted package directory, verify the inner file with `shasum -a 256 -c SHA256SUMS` (macOS) or `sha256sum -c SHA256SUMS` (Linux). On Windows compare each `Get-FileHash` result against its entry.

Place the executable in a permanent user directory:

- macOS/Linux: `~/.local/bin/wemol-cli`.
- Windows: `%LOCALAPPDATA%\Programs\WeMolCLI\wemol-cli.exe`.

Check ownership before replacing an existing file. Preserve execution permissions; if necessary, use `chmod +x` only on the selected macOS/Linux executable. Add the chosen directory to the relevant user PATH if needed, then verify command resolution in a new terminal (`type -a wemol-cli` or `Get-Command wemol-cli -All`). Do not overwrite a Bridge-managed executable with the standalone archive.

Run the final executable with `--version`, `--help`, and no arguments:

```sh
wemol-cli --version
wemol-cli --help
wemol-cli
```

It should report 0.3.9. The no-argument command prints setup guidance with its executable path and selected data directory; it does not log in or enter the TUI. No `init` command is needed. The old hosted `install.sh`/`install.ps1` channel has not been updated or verified for this release and should not be used to install 0.3.9.

## Connect your agent and log in

Tell an agent with access to the device:

> Set up WeMol CLI using the executable at this full path: … . Run it to read its setup guidance, place it in a permanent user directory, check PATH, and install and load its embedded Skill. Preserve any existing data directory. Verify local status and channel access. If login is required, give me the command to run in my terminal. Do not submit a computation as part of setup.

From the final absolute executable path, retaining the `--data-dir` printed by its guidance, run:

```text
skill install --dir <absolute path to your agent's Skill parent directory>
```

This creates `<Skill parent>/wemol/` with the business Skill, references, task guides, device-specific invocation, and Skill version record. Load it using your agent's normal mechanism. An update may require `--replace`, which replaces the existing Skill directory without a backup; preserve personal edits first. Do not use the old `wemol-cli-official` business Skill or copy an installed Skill binding between computers.

The user logs in from their own terminal, using the same executable and data directory:

```sh
wemol-cli login
wemol-cli status
wemol-cli channels
```

`login` prompts for the platform, account, and hidden password; `login --api-key` prompts for an existing frontend user key. Passwords and keys are not accepted as command-line arguments and should not be sent to the agent. The CLI verifies the account before saving credentials. Use `login --channel <channel-name>` to reauthenticate a known channel.

`status` checks the local runtime, not platform authentication. Inspect each channel's `access.state` in `channels`; an outer `ok: true` alone does not establish access. Network unavailability is not proof that login credentials are wrong.

Run `tui` for human management and approval settings. New standalone channels default to no approval; existing policies are preserved during upgrade. Setup verification does not authorize job submission or cancellation.

## Business commands

Examples assume you configured a channel named `cloud`:

```sh
wemol-cli search "antibody humanization" --channel cloud
wemol-cli jobs query --channel cloud --limit 5
wemol-cli jobs --help
wemol-cli deliver --help
```

| Purpose | Entry point |
| --- | --- |
| Discover modules, workflows, and public documents | `search` |
| Read a document | `document` |
| List capabilities or inspect a returned capability | `capabilities`, `capability` |
| Prepare inputs, submit, query, or cancel a job | `jobs prepare/submit/query/cancel` |
| Retrieve published job artifacts | `deliver` |
| Check channels and local runtime | `channels`, `status` |

Use returned capability references and job IDs with the corresponding command's `--help` and installed `wemol` Skill. Preparing inputs does not submit a computation. Successful submission, completed computation, and complete artifact retrieval are separate facts.

## Migration

Identify the product from trusted executable paths, `--version`/`--help`, and installed Skill metadata. Version ordering alone cannot identify it. Check PATH, shell aliases/functions, and the agent's recorded absolute executable path; these may resolve different installations.

### Legacy v1.0.0/v1.1.0

Legacy commands include `host`, `module`, `flow`, and singular `job`; its Skill is `wemol-cli-official`. The old official installers used `~/.wemol/bin/wemol-cli` on macOS/Linux and `$HOME\.wemol\bin\wemol-cli.exe` on Windows. Manual installations may be elsewhere.

1. Keep the old executable/configuration until the new installation is verified. No automatic import of the legacy official CLI's account format is documented; do not copy its credential files into the new data directory.
2. Install verified 0.3.9 in a permanent standalone location. Use its full path during migration, read its setup guidance, and install/load the embedded `wemol` Skill.
3. Log in again for each needed account. Verify version, local status, and each channel's access. Migrating alone does not authorize computation or cancellation.
4. After verification, retire `wemol-cli-official` from active Skill discovery, preserving personal edits outside active Skill directories. Remove only the old executable whose ownership is established, fix stale PATH/aliases/bindings, and verify the new command in a fresh terminal.

These are purpose mappings, not interchangeable syntax:

| Legacy purpose | Redesigned entry point |
| --- | --- |
| Host/account/session inspection | `channels`; account settings in `tui` |
| Public document search/read | `search --source docs`, `document` |
| Module/flow discovery and schema inspection | `search`, `capabilities`, `capability` |
| Input template and submission | `jobs prepare`, then `jobs submit --file ...` |
| Job status, progress, failures, and results | `jobs query --show summary/progress/failure/results` |
| Artifact download | `deliver` |

Regenerate input files through the new preparation flow; do not reuse a legacy submission payload as though its schema were unchanged. Do not run the old hosted installer as the 0.3.9 update path.

### Redesigned standalone 0.3.x

1. Record the existing executable, data directory, and Skill directory. Keep the same data directory throughout the upgrade.
2. Finish local calls and handle pending approvals. In the existing standalone `tui`, use uppercase `Q` and confirm to stop the runtime. Lowercase `q` only closes the interface. Do not force-stop a busy runtime; stopping the local runtime does not cancel jobs already running on the platform.
3. With the runtime stopped, keep a private backup of the original data and executable if rollback is needed. Replace the executable with verified 0.3.9 at the intended permanent path.
4. From that path and the same `--data-dir`, run `skill install --dir <Skill parent> --replace`. Preserve Skill edits first, reload it in the agent, and verify version, `status`, and `channels`.

If moving the executable, update PATH and use the installer to refresh the Skill binding. It can restart an idle standalone runtime from the new path; active calls or pending approvals prevent restart. Account identities, approval settings, and history are preserved in the selected redesigned data directory. This does not imply import from the old v1.1.0 product or Bridge.

Expired Cookies require reauthentication through the reported relogin command. API-key credential files cannot be read by older CLI versions: replacing the executable with an older one is not a data rollback. A local backup does not restore platform keys that were reset/revoked. Removing local credentials does not revoke a platform key.

### Bridge-managed CLI

Use Bridge's update and Agent setup interfaces to keep its executable, runtime, and registered Skills compatible. Do not replace its managed CLI with this standalone archive or redirect its binding/data directory. Standalone and Bridge data are not silently merged.

### Data directories

Standalone defaults are `~/Library/Application Support/WeMolCLI` on macOS, usually `~/.local/share/WeMolCLI` on Linux, and `%LOCALAPPDATA%\WeMolCLI` on Windows. Global `--data-dir` or `WEMOL_CLI_DATA_DIR` can select another private directory. Credentials are local private files and are not encrypted; never include them or their backups in a release package.

## Source and validation

All four executables were rebuilt with `--release --locked` from these fixed local source commits, with clean working trees before and after the build:

| Source project | Commit |
| --- | --- |
| `wemol-agent-bridge` | `00c7d8fce3231201b7388b74a5d0f35dc9255136` |
| `rust-wemol-sdk` | `f5328140ce1e55d83874014bac2cfc9dc555e6a9` |

The CLI was freshly compiled for every target; shared dependency caches were reused. `release-manifest.json` records the commit/tree identities, lockfile digest, build timestamps, toolchain, and binary/archive digests. These source commits have not been pushed yet.

Affected Rust tests passed: **162 passed, 11 ignored, 0 failed**. Ignored cases require external backends or live integration conditions. The release metadata test passed, as did formatting and whitespace checks. Mock-backend and terminal/process tests cover API-key/Cookie selection, credential preservation, login, restart, and migration. A nonblocking accepted socket in a CLI test fixture was corrected during validation; this is a test-only change.

All four executable formats, archive integrity, extracted byte digests, document consistency, and checksums were verified. Apple Silicon `--version`, `--help`, and no-argument setup guidance were exercised. Execution on Intel macOS, Windows, and Linux and real-platform login/TUI/business acceptance remain unverified.

Packages are not release-signed or notarized. Gatekeeper/SmartScreen may require normal per-file user approval; do not disable system protections globally. Complete target-system acceptance before final publication, update pending-publication/validation text, and regenerate any changed archives and checksums. Keep historical v1.0.0/v1.1.0 Releases and explicitly designate 0.3.9 as Latest when it is published.

This repository maintains one public document: this README. The business Skill and workflow references are owned by the executable. Release notes are generated from this README; local build records, the manifest, and attachments are outside Git under `release/`. GitHub-generated “Source code” archives for this distribution repository contain its distribution files, not the redesigned CLI's buildable source. Source development remains in `wemol-agent-bridge` and its companion `rust-wemol-sdk`.
