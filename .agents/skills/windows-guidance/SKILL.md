---
name: windows-guidance
description: Use when building, running, formatting, or checking rct3++ on Windows, or when g++, ./nob, slangc, or clang-format fails with "command not found", "cannot find -lglfw3", "cannot find -lvulkan-1", or "cannot start clang-format".
---

# Windows Guidance

## Overview

rct3++ builds only inside the MSYS2 UCRT64 shell. The Bash tool (Git Bash) and the PowerShell tool lack `/ucrt64/bin` on `PATH`, so every toolchain command fails there. Read `docs/windows.md` for the install steps.

## Run a Command

Load `mcp__terminal__run_in_terminal` with ToolSearch, then type one line into the user's Terminal panel:

```powershell
& '<msys2-root>\msys2_shell.cmd' -ucrt64 -defterm -no-start -here -c '<cmd>'
```

- `<msys2-root>` is the MSYS2 install folder. `-here` starts in the repo root. `<cmd>` is plain words and `;`.
- The tool refuses pipes, `$x` variables, backticks, and multi-line input. Put longer logic in a script file (Write) and run that file.
- A long line can be garbled and cancelled. Keep the line short.
- Read the output with `mcp__terminal__read_terminal` and `wait_for_output_ms`. Close the tab with `mcp__terminal__stop_terminal_tab` when done.

The Bash and PowerShell tools stay the right choice for work outside the toolchain: `git status`, reading files, and tools such as `agent-skills`.

## Commands

| Goal | `<cmd>` |
|---|---|
| Check the toolchain | `which g++ slangc; g++ --version` |
| Bootstrap `nob` | `g++ -std=c++26 -o nob nob.cpp` |
| Build and open the viewer | `./nob run` (add `-- --frames 120` to exit after 120 frames) |
| Formatting check | `./nob format --check` |

Do not run `./nob test` unless the user asks. A run takes over 5 minutes. Never commit.

## Toolchain Facts

- `g++` 16 or later comes from the `mingw-w64-ucrt-x86_64-gcc` package. `slangc` comes from the Slang release zip, copied into `/usr/local/bin`.
- `clang-format` comes from `pacman -S --needed mingw-w64-ucrt-x86_64-clang-tools-extra`.
- `RCT3PP_GAME_PATH` takes MSYS form such as `/c/path/to/game`. Check `echo $RCT3PP_GAME_PATH` through the launch line before a run that reads game files.
- A Windows build of `direnv.exe` exports Windows-style `PATH` that MSYS bash cannot read, which hides `g++`. Keep machine `PATH` setup out of the repo `.envrc`.

## Errors

| Message | Fix |
|---|---|
| `g++: command not found` | The command ran outside UCRT64. Use the launch line above. |
| `cannot find -lglfw3` | Install `mingw-w64-ucrt-x86_64-glfw`. |
| `cannot find -lvulkan-1` | Install `mingw-w64-ucrt-x86_64-vulkan-loader`. |
| `slangc: command not found` | Copy `slangc.exe` and its DLLs into `/usr/local/bin`. |
| `cannot start clang-format` | Install `clang-tools-extra`, see Machine Facts. |

## Docs Rule

Public docs give the standard install step for each requirement. A workaround goes in chat when the user asks, never in `docs/`.
