# Caph for ArcaOS / OS/2

Caph is a physics sandbox puzzle game. Guide the red object to touch the green
object by drawing solid, wire (rope), or bendable shapes that interact with
gravity.

## Version

**0.1.0 – ArcaOS Release 1**

## License

GNU GPL v3 — see `LICENSE` for the full text.

## Requirements

- ArcaOS 5.x (or OS/2 Warp 4.5 with the appropriate runtime libraries)
- SDL2
- libpng

## How to Build

Use the provided OS/2 build script with GCC / EMX:

```
compile.cmd
```

The script sets the required `EMXOMFLD_*` linker environment variables and
calls `make -f makefile.os2`. The build produces `caph.exe`. Game data must be
in the `share/caph/` directory next to `bin/` (i.e. `../share/caph/` relative
to the executable).

## How to Run

```
caph.exe
```

## Configuration

The configuration file is stored in the XDG Base Directory:

- `%XDG_CONFIG_HOME%/caph/caph.conf` if `XDG_CONFIG_HOME` is set
- `%HOME%/.config/caph/caph.conf` otherwise

The directory is created automatically on first run. The file contains three
integers: `<width> <height> <fullscreen>`. Example:

```
1024 768 0
```

The minimum resolution is 1024 × 768; lower values in the config are ignored.

## Keyboard Shortcuts

| Key         | Action                                    |
|-------------|-------------------------------------------|
| Alt+Enter   | Toggle fullscreen                         |
| Ctrl+X      | Quit immediately                          |
| Q           | Quit                                      |
| P           | Toggle play / edit mode                   |
| T           | Pause / resume physics                    |
| R           | Reload current level                      |
| N           | Next level                                |
| M           | Previous level                            |
| A           | Select: default (soft) object type        |
| S           | Select: soft                              |
| B           | Select: bend                              |
| C           | Select: const (edit mode)                 |
| X           | Select: phantom (edit mode)               |
| F           | Select: fly (edit mode)                   |
| D / Escape  | Remove last object                        |
| E           | Erase all objects (edit mode)             |
| K           | Save map (edit mode)                      |
| U           | Toggle brush rotation mode                |
| 1 / 2 / 3  | Toggle phantom colour flags R / G / B     |
| 4 / 5       | Toggle player / target flags              |
| G           | Toggle background flag                    |
| H           | Toggle NOCROSS flag                       |
| L           | Toggle loop flag                          |

## ArcaOS Port — Change Log

### Release 1 — upstream 0.1.0

#### SDL1 → SDL2 Migration

- Updated SDL header paths from `SDL/SDL.h` to `SDL2/SDL.h`.
- Removed SDL1 `#undef main` (not needed in SDL2).
- Replaced `SDL_SetVideoMode` with `SDL_CreateWindow`.
- Replaced `SDL_WM_SetCaption` with `SDL_SetWindowTitle` (title set in
  `SDL_CreateWindow`).
- `SDL_ShowCursor(0)` → `SDL_ShowCursor(SDL_DISABLE)`.
- `SDL_SoftStretch` (removed in SDL2) → `SDL_BlitScaled`.
- `SDL_SRCCOLORKEY` → `SDL_TRUE` in `SDL_SetColorKey`.
- `SDL_GL_SwapBuffers()` → `SDL_GL_SwapWindow(window)` (OpenGL path).

#### Fullscreen Scaling

- Introduced a **virtual back buffer** (`screen`) created with
  `SDL_CreateRGBSurface` at the game's native resolution. All rendering
  targets this surface unchanged.
- Added `caph_present()`: blits the virtual buffer to the real window surface
  with aspect-ratio-preserving `SDL_BlitScaled` when the window is larger than
  the native resolution (fullscreen), or a plain `SDL_BlitSurface` in windowed
  mode, then calls `SDL_UpdateWindowSurface`.
- Alt+Enter fullscreen toggle uses `SDL_SetWindowFullscreen` without
  reassigning the virtual buffer pointer.

#### New Keyboard Shortcuts

- **Alt+Enter** — toggle fullscreen at runtime.
- **Ctrl+X** — quit immediately from any mode.

#### Minimum Resolution

- Resolution is clamped to a minimum of 1024 × 768 regardless of the value
  stored in the configuration file.

#### XDG Configuration Path

- Configuration file moved from `$HOME/.caph.conf` to
  `$XDG_CONFIG_HOME/caph/caph.conf` (defaulting to
  `$HOME/.config/caph/caph.conf`). The directory is created automatically.

#### OS/2-specific Additions

- Added `src/caph.def` module definition file (`NAME CAPH WINDOWAPI`).
- Added `makefile.os2` for building with GCC / EMX on ArcaOS.
- Added `compile.cmd` build helper that sets `EMXOMFLD_*` linker variables.
- `sys_get_data_dir()` falls back to `../share/caph/` directly on OS/2
  (no `/proc/self/exe`).

#### Compiler Warning Fixes (GCC 9.2 `-Wall`)

- Renamed `static int time` → `t_run` to avoid conflict with `time_t time()`
  from `<time.h>` (pulled in by `<sys/stat.h>`).
- Removed unused `ret` variables in `sys_chdir`, `main` (`caph.c`),
  `img_load_png` (`draw.c`), and `load_map` (`maps.c`); all unchecked
  return values wrapped with `(void)`.
- Fixed `-Wformat-truncation`: enlarged `path` buffer in `sys_get_config`.

## Authors

- Original game: Roman Belov

## Links

- Upstream: https://sourceforge.net/projects/caphgame/
