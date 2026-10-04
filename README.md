# Root Scanner

[![Platform](https://img.shields.io/badge/Platform-Windows%20x64-0078D6?style=flat-square&logo=windows)](https://microsoft.com/windows)
[![Target](https://img.shields.io/badge/Target-Minecraft%20Java%20Edition-5B8C32?style=flat-square)](https://minecraft.net)
[![Architecture](https://img.shields.io/badge/Architecture-x86__64-orange?style=flat-square)](#system-requirements)
[![Privileges](https://img.shields.io/badge/Privileges-User%20%2F%20Admin-lightgrey?style=flat-square)](#execution)

**Root** is a standalone, forensic-grade anti-cheat introspection scanner engineered specifically for Minecraft Java Edition on Windows (x64). Built in native C++20, Root performs comprehensive, deterministic, read-only analysis of live `javaw.exe` process memory, HotSpot JVM internals, NTFS USN Change Journals, and game directories to detect and reconstruct injected cheat modules, manual mappers, disguised binaries, fileless routines, and self-destruct artifacts.

---

## Key Features & Detection Engines

### 1. Native DLL Forensics & In-Process Injection
* **Historical Module Unload Tracing (`LdrpUnloadedModuleList`)**: Reads the NT Loader's circular unload event buffer (`ntdll!RtlGetUnloadEventTraceEx`) to catch cheat DLLs that injected and subsequently unloaded (self-destructed) during the current Windows session.
* **Ghost DLL & Unlinked PEB Discovery**: Traverses committed executable memory to unmask stealth modules deliberately unlinked from the PEB (`InLoadOrderModuleList`).
* **Manual Map & Reflective Injection Detection**:
  * Scans unbacked virtual memory (`MEM_PRIVATE` / `MEM_MAPPED`) for mapped PE headers (`IMAGE_DOS_HEADER`, `IMAGE_NT_HEADERS`).
  * Reconstructs wiped, zeroed, and header-stripped in-memory DLLs by recovering intact PE section header tables (`.text`, `.rdata`, `.data`).
  * Detects headerless manual-mapped `.text` code sections through x64 function prologue analysis and CRT string correlation.
* **Disguised Native DLLs**: Detects PE executables disguised with non-executable extensions (`.png`, `.jpg`, `.dat`, `.tmp`, `.bin`) or polyglot image wrappers (`\x89PNG` headers).
* **Dangling Detour Hooks**: Disassembles function prologues across system libraries (`ntdll.dll`, `kernel32.dll`, `user32.dll`, `opengl32.dll`, `glfw.dll`) to expose inline trampolines redirecting execution into unbacked private memory.
* **Window Procedure Hook Inspection**: Verifies `GWLP_WNDPROC` addresses via `GetWindowLongPtr` to catch unauthorized input sniffing and GUI drawing detours.
* **DLL Sideloading / Proxy Injection**: Audits loaded system modules against verified Windows system directories (`System32`, `SysWOW64`, `wbem`) to detect hijacked proxy DLLs (`version.dll`, `winmm.dll`, `d3d11.dll`, etc.).
* **In-Process .NET CLR Hosting**: Detects hosting of the Common Language Runtime (`clr.dll`, `coreclr.dll`, `mscoree.dll`) inside `javaw.exe` used to execute in-memory C# assemblies.

### 2. HotSpot JVM & In-Memory Bytecode Analysis
* **In-Memory Java Class Injection**: Identifies raw Java bytecode (`0xCAFEBABE` class binaries, Java 8 through 23) injected into JVM virtual memory without backing `.jar` archives on disk.
* **Dynamic Bytecode Mappers**: Detects in-memory mapper routines utilizing `Unsafe.defineAnonymousClass`, `MethodHandles.Lookup.defineHiddenClass`, or `JVM_DefineClass` to load bytecode dynamically.
* **HotSpot Dynamic Attach Tracing**: Detects HotSpot Attach API artifacts (`.attach_pid<PID>` IPC tokens, `\\.\pipe\javatool<PID>` named pipes) left by external injection tools.
* **JVMTI Agent Injection**: Detects modules exporting `Agent_OnAttach` or `Agent_OnLoad` attached dynamically to the running instance.
* **Memory Protection Scan Evasion**: Detects executable code regions cloaked with `PAGE_GUARD` permissions (Ekko, Gargoyle, Foliage sleep obfuscation).
* **Native JNI Infiltration**: Scans unbacked executable allocations for references to JNI invocation functions (`JNI_GetCreatedJavaVMs`, `AttachCurrentThread`, `JVM_FindClassFromCaller`).
* **Stomped Modules & Code-Caves**: Compares loaded module code sections against on-disk binaries to expose trampoline stomping and overwritten functions.

### 3. NTFS USN Change Journal Forensics
* **Low-Level Journal Parsing**: Direct volume query of NTFS `$Extend\$UsnJrnl` via `FSCTL_READ_USN_JOURNAL`.
* **Self-Destruct Timeline Correlation**: Cross-references deleted `.dll`, `.exe`, `.jar`, `.pf` (Prefetch), and temporary files against process creation timestamps to identify cheat launchers or payloads deleted immediately prior to or during a screen-share.
* **Anti-Forensics Tracking**: Flags log truncation (`latest.log`, `debug.log`), Prefetch clearing, and browser artifact wiping.

### 4. Mod Archive & Static Bytecode Inspection
* **Recursive JAR Archive Walk**: Deep inspection of nested JAR files and unpacked classes.
* **Embedded Native Injectors**: Flags mod JARs that embed native binaries (`.dll`, `.exe`) alongside reflective class loaders.
* **Combat & Bounding Box Manipulation**: Flags entity AABB expansion, reach modifications, and synthetic combat loops.
* **Obfuscation & Math Double-Struck Unicode**: Decodes mathematical alphanumeric Unicode symbols and XOR/reflection string decrypters used to conceal cheat symbols.
* **Curated Cheat Signature Library**: Scans across 1,000+ verified signatures covering commercial and private cheat clients (Vape, Drip Lite, Slinky, Bloom, Raven B+, Entropy, Dream, Augustus, etc.).

### 5. Fileless Execution & Shellcode Detection
* Identifies in-memory PowerShell staging scripts (`IEX`, `DownloadString`, `Assembly.Load`), AMSI bypasses (`AmsiUtils.amsiInitFailed`), and ETW event patching (`EtwEventWrite 0xC3`).

---

## System Requirements

| Component | Minimum Requirement | Recommended |
| :--- | :--- | :--- |
| **Operating System** | Windows 10 x64 (Version 1809+) | Windows 10/11 x64 (Latest) |
| **Architecture** | 64-bit (x86_64) | 64-bit (x86_64) |
| **Target Runtime** | Java 8, 11, 17, 21, 22, 23 (Zulu, Temurin, Corretto, Oracle, Microsoft) | Any standard OpenJDK build |
| **Permissions** | Standard User (Admin recommended for USN Journal parsing) | Administrator |

---

## Download & Execution

1. Download the latest release binary from the [Releases](https://github.com/) page:
   * **`Root.exe`** (or **`PBS.exe`**)
2. Place the executable in any directory. Root is fully portable and requires no installation.
3. **Run as Administrator** (Right-click -> *Run as administrator*) to enable NTFS USN Change Journal parsing and cross-privilege process inspection.
4. Root will automatically discover your active Minecraft `javaw.exe` instance and begin a non-invasive, read-only scan.
5. If multiple game instances are detected, select the target PID from the prompt.

### Command Line Flags
```text
Root.exe [options]
  --pid <PID>        Target specific javaw.exe process by PID
  --headless         Execute non-interactive scan and output JSON/HTML to stdout/disk
  --report-dir <dir> Override default report directory
```

---

## Inspection Output & Reports

Upon completion, Root exports a structured, interactive HTML report with categorized forensic findings, virtual memory addresses, disassembly evidence, and journal timelines.

Reports are saved locally to:
```
%TEMP%\RootScanner\report_<PID>_<TIMESTAMP>.html
```

### Report Severity Levels
* **DETECTION (Red)**: Confirmed cheat payload, manual-mapped PE, unbacked bytecode injection, deleted injector correlation, or verified cheat signature.
* **SUSPICIOUS (Yellow)**: Unverified third-party native code, unusual memory protection transitions, or suspicious execution attributes.
* **WARNING / REVIEW (Orange)**: Capability combination requiring manual review (e.g. dynamic reflection combined with native loaders).
* **INFO (Blue)**: Operational telemetry, evaluated historical module unloads, and JVM runtime metadata.

---

## Ecosystem Compatibility & False-Positive Elimination

Root features an advanced whitelist engine that eliminates false positives across standard client environments:

* **Mod Loaders & Launchers**: Full out-of-the-box support for Vanilla, Modrinth App, Prism Launcher, CurseForge, Lunar Client, Badlion Client, Feather Client, and ATLauncher.
* **Game Recording & Overlays**: Pre-whitelisted hooks from Medal.tv (`medal-hook64.dll`), Discord, OBS Studio, RivaTuner Statistics Server (RTSS), GeForce Experience / ShadowPlay, Overwolf, and SteelSeries Moments.
* **Optimization & Performance Mods**: AST-level whitelisting for ImmediatelyFast (`net/lenni0451/reflect`), ModernFix (`org/embeddedt/modernfix`), CaffeineMC (`Sodium`, `Lithium`, `Iris`), `MixinSquared`, `MixinExtras`, and Cloth Config.
* **Thread Stack Integrity**: Accurately differentiates standard non-executable `PAGE_GUARD | PAGE_READWRITE` thread stack guard pages from executable sleep-obfuscated cheat caves.
* **Read-Only Memory Mappings**: Validates executable permissions on memory-mapped sections before evaluating PE headers, eliminating false flags on data-mapped font and resource files.

---

## Safety & Non-Invasive Design

* **100% Read-Only**: Root never modifies process memory, never injects native DLLs into your game, and never patches running game code.
* **No Kernel Drivers**: Operates entirely in user mode using official Windows NT APIs (`ReadProcessMemory`, `VirtualQueryEx`, `FSCTL_READ_USN_JOURNAL`).
* **Zero Antivirus / Anti-Cheat Interference**: Designed to run cleanly without triggering EAC, BattlEye, or Windows Defender.
