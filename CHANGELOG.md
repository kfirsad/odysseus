# Changelog

All notable changes to the Odysseus self-hosted AI workspace will be documented in this file.

This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.1.0] - 2026-06-02

### Added
- **Standalone Windows Distribution (`Odysseus.exe`)**: Added a compiled, zero-dependency, single-folder portable Windows build (`dist/Odysseus`) for seamless deployment. Users no longer need to manually manage Python runtimes, create virtual environments, install packages, or execute terminal commands to deploy Odysseus on Windows.
- **Windowless/Headless Background Service**: The packaged application executes in headless mode (`--noconsole`), keeping the Windows taskbar and desktop clutter-free. No black terminal command prompt remains open.
- **Instant Splash Loader Screen**: Spawns a lightweight, borderless dark-themed Tkinter splash screen (`⛵ Odysseus - Launching background services...`) in a background daemon thread at the absolute beginning of application startup. Users receive instant visual feedback (<100ms) upon double-clicking `Odysseus.exe` while heavy AI packages and server dependencies initialize.
- **Windows System Tray Integration**: Spawns an interactive system tray icon displaying the Odysseus sails brand logo.
  - *Double-Click*: Instantly launches or focuses the Odysseus web interface in the system's default browser.
  - *Context Menu*: Right-clicking the tray icon opens a custom context menu with **Open Odysseus** and **Exit** options.
  - *Graceful Shutdown*: The **Exit** handler cleanly terminates the background Uvicorn web server and releases all system resources and database file locks (`os._exit(0)`).
- **Embedded HD Application Icon**: Embedded a high-quality multi-resolution custom sails boat icon (`static/icon.ico` supporting resolutions from 16x16 up to 256x256) inside the compiled `Odysseus.exe` executable binary resource.
- **Premium Real-Time Diagnostics Terminal UI**: Added a custom logs console under **Settings -> System** for administrators to diagnose the workspace in real-time.
  - *Admin-Gated Security*: Secured with the `require_admin(request)` middleware, ensuring logs cannot be viewed by unauthorized users.
  - *Syntax Highlighting*: Standard log levels are color-coded in real-time (Emerald Green for `INFO`, Amber for `WARNING`, Bright Red for `ERROR`, and Gray for `DEBUG`) in a sleek dark monospaced console.
  - *Interactive Querying & Filtering*: Built-in regular expression (Regex) search bar to live-filter log lines by module (e.g. `uvicorn`, `chromadb`, `agent_loop`) or text.
  - *Polling and Stream Control*: Toggle switch to enable/disable real-time auto-polling with customizable payload limits (50, 100, 200, 500 lines) to optimize network bandwidth.
  - *Auto-Scroll Lock*: Automatically scrolls to the bottom of the console as new log events stream in.

### Fixed
- **Uvicorn Stdout Stream Crash**: Resolved a critical startup crash under windowless Windows mode where Python standard streams (`sys.stdout`, `sys.stderr`) are set to `None`. Overrode standard streams with a custom thread-safe `NullWriter` implementing a dummy standard stream interface and an explicit `isatty() -> False` method, preventing Uvicorn logging configurations from failing.
- **Python Import Latency User Feedback**: Handled the 10-20s startup delay caused by loading heavy ONNX, PyTorch, Pillow, and ChromaDB dependencies natively by prioritizing the Tkinter splash thread execution before any third-party package imports.

### Technical Details & Distribution
- **Compilation Toolchain**: Built using PyInstaller with customized spec definitions linking native DLL hooks and packaging static resources.
- **Portability**: All system configurations, databases (`app.db`), memories (`memory.json`), vectors (`chroma/`), and user uploads reside within the portable `data/` directory relative to the binary path, ensuring complete self-containment.

---

> [!NOTE]
> For users who still prefer manual deployments or running inside customized Docker containers, the native Git, Docker Compose, macOS, and Linux setup procedures are fully supported and remain unchanged. See the [README](README.md) for details.
