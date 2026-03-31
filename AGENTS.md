# Jamulus Agent Guidelines

This document provides essential information for agentic coding tools operating
in the Jamulus repository. Adhere to these instructions to ensure consistency
and compatibility with the project's standards.

## 1. Build, Lint, and Test Commands

### Build Commands

Jamulus uses **qmake** (Qt build system). Ensure you have Qt 5.12.2+ installed.

- **Linux:**

  ```bash
  qmake Jamulus.pro
  make -j$(nproc)
  ```

- **macOS (Xcode project):**

  ```bash
  qmake -spec macx-xcode Jamulus.pro
  xcodebuild build
  ```

- **Windows:**

  ```powershell
  .\windows\deploy_windows.ps1 "C:\Qt\5.15.2\msvc2019" "C:\Qt\5.15.2\msvc2019_64"
  ```

- **Build Options:** Use `qmake "CONFIG+=<option>"` to customize the build:
  - `headless`: Disable GUI (often used with `serveronly`).
  - `serveronly`: Build only the server component.
  - `nojsonrpc`: Disable JSON-RPC support.
  - `noupcasename`: Use `jamulus` as the binary name instead of `Jamulus`.

### Lint and Formatting

The project enforces a strict coding style via **clang-format**.

- **Run C++ Formatter:**

  ```bash
  make clang_format
  ```

  _Note: This runs `clang-format -i` on all relevant source files._

- **Python:** Use `pylint` (version < 3.0) for scripts in `./tools`.
- **Bash:** Use `shellcheck` and `shfmt`.

### Testing

Jamulus does not have a traditional `make test` suite. Verification relies on:

- **Build Verification:** Ensure the project compiles after changes.
- **Manual Testing:** Run the generated binary and verify functionality.
- **CI Checks:** Automated builds and style checks run on every PR via GitHub Actions.
- **Testbench:** A manual testbench utility exists in `src/testbench.h`, but it
  is primarily for protocol stress testing.

---

## 2. Code Style Guidelines

Jamulus follows a consistent C++/Qt style. Refer to `.clang-format` and
`.editorconfig` for automated rules.

### Naming Conventions

- **Classes:** Prefix with `C` (e.g., `CClient`, `CProtocol`, `CSocket`).
- **Methods:** PascalCase (e.g., `Update()`, `OnTimer()`, `ProcessAudio()`).
- **Variables:** camelCase with optional type prefixing (Hungarian-lite):
  - `strName` for `QString`
  - `iPort` for integers
  - `bEnabled` for booleans
  - `pPointer` for pointers
- **Enums:** Prefix with `E` (e.g., `ELicenceType`) and values often start with
  a 2-3 letter prefix (e.g., `LT_NO_LICENCE`).
- **GUI Components (Prefixes):**
  - `lbl`: QLabel
  - `cbx`: QComboBox
  - `edt`: QLineEdit
  - `but`: QPushButton
  - `sld`: QSlider
  - `chb`: QCheckBox

### Formatting and Braces

- **Indentation:** 4 spaces (no tabs).
- **Braces:** Always on a **new line** for classes, functions, and control
  statements (`if`, `else`, `for`, `while`).

  ```cpp
  if ( bSuccess )
  {
      DoSomething();
  }
  else
  {
      DoOther();
  }
  ```

- **Spacing:**
  - Insert a space **before and after** parentheses `( )` in control statements
    and function calls.
  - No space between `)` and `;`.
  - No space before an empty `()`.

  ```cpp
  void Function ( int iParam ); // Correct
  Function ( 10 );              // Correct
  ```

- **Pointers:** Left-aligned: `Type* pPointer`.

### Imports and Headers

- Use `#include "header.h"` for project-local files.
- Use `#include <Header>` for Qt and standard library headers.
- **Do not sort includes** automatically; follow the existing order in the file.
- Use `#pragma once` in all header files.

### Error Handling and Logging

- Use Qt's logging mechanisms: `qInfo()`, `qDebug()`, `qWarning()`, `qCritical()`.
- Avoid C++ exceptions; use return codes or status flags where appropriate.
- For UI-facing errors, use `QMessageBox` (only if not in `headless` mode).
- Custom error class: `CGenErr` in `src/global.h`.

### UI and Translations

- All user-facing strings **must** be wrapped in `tr()`.
- **Do not** use string concatenation for parameters. Use `.arg()` substitution.
  - **Do:** `tr ( "Connected to %1" ).arg ( strServerName )`
  - **Don't:** `tr ( "Connected to " ) + strServerName`

### Best Practices

- **Stability:** Jamulus is used for live performances. Stability is the #1
  priority. Avoid risky optimizations or complex dependencies.
- **KISS & Unix Philosophy:** Keep it simple. Implement features as interfaces
  (like JSON-RPC) rather than bloating the core if possible.
- **Qt Compatibility:** Maintain compatibility with Qt 5.12.2.
- **C++ Version:** Use C++11 (standard for the project), except for specific
  components like Android/Oboe which use C++17.
- **Licensing:** Every new file must include the GPL-2.0 license header found
  in existing files.

---

## 3. Repository Structure

- `src/`: Core Jamulus source code.
- `src/sound/`: Audio backend implementations (ASIO, CoreAudio, JACK, Oboe).
- `src/recorder/`: Audio recording logic (Reaper project export, WAV).
- `libs/`: Third-party libraries (Opus, Oboe).
- `linux/`, `mac/`, `windows/`, `ios/`, `android/`: Platform-specific
  deployment scripts and assets.
- `tools/`: Helper scripts for translations, changelogs, etc.
- `docs/`: Documentation for protocols and APIs.

## 4. Architecture Notes

- **Communication:** Uses a custom UDP protocol. Message IDs are defined in
  `src/protocol.h` (e.g., `PROTMESSID_ACKN`).
- **Data Structures:** Uses `CVector` as a primary container wrapper.
- **Threading:** Heavy use of Qt's event loop and signals/slots for networking
  and UI. High-priority audio callbacks are managed by platform-specific sound
  backends.
- **State Management:** `CSettings` handles persistence via INI files.
- **RPC:** Support for JSON-RPC is integrated for external control.
