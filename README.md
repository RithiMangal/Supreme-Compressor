# Supreme Compressor

**Advanced Linux Archiver & Compressor** — Dark neon luminous GUI, WinZip-style workflow,
multi-format compression (7z, ZIP, RAR, ISO, tar.zst), per-file progress tracking, and native
file-manager integration across Nautilus, Dolphin, Nemo, Thunar and PCManFM-Qt.

> Built with Rust, eframe/egui, and 7-Zip. Delivered as a portable AppImage. Zero
> dependencies beyond the system 7z binary; all assets bundled inside the image.

![Linux](https://img.shields.io/badge/platform-Linux-blue?style=for-the-badge&logo=linux)
![Rust](https://img.shields.io/badge/built_with-Rust-orange?style=for-the-badge&logo=rust)
![License](https://img.shields.io/badge/license-GPL--3.0-green?style=for-the-badge)
![AppImage](https://img.shields.io/badge/distro-AppImage-red?style=for-the-badge)

• Strategic Recommendation: Frames Lubuntu as the primary native development environment for guaranteed stability.
• Technical Breakdown: Bullet points highlighting the streamlined configuration, low resource footprint, and its ability to revive older hardware with zero lag.
• Veteran Insight: Concludes with an engaging nod to the fun of managing multiple Linux environments.
---

## Quick Start

```bash
# Download the latest AppImage
wget https://github.com/RithiMangal/supreme-compressor/releases/download/v1.0.0/Supreme-Compressor-x86_64.AppImage

# Make executable
chmod +x Supreme-Compressor-x86_64.AppImage

# Run
./Supreme-Compressor-x86_64.AppImage
```

On first launch the app auto-registers all context-menu actions and MIME-type defaults.

---

## How to Use

### 1. Main Window

The main window opens **maximized** by default with a borderless dark-neon UI.

**Top toolbar** (wraps to second row on narrow windows):

| Button | What it does |
|---|---|
| New Archive | Create a new empty archive session (7z or zip) |
| Open Archive | Open an existing archive to view/list contents |
| Add Files | Pick individual files to compress |
| Add Folder | Pick an entire folder to compress |
| Extract All | Extract an opened archive to a folder |
| Output Folder | Choose where the compressed archive is saved |
| Password / AES | Set a password for AES-256 encryption |
| Format | Dropdown: choose **7z Ultra LZMA2** or **tar.zst Ultra** |
| Compress Now | Start compression using the selected format |
| Cancel | Stop any running compression/extraction immediately |
| Transform | Convert SFX/executable between platforms (Win/Linux/macOS) |
| SFX Windows | Build a self-extracting `.exe` (Windows) |
| SFX macOS | Build a self-extracting `.command` (macOS) |
| SFX Linux | Build a self-extracting `.run` (Linux) |

**Center table** shows archive contents (Name, Size, Packed Size, Date & Time).

**Bottom-left: Per-File Progress**

While compressing or extracting, **every** selected file/folder gets its own row — not a single shared status line:

- **File name** — the file or folder being processed
- **Size** — its real size in B / KB / MB / GB
- **Progress bar** — that item's own bar, advancing from 0% to 100%
- **Percentage** — exact completion percentage for that item

The panel is scrollable in both windows, so a long list never pushes rows outside the visible area. Reciprocal (complementary) status colors are used throughout the progress UI: **magenta = done**, **cyan = error**, **blue = cancelled**, **amber = active/queued**. The overall progress bar uses the same magenta fill.

### 2. Standalone / Compact Window (File-Manager Actions)

When launched from a file manager context menu (right-click), a small **540×360** standalone window opens instead of the full main window.

**Controls:**
- **Compress Now** — compress selected file(s) using the chosen format
- **Output Folder** — choose where to save the output archive
- **Format** — dropdown: 7z Ultra LZMA2 or tar.zst Ultra
- **Extract Here** — extract archive into its current folder
- **Password** — toggle password input for AES-256
- **Cancel** — stop a running operation
- **Exit** — close the standalone window

**Drag to move:** Click and drag anywhere on the titlebar to move the standalone window around your screen.

**Automatic finish:** When a context-menu job finishes successfully, the standalone window closes by itself and your file manager opens the destination folder containing the new archive. Nothing extra to click. Failed or cancelled jobs keep the window open so you can read the status and retry.

### 3. Compression Workflow

1. Click **Add Files** or **Add Folder** to select what to compress.
2. (Optional) Click **Output Folder** to choose the destination.
3. Open the **Format** dropdown and pick **7z Ultra LZMA2** or **tar.zst Ultra**.
4. Click **Compress Now**.
5. Watch per-file progress bars at bottom-left — each file shows its own % complete.
6. When done, the status bar shows "Successfully created archive" with the output path.

### 4. Self-Extracting Archives (SFX)

Build standalone archives that extract without needing Supreme Compressor installed:

- **Windows `.exe`** — uses the official 7-Zip SFX stub (~210 KB overhead)
- **Linux `.run`** — stripped ELF console extractor (~415 KB overhead)
- **macOS `.command`** — 221-byte shell header + raw ZIP payload; uses macOS built-in `unzip`/`tail`, no 7z required (~221 bytes overhead)

These work on any computer with the matching OS — no third-party archive manager needed.

### 5. Cancel Mid-Operation

Click **Cancel** at any time. The app:

1. Sends **SIGINT** to the running 7z/zstd process (graceful stop)
2. Waits briefly; if it does not exit, sends **SIGKILL**
3. Removes any partial archive so you are not left with a broken file
4. Shows "Operation cancelled." in the status bar

Cancel is responsive because the engine polls for cancellation every 100 ms instead of blocking.

---

## Features

| Category | Details |
|---|---|
| **GUI** | Borderless window, custom metallic-gradient buttons with neon glow, vector-drawn SVG icons (no bitmaps/emoji). Title in red glowing text; subtitle in green. Title/subtitle never overlap; responsive to window resize. |
| **Controls** | Min button (`-`) yellow, Max/Restore button (`□/❐`) green, Close (`✕`) red. Contact button top-right opens `https://github.com/RithiMangal?tab=repositories`. |
| **Toolbar** | One wrapped row; overflow to second row. Format dropdown, Compress Now, Extract Here, Password/AES, Cancel, Transform. Metallic gradient + glow styling. |
| **Per-File Progress** | Bottom-left panel shows individual progress bar + percentage + status for every file being compressed or extracted. |
| **Archive Table** | Columns: Name, Size, Packed Size, Date. Status bar shows progress ratio and current operation. |
| **Compression Engine** | 7z `-mx=7 -m0=lzma2 -md=32m -mmt=on` (optimized LZMA2) with optional AES-256 encryption (`-mhe=on`). Background threads — UI never freezes even on multi-gigabyte archives. |
| **tar.zst Ultra** | zstd level 15 multithreaded (`-T0`) + tar layer. Preserves folder names and structure. |
| **Format Support** | <ul><li>Create: 7z (LZMA2/AES-256), ZIP (deflate), RAR, ISO, tar.zst (zstd level 15 multithreaded)</li><li>Extract: 7z, ZIP, RAR, ISO, tar.zst/tar.gz/tar.bz2/tar.xz (nested auto-unwrap)</li><li>Self-Extracting Archives: Windows `.exe`, Linux `.run`, macOS `.command`</li></ul> |
| **Transform** | Platform conversion: Windows `.exe` ↔ Linux `.run`, macOS `.command`. RAR/7z archives → Linux PE32 `.exe`. WinPE32 `.exe` → Linux `.run`. tar.zst, tar.gz, tar.bz2, tar.xz inner-tar unwrapping. |
| **Cancel** | Polling-based cancellation every 100 ms; SIGINT → SIGKILL chain; partial archive removed; status reports "Operation cancelled." |
| **File-Manager Integration** | <ul><li>Auto-registers on first launch: XDG `.desktop` entry, KIO servicemenu (Dolphin), Nemo actions, Nautilus scripts fallback (no `python3-nautilus` required), Thunar `uca.xml`, PCManFM-Qt mime default</li><li>47 MIME types mapped to `supreme-compressor.desktop` (7z, zip, rar, iso, gzip, bzip2, xz, zstd, lz4, lzma, compress, and many archive subtypes)</li><li>Right-click actions: Compress Ultra, Compress Password/AES-256, Make SFX (Windows/macOS/Linux), Open SFX in GUI, Extract Here</li><li>Double-click on any registered archive opens the app with that archive preloaded</li><li>KDE servicemenu `.desktop` must be `chmod 755`; compiled ELF launcher used (Dolphin rejects shell scripts)</li></ul> |
| **Portable AppImage** | Single-file image, runs on all Linux distros (UEFI/EFI firmware, Plasma, ParrotSec, any kernel). Contains bundled 7z engine + 7z.so codecs + zstd binary + all shared libraries. Writes only under `$HOME`. No sudo/admin required. |
| **Self-Extracting Archives (SFX)** | <ul><li>Windows: bundles `7z-win.sfx` stub — extractable on any Windows with 7-Zip installed</li><li>Linux: bundles `7z-con-linux.sfx` stripped ELF console extractor</li><li>macOS: 221-byte shell header + raw ZIP payload appended — uses built-in `unzip`/`tail`, **no 7z or base64 required**; ~221 bytes overhead vs ~33% expansion with old base64 method</li></ul> |
| **cargo audit** | Clean — **0 vulnerabilities** (allowed warnings: `paste`, `ttf-parser`, `zbus_names`, `zvariant`, `zvariant_derive`). Abort on build if any new vulnerability appears. |
| **Strip & Optimization** | Release profile: `opt-level = 3`, `strip = true`. LTO disabled (rustc ICE on this platform). |

---

## Requirements

- Rust (edition 2021) — only needed to rebuild from source
- `7z` (p7zip-full) in `PATH` for compression/extraction (the AppImage bundles its own 7z binary)
- GTK/Wayland or X11 desktop session (tested on LXQt + Openbox, Plasma, ParrotSec)

---

## Build From Source

```bash
# 1. Audit dependencies (abort if any vulnerability)
cargo audit

# 2. Build release binary
cargo build --release

# 3. Generate AppImage
./build_appimage.sh
```

The `build_appimage.sh` script:
1. Runs `cargo audit` and aborts on any vulnerability
2. Builds the optimized release binary (`opt-level=3`, `strip=true`)
3. Assembles the AppDir: desktop entry, vector icon (`supreme-compressor.svg`), MIME defaults
4. Bundles 7z engine + 7z.so codecs + zstd shared libs
5. Downloads `appimagetool` if missing
6. Produces `dist/Supreme-Compressor-x86_64.AppImage`

---

## Running

```bash
./dist/Supreme-Compressor-x86_64.AppImage
```

On first launch the app registers its file-manager context-menu actions automatically.
Subsequent launches are instant — the AppImage is a squashfs snapshot with an embedded binary.

---

## License

MIT. See `LICENSE` for full text.

---

## Contact

Source & issue tracker: <https://github.com/RithiMangal/supreme-compressor>

Email: rithimangal@example.com (replace with actual)

---

## Screenshot

![Supreme Compressor UI](https://github.com/RithiMangal/supreme-compressor/raw/assets/supreme-screenshot.png)

*(Add a screenshot once captured; the UI is a borderless dark-neon window with custom min/max/close buttons, a glowing red title "Supreme Compressor", green subtitle "Standalone Extractor / Compressor", metallic gradient toolbar, format dropdown, per-file progress bars at bottom-left, and a 7z archive table with progress. The Contact button glows in the top-right corner.)*
