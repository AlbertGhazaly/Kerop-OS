# Kerop-OS

> A small x86 32-bit protected-mode operating system, written in C and NASM assembly, that boots in the QEMU emulator into a FAT32-backed shell with multitasking.

🇮🇩 [Baca dalam Bahasa Indonesia](README.id.md)

> **Note:** This repository is a fork of the original team repository, [labsister21/os-2024-kerop-os](https://github.com/labsister21/os-2024-kerop-os), where the project was developed.

Kerop-OS is our major assignment (Tugas Besar) for the Operating Systems course, 2024. It was built across three milestones, from a text framebuffer and interrupts up to user-space processes and a round-robin task scheduler driven by the timer interrupt.

## Table of Contents
- [Demo](#demo)
- [Features](#features)
- [Quickstart](#quickstart)
- [Shell Commands](#shell-commands)
- [Project Structure](#project-structure)
- [Technologies Used](#technologies-used)
- [Contributors](#contributors)
- [Acknowledgements](#acknowledgements)

## Demo

### Boot screen

![Kerop-OS boot screen: a grey loading bar filling up with yellow](gif/loading.gif)

This is the first thing you see when Kerop-OS starts in QEMU. Once the kernel has booted and launched the shell, a loading bar fills from grey to yellow, the screen shows a short "HI" greeting in yellow block letters, and then the shell appears.

### Shell

![Kerop-OS shell session running filesystem commands and the clock](gif/shell.gif)

After booting, you land in the Kerop-OS shell. The prompt shows the current path, for example `kerop-os/ROOT/ $`. In this session:

1. `mkdir aku`, `cd aku`, `mkdir ganteng` and `cd ganteng` create and enter nested directories. The prompt updates to `kerop-os/ROOT/aku/ganteng/ $`.
2. `nano sekali.txt` creates a text file, and the line `aku ganteng sekali` is typed into it.
3. `cat sekali.tx` fails with `INVALID FILE EXTENSION`, and `cat sekali.txt` prints the file's contents.
4. `./clock` fails with `FILE NOT FOUND` inside the subdirectory, because the clock program lives in the root directory.
5. `cd ../../` goes back to `ROOT`, and `./clock` starts the clock as a separate process. The current time appears in the bottom-right corner while the shell stays usable.
6. `ls` lists the root directory: `shell`, `clock` and the new `aku` folder.

See [Shell Commands](#shell-commands) for every command the shell supports.

## Features

**Milestone 1**
- Text framebuffer
- Interrupts (GDT, IDT, interrupt handling)
- Keyboard driver
- FAT32 filesystem on an emulated IDE disk

**Milestone 2**
- Memory management (paging)
- Kernel / user-space separation, with system calls via `int 0x30`
- Interactive shell running in user mode

**Milestone 3**
- Process structures (process control blocks)
- Task scheduler and context switching
- Shell commands for process management (`ps`, `kill`)
- Multitasking with a clock program that runs alongside the shell (time read from the CMOS RTC)

## Quickstart

The project was developed on **Windows Subsystem for Linux (WSL)**; any Debian/Ubuntu-like Linux environment with the same toolchain should follow the same steps.

1. Install the toolchain:
   ```bash
   sudo apt update
   sudo apt install -y nasm gcc qemu-system-x86 make genisoimage gdb
   ```
   The build also calls `qemu-img` to create the disk image. If it isn't available after the step above, install it with `sudo apt install -y qemu-utils`.

2. Clone the repository:
   ```bash
   git clone https://github.com/AlbertGhazaly/Kerop-OS.git
   cd Kerop-OS
   ```

3. Build and run:
   ```bash
   make start   # create the 4 MB disk image and insert the shell and clock programs
   make run     # build the kernel ISO and boot it in QEMU
   ```

QEMU opens and boots into the shell with a `kerop-os/ROOT/ $` prompt.

### Make targets

| Target | What it does |
| --- | --- |
| `make start` | Creates `bin/sample-image.bin` and inserts the `shell` and `clock` user programs into it |
| `make run` | Builds the kernel, packs it into `bin/OS2024.iso` (GRUB), and boots QEMU with the disk attached |
| `make build` | Builds the kernel ISO without running it |
| `make clean` | Removes object files and the built kernel binary |

`make run` starts QEMU with `-s`, so you can attach GDB on port 1234 for debugging. Launch and task configs for Visual Studio Code are included in `.vscode/`. To install VS Code on Windows, run `winget install microsoft.visualstudiocode`.

## Shell Commands

| Command | Description |
| --- | --- |
| `cd <dir>` | Change directory (supports `.` and `..`) |
| `ls` | List the current directory |
| `mkdir <dirname>` | Create a directory |
| `cat <file>` | Print a file's contents |
| `nano <file>` | Create a file and type a single line of text into it |
| `cp <src> <dest>` | Copy a file |
| `mv <src> <dest>` | Move a file |
| `rm <file>` | Remove a file |
| `find <name>` | Find a file or folder by name |
| `clear` | Clear the screen |
| `./<file>` | Run an executable from the filesystem |
| `ps` | List processes with their ID, name and status |
| `kill <pid>` | Terminate a process (the shell itself can't be killed) |
| `clock` | Start the clock process, which shows the current time on screen |

## Project Structure

```
src/
├── kernel-entrypoint.s, kernel.c   # kernel entry and initialisation
├── gdt.c, idt.c, interrupt.c       # descriptor tables, interrupts, syscalls
├── framebuffer.c, keyboard.c       # text output and keyboard input
├── disk.c, fat32.c                 # IDE disk driver and FAT32 filesystem
├── paging.c                        # memory management
├── process.c, scheduler.c/.s       # processes, scheduling, context switch
├── cmos_driver.c                   # real-time clock
├── user-shell.c, clock.c, crt0.s   # user-space programs
├── external-inserter.c             # host tool that writes programs into the disk image
└── header/                         # headers, grouped by subsystem
other/grub1                         # GRUB bootloader used to build the ISO
```

## Technologies Used
1. Windows Subsystem for Linux
2. Netwide Assembler (NASM)
3. GNU C Compiler
4. GNU Linker
5. QEMU – System i386
6. GNU Make
7. genisoimage
8. GDB
9. Visual Studio Code

## Contributors

| No. | Name | NIM | GitHub |
| --- | --- | --- | --- |
| 1 | Maulana Muhamad Susetyo | 13522127 | [@LastPrism7](https://github.com/LastPrism7) |
| 2 | Hugo Sabam Augusto | 13522129 | [@miannetopokki](https://github.com/miannetopokki) |
| 3 | Muhammad Dzaki Arta | 13522149 | [@TuanOnta](https://github.com/TuanOnta) |
| 4 | Albert Ghazaly | 13522150 | [@AlbertGhazaly](https://github.com/AlbertGhazaly) |

## Acknowledgements
1. God Almighty (Tuhan Yang Maha Esa)
2. Operating Systems course lecturers
3. LabSister teaching assistants (+ pol)
