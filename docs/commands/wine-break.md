## Command `wine-break`

The `wine-break` (alias `wstart`) command runs a Windows PE executable under
Wine and stops on its entry point with full source-level symbols, much like
`entry-break` does for native ELF binaries.

```text
wine-break EXECUTABLE.exe [ARGS ...]
```

It performs the following actions:

1.  Locate an ELF Wine loader (`wine64` / `wine`) — distro shell wrappers are
    skipped automatically — and `file` it.
2.  Arm a pending breakpoint on `signal_start_thread`, the exported `ntdll.so`
    symbol that fires once the target PE image is guaranteed to be mapped in
    the process. No Wine debug build is required.
3.  When hit, scan the process maps for the PE, compute its load slide, and
    register its symbol file (`add-symbol-file -o <slide>`).
4.  Set a temporary breakpoint on `main` if the PE exports it, or on the raw
    COFF `AddressOfEntryPoint` otherwise, and continue.
5.  On the final stop, run [`wine-symbols`](./wine-symbols.md) so the Wine
    builtin DLLs (`kernel32.dll`, `ntdll.dll`, …) also resolve in backtraces.

Settings:

| Setting       | Default               | Description                                  |
|---------------|-----------------------|----------------------------------------------|
| `wine_loader` | autodetect            | Path to the ELF Wine loader binary           |
| `sync_symbol` | `signal_start_thread` | Exported `ntdll.so` symbol used as sync point |
| `break_main`  | `True`                | Prefer `main` over the raw COFF entry        |

Tested on Wine 9.x; should work on any Wine 6.x+ where `ntdll.so` exports
`signal_start_thread`. For older builds, override `sync_symbol`.
