

# LuckyKiller

A detection, removal, and blocking tool for the **Luckyware RAT** — a Remote Access Trojan that infects Windows development environments by injecting malicious code into C++ projects, ImGui source files, Windows SDK headers, and Visual Studio solution files.

---

## Features

- **Process termination** — kills known Luckyware processes (`Berok.exe`, `Retev.exe`, `Zetolac.exe`, `HPSR.exe`)
- **File scanning** — multi-threaded scan of `.exe`, `.dll`, `.vcxproj`, `.csproj`, `.suo`, `.h`, `.hpp`, `.cpp` files
- **PE inspection** — detects malicious PE sections (e.g. `.rcd*`) and embedded droppers
- **VCXPROJ cleaning** — removes hidden PowerShell download commands injected into build events
- **ImGui detection** — finds obfuscated hex blob payloads and `system()` dropper calls injected into Dear ImGui source files
- **SDK header cleaning** — detects Luckyware SDK namespace injections (`VccLibaries`, `SDKInfector`, etc.)
- **SUO hijack detection** — finds XOR key artifacts in Visual Studio `.suo` files
- **Temp dropper cleanup** — removes chrono-named dropper files from `%TEMP%` and `%APPDATA%`
- **YARA rules** — optional deeper matching using embedded YARA signatures
- **Network blocking** — blocks C2 domains via the Windows HOSTS file and adds outbound firewall rules for C2 IPs
- **Dry run mode** — preview detections without modifying any files

---

## Requirements

- **Windows** (uses Win32 APIs for admin elevation, firewall, and console control)
- **Python 3.8+**
- **Administrator privileges** (required for network blocking and process termination)

Dependencies are installed automatically on first run:

- `pefile`
- `yara-python`
- `colorama`

---

## Usage

Run the script directly. It will request administrator elevation automatically if needed.

```
python main.py
```

### Menu Options

| Option | Description |
|--------|-------------|
| `1` | Full scan + auto-remove + block C2 (recommended) |
| `2` | Full scan only — detect and report, no changes |
| `3` | Block C2 domains and IPs only |
| `4` | Kill Luckyware processes only |
| `5` | Scan a specific folder |
| `6` | Dry run — preview what would be detected/removed |

---

## What Gets Cleaned

| Threat Type | Action |
|-------------|--------|
| Infected PE (`.exe`/`.dll`) | Malicious section execute-bits cleared |
| Infected `.vcxproj` / `.csproj` | Malicious build event lines removed |
| Infected ImGui source files | Hex blob and `system()` calls replaced |
| Poisoned SDK headers | Injected namespace lines removed |
| Hijacked `.suo` files | File wiped and deleted |
| Temp droppers | File wiped and deleted |

---

## Network Indicators Blocked

The tool blocks all known Luckyware C2 infrastructure including domains such as `luckyware.co`, `darkside.cy`, `phobos.top`, and others, as well as C2 IP addresses via Windows Firewall outbound rules. DNS cache is flushed after blocking.

---

## Notes

- After running LuckyKiller, a full scan with an AV tool (e.g. Bitdefender) is recommended for final verification.
- Severely infected systems — particularly those with widespread SDK or PE infections — may require a clean Windows reinstall.
- The tool skips directories like `.vs`, `.git`, `node_modules`, and Windows/Qt SDK paths to reduce noise (SDK headers are scanned separately).
