# Eagle Fortify Tool

<div align="center">



### Local Security · Encrypted Vault · Privacy First

**A local-first password utility built to keep your credentials under your control.**

[![Python](https://img.shields.io/badge/Python-3.14-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![PySide6](https://img.shields.io/badge/PySide6-Desktop%20UI-41CD52?style=for-the-badge&logo=qt&logoColor=white)](https://doc.qt.io/qtforpython-6/)
[![Security](https://img.shields.io/badge/AES--256--GCM-Encrypted-6C5CE7?style=for-the-badge&logo=letsencrypt&logoColor=white)](https://csrc.nist.gov/)
[![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)](https://www.microsoft.com/windows)
[![CLI](https://img.shields.io/badge/Interface-CLI%20%2B%20GUI-24292F?style=for-the-badge&logo=windows-terminal&logoColor=white)](#using-the-command-line-tool)

[Features](#features) · [Installation](#install) · [Screenshots](#feature-tour) · [Privacy & Security](#privacy--zero-knowledge-encryption) · [User Guide](docs/USER_GUIDE.md)

</div>

---

## Overview

**Eagle Fortify Tool** is a local password utility designed to help you create, analyze, and manage credentials without relying on a cloud account.

It can generate strong passwords, evaluate password strength, create secure passphrases and email identities, check passwords against known data breaches, and store credentials in a private encrypted vault.

Most features run locally on your device. Your vault and generated credentials are not uploaded to a remote server.

The optional breach-check feature uses a network connection and follows the Have I Been Pwned k-anonymity approach.

## Features

- **Password Generator** — Generate strong passwords with configurable length and quantity.
- **Password Analyzer** — Evaluate password strength and identify potential weaknesses.
- **Passphrase Generator** — Create memorable passphrases using multiple words.
- **Email Identity Generator** — Generate random email identities.
- **Encrypted Vault** — Store credentials in an encrypted local vault.
- **Breach Checker** — Check whether a password appears in known data breaches using a privacy-preserving hash-prefix lookup.
- **Desktop Application** — Access the tools through a graphical interface.
- **Command-Line Interface** — Run supported operations directly from Command Prompt.
- **Offline-First Design** — Core password utilities work without an internet connection.
- **Encrypted Backups** — Keep encrypted copies of your vault for safekeeping.

## Feature Tour

Explore the application's main features below.

For the complete walkthrough, including screenshots and explanations, see the **[Visual User Guide](docs/USER_GUIDE.md)**.

| Dashboard | Password Generator | Encrypted Vault |
|:---:|:---:|:---:|
| ![Dashboard](docs/screenshots/01-dashboard.png) | ![Password Generator](docs/screenshots/02-generator-passwords.png) | ![Unlocked Vault](docs/screenshots/07-vault-unlocked.png) |

| Password Analyzer | Leak Checker | Email Generator |
|:---:|:---:|:---:|
| ![Password Analyzer](docs/screenshots/04-analyzer.png) | ![Leak Checker](docs/screenshots/05-leak-checker.png) | ![Email Generator](docs/screenshots/09-email-generator.png) |

---

## Install

### Option 1 — Windows Installer (Recommended)

1. Download `EagleFortifyTool-3.2.0-Setup.exe` from the project's available release files.
2. Run the installer.
3. Follow the installation instructions.
4. Keep **Add eftool to PATH** enabled if you want to use the command-line tool from any Command Prompt.
5. Open a **new** Command Prompt window after installation.

Verify the installation:

```bat
eftool --version
```

Expected output:

```text
Eagle Fortify Tool 3.2.0
```

You can launch the desktop application from the Start Menu or desktop shortcut.

### Option 2 — Portable Version

1. Download `EagleFortifyTool-3.2.0-portable.zip`.
2. Extract the archive to a folder, such as `C:\EagleFortify`.
3. Run `EagleFortifyTool.exe` to open the desktop application.

To use `eftool` from any Command Prompt, add the extracted folder to your Windows `PATH`:

1. Press **Win + R**.
2. Type `sysdm.cpl` and press Enter.
3. Open **Advanced → Environment Variables**.
4. Under **User variables**, select `Path` and click **Edit**.
5. Click **New** and enter the extracted folder path.
6. Confirm the changes and open a new Command Prompt.

Then run:

```bat
eftool --version
```

### Troubleshooting: `eftool` Is Not Recognized

If Windows reports that `eftool` is not recognized:

1. Close the current Command Prompt and open a new one.
2. Confirm that the application is installed:

   ```bat
   dir "C:\Program Files\Eagle Fortify Tool\eftool.exe"
   ```

3. Check whether the installation directory is included in your `PATH`:

   ```bat
   echo %PATH%
   ```

4. If the directory is missing, rerun the installer and enable **Add eftool to PATH**.
5. If an older development executable is taking precedence, inspect your `PATH` for outdated `eftool.exe` files and remove obsolete entries where appropriate.

The installer is intended to configure `PATH` automatically. Existing terminal windows may need to be reopened before the updated environment takes effect.

---

## Using the Command-Line Tool

The command-line interface is available through `eftool`.

### Version

```bat
eftool --version
```

### Generate Passwords

```bat
eftool generate
```

Generate three passwords, each 24 characters long:

```bat
eftool generate --length 24 --count 3
```

### Analyze Password Strength

```bat
eftool analyze "your-password-here"
```

**Security note:** Avoid entering a real, currently used password directly into a command if your shell history or other local tools may retain commands. Prefer analyzing test credentials or use the desktop interface when appropriate.

### Generate Passphrases

```bat
eftool passphrase
```

Generate a passphrase containing five words:

```bat
eftool passphrase --words 5
```

### Generate Email Identities

```bat
eftool email
```

### Manage the Encrypted Vault

Create a vault:

```bat
eftool vault create
```

List stored vault entries:

```bat
eftool vault list
```

### Open the Desktop Application

Launch the graphical interface from Command Prompt:

```bat
eagle-fortify-gui
```

Alternatively, open `EagleFortifyTool.exe` in the portable installation or use the installed Start Menu or desktop shortcut.

### Command-Line Notes

- Core commands work offline.
- The optional `eftool leak-check` command requires an internet connection.
- The breach checker uses a hash-prefix lookup rather than sending the original password to the breach-check service.
- Available commands and options may vary by release. Run `eftool --help` or consult the user guide for the supported syntax.

---

## Privacy & Zero-Knowledge Encryption

Eagle Fortify Tool is designed around local control and encrypted credential storage.

### Local-First Architecture

The vault, password generator, passphrase generator, email identity generator, and password analyzer operate locally.

The application does not require cloud synchronization or a remote account to use its core features.

### Encrypted Vault

Vault entries are encrypted using **AES-256-GCM**. The encryption key is derived from the Master Password using **PBKDF2-HMAC-SHA256**, with 600,000 iterations and a random 32-byte salt, according to the application's documented configuration.

Encryption protects stored data, but it does not eliminate every security risk. Keep your device secure, use a strong Master Password, and maintain encrypted backups.

### Optional Breach Checking

The optional breach-check operation requires network access. It uses the Have I Been Pwned k-anonymity model, which sends only the first five characters of a SHA-1 password hash to the service for matching.

The original password is not sent as part of that lookup. SHA-1 is used here for compatibility with the breach-check protocol, not as the vault's encryption algorithm.

Only check passwords you no longer use whenever possible.

### Master Password & Recovery

Your Master Password is not stored as a recoverable password by the application. If it is lost, the encrypted vault may become permanently inaccessible.

**Keep your Master Password safe.** The developer cannot recover vault contents without the required secret.

### Backups & Local Storage

Default data locations:

| Data | Location |
|---|---|
| Encrypted vault | `%USERPROFILE%\.eagle_fortify\main.efv` |
| Application settings | `%USERPROFILE%\.eagle_fortify\settings.json` |

Vault backups are encrypted files as well. Store them somewhere secure and make sure you can access your Master Password when you need to restore them.

Uninstalling the application does not automatically delete your vault.

---

## Uninstall

### Installer Version

1. Open **Windows Settings → Apps**.
2. Find **Eagle Fortify Tool**.
3. Select **Uninstall**.

If the installer configured the command-line tool in `PATH`, it is intended to remove that entry during uninstallation.

### Portable Version

Delete the folder where you extracted the portable archive.

If you manually added that folder to `PATH`, remove the corresponding environment-variable entry as well.

**Important:** Uninstalling or deleting the application folder does not necessarily remove your vault or settings. Review the data locations above if you intend to remove local application data.

---

## Documentation

- **[Visual User Guide](docs/USER_GUIDE.md)** — Screenshots and explanations of the application's features.
- **[Changelog](CHANGELOG.md)** — Release history and changes.
- **[License](LICENSE)** — Terms and conditions governing use.

---

## Technology

Eagle Fortify Tool combines a desktop interface with a command-line workflow and local encrypted storage.

| Component | Technology |
|---|---|
| Programming language | Python 3.14 |
| Desktop interface | PySide6 / Qt for Python |
| Vault encryption | AES-256-GCM |
| Key derivation | PBKDF2-HMAC-SHA256 |
| Supported platform | Windows |
| User interfaces | Desktop GUI and CLI |

---

## Project

**Eagle Fortify Tool** is developed under **EEPX Labs**.

The project focuses on practical password utilities, local data control, and privacy-conscious credential management.

---

## License

**Proprietary — All rights reserved.**

See [LICENSE](LICENSE) for the full terms and conditions of use.

<div align="center">

**Eagle Fortify Tool**

*Your credentials. Your device. Your control.*

</div>
