# Win11Explorer

A Windows 11 style **File Explorer** written from scratch in **C++** using the
raw **Win32 API + GDI32** — no Qt, no MFC, no .NET. Compiles to a single
self-contained `Win11Explorer.exe` with MinGW-w64 `g++`.

## Features

- Win11-style UI: light top bar, round icon buttons (back / forward / up /
  refresh), rounded window corners, sidebar + list view layout
- Address bar (press **Enter** to go, supports `%ENV%` paths and `This PC`)
- Search box that filters the current folder as you type
- Sidebar quick links: Home, Desktop, Downloads, Documents, Pictures, Music,
  Videos, This PC, plus all drives (owner-drawn, with shell icons)
- File list with shell icons, columns Name / Date modified / Type / Size,
  click a column header to sort (folders always first)
- Back / forward / up history (Alt+Left, Alt+Right, Alt+Up)
- Double-click or Enter opens files and folders; drives listed under This PC
- Right-click menu: Open, Copy path, Properties, Refresh, New folder
- F2 rename, F5 refresh, Delete to Recycle Bin, Ctrl+A select all
- Per-Monitor-V2 DPI aware (crisp on HiDPI displays)
- Always renders the light Windows 11 theme

## Build

Requirements: MinGW-w64 g++ (WinLibs / MSYS2 / winget
`BrechtSanders.WinLibs.POSIX.UCRT`), plus `windres`, which ships with it.

```bat
build.bat
```

Output: `bin\Win11Explorer.exe`

### Why build.bat swaps `default-manifest.o`

MinGW's gcc driver always links its built-in `default-manifest.o` at the end
of the link step. That object carries a *non-zero* resource language, so
combining it with a custom `1 24` manifest in an `.rc` file makes binutils
abort with:

```
.rsrc merge failure: multiple non-default manifests
```

`build.bat` therefore backs the toolchain file up, temporarily replaces it
with our manifest object (`windres app.rc -O coff -o bin\default-manifest.o`),
links, and **always restores the original** (verified by the script itself).
The original is also kept as `default-manifest.o.orig.bak` next to it.

## Run on macOS with Whisky

A real macOS `.app` bundle can only be built on macOS — but Whisky wraps a
Windows `.exe` for you:

1. Copy `bin\Win11Explorer.exe` to your Mac.
2. Open **Whisky** and create a bottle (Windows 11 / Windows 10).
3. Use **+** / *Run an executable* (or drag the `.exe` into the bottle) and
   pick `Win11Explorer.exe`.
4. Optionally add it to your library so Whisky keeps a launcher entry for it.

## Project layout

```
G:\Win11Explorer\
├── build.bat          one-shot build script (includes manifest swap)
├── app.rc             embeds the manifest as resource 1/24
├── src\
│   ├── main.cpp       the whole application
│   └── app.manifest   Common Controls v6 + PerMonitorV2 DPI
└── bin\               build output
    ├── Win11Explorer.exe
    └── default-manifest.o   (intermediate resource object)
```
