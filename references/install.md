# Install

Use the installer scripts when `wemol-cli` is not already available, or when installed version is below the required skill baseline.

Start with:

```bash
wemol-cli --version
```

Rules:
- skill baseline: `v1.1.0`
- if the command is missing, install
- if installed version is below baseline, upgrade
- if installed version meets baseline, do not reinstall unless the user explicitly asks
- after installation, run `wemol-cli --help` to verify the binary is callable

## macOS and Linux

Install with the Unix installer:

```bash
curl -LsSf https://wemol.wecomput.com/static/wemol-cli/latest/install.sh | sh
```

The default release base URL is:

```text
https://wemol.wecomput.com/static/wemol-cli
```

To override it temporarily:

```bash
curl -LsSf https://wemol.wecomput.com/static/wemol-cli/latest/install.sh | \
  WEMOL_INSTALL_BASE_URL="https://your-release-base" sh
```

To install a specific version:

```bash
curl -LsSf https://wemol.wecomput.com/static/wemol-cli/latest/install.sh | \
  sh -s -- --version v1.1.0
```

Agent notes:
- Use the `latest/install.sh` URL above, not a versioned installer path.
- If a specific version is given without the `v` prefix, the installer adds it automatically.
- The installer checks whether `~/.wemol/bin` is already on `PATH` and prints the shell snippet to add it when needed.

The installer places the binary in:

```bash
~/.wemol/bin/wemol-cli
```

If `wemol-cli` is not on `PATH`, the installer prints the shell snippet needed to add it.

## macOS Security Notice

Current macOS builds are not yet distributed with a valid developer signature/notarization chain.

On a new Mac, the first launch may be blocked by Gatekeeper. If that happens, ask the user to manually allow the binary:

1. Try running `wemol-cli --help` once.
2. Open `System Settings -> Privacy & Security`.
3. Find the blocked `wemol-cli` item and choose `Allow Anyway`.
4. Run the command again and confirm the security prompt.

This is currently expected for macOS release builds until official signing is added.

## Windows

Install with the PowerShell installer:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://wemol.wecomput.com/static/wemol-cli/latest/install.ps1 | iex"
```

To override the release base URL:

```powershell
$env:WEMOL_INSTALL_BASE_URL = "https://your-release-base"
powershell -ExecutionPolicy ByPass -c "irm https://wemol.wecomput.com/static/wemol-cli/latest/install.ps1 | iex"
```

To install a specific version:

```powershell
powershell -ExecutionPolicy ByPass -c "& ([scriptblock]::Create((irm https://wemol.wecomput.com/static/wemol-cli/latest/install.ps1))) -Version v1.1.0"
```

Agent notes:
- Use the `latest/install.ps1` URL above, not a versioned installer path.
- If a specific version is given without the `v` prefix, the installer adds it automatically.
- The installer checks whether `$HOME\.wemol\bin` is already in the user `Path` and prints a reminder when it is missing.

The installer places the binary in:

```powershell
$HOME\.wemol\bin\wemol-cli.exe
```

## Windows Security Notice

Current Windows builds are not yet distributed with a code-signing certificate.

Because of that, Windows Defender SmartScreen or browser download protection may warn about `wemol-cli.exe` on first download or launch. If that happens, ask the user to review the file source and then use the standard Windows override flow, such as:

1. Keep the downloaded file if the browser marks it as uncommon.
2. Run `wemol-cli.exe --help` once.
3. If SmartScreen blocks it, choose `More info` and then `Run anyway`.

This is currently expected for Windows release builds until official code signing is added.

## Verification

After installation, verify:

```bash
wemol-cli --help
```

If the command is still missing, check the install directory and `PATH`.
