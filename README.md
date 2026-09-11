# Eagle Fortify Tool 3.1

![EEPX Labs][https://github.com/EEPX-Labs/Projects-Backgrounds/blob/main/EFT_GH_Background.jpg]

**Eagle Fortify Tool** is a local-first password security utility by **EEPX Lab**.

One shared security core powers two interfaces:

- **Desktop GUI** — a modern PySide6 dashboard with a dark security-focused design.
- **Command-line interface (CLI)** — scriptable `eftool` commands with the same crypto core.

No account. No cloud. Your data never leaves your machine — the only network
call in the whole program is the **opt-in** HIBP leak check, which uses
[k-anonymity](https://en.wikipedia.org/wiki/K-anonymity) so the password itself
is never sent anywhere.

---

## Features

- Cryptographically secure password generation (uses Python's `secrets`, never `random`)
- Password analysis: entropy, score, strength label, and estimated crack time
- XKCD-style **passphrases** from a built-in dictionary of **7,776 words** (EFF large wordlist)
- Optional HIBP breach checking via k-anonymity (opt-in, only network feature)
- AES-256-GCM encrypted local vault with a single master password
  - PBKDF2-HMAC-SHA256 key derivation (600,000 iterations)
  - Search, backup, auto-lock after inactivity, and lock-on-close
- Secure clipboard with auto-clear (does not overwrite newer clipboard content)
- Random email-identity suggestions (nothing is registered anywhere)
- Desktop GUI with an embedded `.ico` icon shown in the **taskbar, Explorer, and Task Manager**
- CLI built on the exact same core services as the GUI

## Requirements

- **Python 3.10+** (developed and packaged on 3.14)
- Windows 10/11 for the prebuilt `.exe` packages (macOS/Linux run from source)

The only runtime dependencies are:

```
PySide6>=6.7,<7
cryptography>=43,<47
pyperclip>=1.9,<2
```

---

## Installation (from source)

```bash
# 1. Create and activate a virtual environment (recommended)
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # macOS / Linux

# 2. Install the package and its dependencies
python -m pip install -r requirements.txt
python -m pip install -e .
```

This installs two console entry points:

| Command                  | Interface |
| ------------------------ | --------- |
| `eagle-fortify-gui`      | Desktop GUI |
| `eftool`                 | Command-line utilities |

---

## Running the GUI

Any of these work:

```bash
eagle-fortify-gui            # installed console entry point

python main.py
python run_gui.py
python -m eagle_fortify.main
```

On Windows you can also double-click **`run.bat`** — it verifies Python is
present, installs dependencies on first run, then starts the app.

> **Note:** The packaged executable is `EagleFortifyTool.exe` (see
> [Building a Windows release](#building-a-windows-release)). It opens without
> a console window and shows the EFT icon in the taskbar and Task Manager.

## Using the CLI

```bash
eftool --version                        # print version
eftool generate --length 24 --count 3   # 3 strong passwords
eftool analyze "Example-Password-Only-For-Demo"
eftool passphrase --words 5 --capitalize --number
eftool email --prefix alice --domain proton.me
eftool leak-check "password-to-check"   # opt-in network call
eftool vault create
eftool vault list
```

`generate`, `analyze`, `passphrase`, `email`, and `vault` are fully offline.
`leak-check` is the only feature that touches the network, and you always
trigger it explicitly.

### `eftool generate`

| Option | Default | Meaning |
| --- | --- | --- |
| `-l, --length` | `20` | Password length (4–128) |
| `-n, --count` | `1` | How many passwords (1–10) |
| `--no-upper` | off | Exclude uppercase letters |
| `--no-lower` | off | Exclude lowercase letters |
| `--no-numbers` | off | Exclude digits |
| `--no-symbols` | off | Exclude symbols |
| `--exclude-ambiguous` | off | Remove `0 O 1 l I \|` |

Each generated password is guaranteed to contain at least one character from
every enabled character class.

<!-- README_PART2 -->

### `eftool vault`

The vault stores credentials in a single AES-256-GCM encrypted file:

- **Default location:** `~/.eagle_fortify/main.efv` on every platform
- **Settings file:** `~/.eagle_fortify/settings.json` (non-sensitive preferences)

```bash
eftool vault create --path C:\path\to\my.efv   # specify a custom location
eftool vault list
```

Backups are encrypted `.efv` files too. The GUI can auto-lock the vault after
inactivity and clear copied secrets from the clipboard after a configurable
timeout (default 60 seconds).

---

## Tests

```bash
python -m pytest -q
```

The suite covers password generation, the analyzer, passphrase/wordlist
integrity, vault encryption (including rejecting a wrong master password),
clipboard auto-clear, and settings round-trips.

---

## Building a Windows release

Two artifacts are produced:

1. **`dist\EagleFortifyTool\EagleFortifyTool.exe`** — GUI, *onedir* build
   (portable folder, fastest startup, no console window).
2. **`dist\eftool.exe`** — CLI, *onefile* build (self-contained console tool).

Both executables embed `assets\EFT_icon.ico` **and** a full Windows version
resource, so Explorer, the taskbar, and Task Manager all show:

- the Eagle Fortify Tool icon, and
- the app/process as **Eagle Fortify Tool** (`EagleFortifyTool` process name).

### One command

```powershell
powershell -ExecutionPolicy Bypass -File .\packaging\build_windows.ps1
```

The script:

1. Cleans `build\` and `dist\`.
2. Installs PyInstaller if missing.
3. Runs a source import + core smoke test.
4. Builds both executables from `packaging\EagleFortify.spec`.
5. Smoke-tests the frozen CLI.
6. Zips a **portable** release: `dist\EagleFortifyTool-3.1.0-portable.zip`.
7. If **Inno Setup 6** (`ISCC.exe`) is installed, also builds the installer
   `dist\installer\EagleFortifyTool-3.1.0-Setup.exe`.

Add `-SkipInstaller` to skip the installer step:

```powershell
powershell -ExecutionPolicy Bypass -File .\packaging\build_windows.ps1 -SkipInstaller
```

### Manual PyInstaller build

```bash
python -c "import PyInstaller" || python -m pip install pyinstaller
python -m PyInstaller packaging\EagleFortify.spec --noconfirm --clean
```

### Installer (Inno Setup)

The `.iss` script at `packaging\installer\EagleFortify.iss` produces a modern
Wizard-style installer that:

- installs the GUI + CLI under `%ProgramFiles%\Eagle Fortify Tool`,
- creates Start Menu shortcuts (and an optional desktop shortcut),
- uninstalls **without touching** the user vault in `~\.eagle_fortify`.

Download Inno Setup 6 from <https://jrsoftware.org/isdl.php> and build with:

```powershell
& "C:\Program Files (x86)\Inno Setup 6\ISCC.exe" packaging\installer\EagleFortify.iss
```

---

## Packaging details

| File | Purpose |
| --- | --- |
| `packaging\EagleFortify.spec` | PyInstaller spec (GUI onedir + CLI onefile) |
| `packaging\cli_launcher.py` | CLI entry point used by the frozen `eftool.exe` |
| `packaging\version_info.py` | Builds the Windows version resource (name/icon in Task Manager) |
| `packaging\build_windows.ps1` | One-shot release build (PyInstaller + zip + Inno Setup) |
| `packaging\installer\EagleFortify.iss` | Inno Setup installer definition |

The frozen executables look for their wordlist and icon inside the bundle, so
the packaged apps run from any folder and the CLI works with no Qt installed.

---

## Project layout

```text
eagle_fortify/
├── cli.py                  # console interface (eftool)
├── clipboard.py            # clipboard auto-clear manager
├── constants.py            # shared constants + wordlist path resolution
├── settings.py             # local non-sensitive preferences
├── vault.py                # AES-256-GCM encrypted vault backend
├── data/words.txt          # 7,776-word passphrase dictionary
├── core/                   # UI-independent security services
│   ├── analyzer.py         # strength / entropy logic
│   ├── crypto.py           # AES-GCM primitives used by the vault
│   ├── crypto_vault.py     # PBKDF2 + layered AES-GCM vault
│   ├── generators.py       # password / passphrase / email generators
│   ├── models.py           # VaultMetadata & shared models
│   ├── services.py         # high-level service API
│   └── ...
└── ui/
    ├── app.py              # PySide6 desktop interface
    └── app_legacy.py       # v2 legacy UI (kept for reference)

assets/                     # logos, icon, SVG/Png UI assets
data/words.txt              # legacy wordlist mirror (keep in sync)
packaging/                  # spec, scripts, and installer sources
tests/                      # pytest suite
main.py · run_gui.py · run_cli.py · run.bat · EF_Tool.py   # launchers
```

---

## Security notes

- Generation and shuffling use `secrets` (CSPRNG); no `random`-based fallback exists.
- Strength/crack-time values are **estimates** based on character-set entropy
  and an offline-guessing assumption — not guarantees.
- The vault derives an AES-256 key with PBKDF2-HMAC-SHA256 (600k iterations)
  and authenticates both the header and the payload with AES-GCM.
- Secrets are never written to logs. Vault backups remain encrypted `.efv` files.
- Passphrase dictionary: **7,776 unique words** (EFF large wordlist). A 4-word
  passphrase therefore has ~51 bits of entropy before capitalization/numbers.

## License

MIT — see [LICENSE](LICENSE).
