# myos-vim

Vim port for MyOS.

## Purpose

`myos-vim` tracks the work required to build, package and run the upstream Vim editor as a native **`x86_64-myos`** userspace program.

Vim is an important integration target for MyOS because a usable terminal editor exercises a broad cross-section of the operating-system interface: libc, filesystems, terminal control, process execution, signals, timing, environment handling, floating point and other Unix-style facilities.

The long-term milestone is straightforward:

```text
MyOS $ vim hello.c
```

and ultimately using Vim while developing MyOS from within MyOS itself.

## Porting boundary

```text
upstream Vim source
        |
        v
 myos-vim build/patch layer
        |
        v
   myos-libc + sysroot
        |
        v
 public x86_64-myos ABI
        |
        v
      MyOS
```

Vim is a **consumer of the platform**, not a reason to special-case the kernel.

If the port exposes a missing capability:

1. identify the required portable behavior;
2. assign it to libc, the syscall ABI, VFS, TTY, process, signal, time or another owning subsystem;
3. implement and regression-test the general capability there;
4. rebuild Vim against the public `x86_64-myos` platform.

No Vim-specific kernel syscall or private kernel interface is acceptable.

## Upstream

The reference implementation is the official Vim repository:

- https://github.com/vim/vim

The exact upstream release/tag/commit used by the port will be pinned when implementation begins.

The initial target is a **terminal Vim** with a deliberately constrained feature profile. GUI/X integration, embedded `:terminal`, language interpreters and other optional subsystems can be enabled later when the corresponding MyOS capabilities exist.

## Expected platform requirements

The port is expected to exercise, among other things:

- ISO C library functionality;
- filesystem and pathname operations;
- directories and file metadata;
- safe file replacement, temporary files, swap and recovery;
- environment and user/home information;
- TTY and termios behavior;
- terminal-size ioctls;
- termcap/terminal capability handling;
- signals and process groups;
- `fork`/`exec`/`waitpid`;
- pipes and descriptor duplication;
- time and sleep APIs;
- `select`/readiness behavior;
- locale, multibyte and UTF-8 support;
- floating-point context management and mathematical functions.

The detailed capability work is tracked from the MyOS roadmap rather than being hidden inside the port.

## Repository scope

This repository is expected to contain the MyOS-specific integration needed around upstream Vim, such as:

- pinned upstream version metadata;
- reproducible cross-build configuration;
- MyOS-specific build-system patches where unavoidable;
- feature manifests;
- packaging/install rules;
- smoke/integration tests;
- documentation of required MyOS capabilities.

It should avoid carrying unnecessary forks of upstream code. Any source modifications should remain small, reviewable and suitable for upstreaming where practical.

## Related projects

- [crecabar/myos](https://github.com/crecabar/myos) — kernel and platform.
- [crecabar/myos-libc](https://github.com/crecabar/myos-libc) — C library/runtime used by the port.
- [crecabar/myos-userland](https://github.com/crecabar/myos-userland) — first-party MyOS userspace.
- [Vim on MyOS epic #257](https://github.com/crecabar/myos/issues/257) — roadmap and platform-integration tracking.

## Status

Planning/bootstrap stage. The port will become buildable as the public MyOS libc, TTY, filesystem, process and related userspace interfaces mature.

## Licensing

Original code, build integration and patches authored specifically for this repository are licensed under the **GNU General Public License version 2 only (GPL-2.0-only)** unless a file states otherwise.

Upstream Vim source code is third-party software and retains its own upstream license and copyright notices. It is **not relicensed** by this repository.

See [LICENSE](LICENSE) for the license covering original `myos-vim` project material.
