## Command `wine-symbols`

`wine-symbols` walks the current process maps, identifies every memory region
backed by an on-disk PE/COFF image (the target executable and Wine builtin
DLLs), computes each image's load slide against its preferred `ImageBase`,
and registers the file with GDB via `add-symbol-file -o <slide>`. Images that
are already known to GDB are skipped, so the command is idempotent.

This is invoked automatically by [`wine-break`](./wine-break.md), but is also
useful standalone when attaching to an already-running Wine process, or after
a late `LoadLibrary` to pick up the newly mapped DLL.

```text
gef➤  wine-symbols
[+] msvcrt.dll                       @ 0x00006ffffefc0000 (slide=0x6ffe7efc0000)
[+] kernelbase.dll                   @ 0x00006fffff470000 (slide=0x6ffe8b470000)
[+] kernel32.dll                     @ 0x00006fffffa80000 (slide=0x6ffe87a80000)
[+] ntdll.dll                        @ 0x00006fffffc50000 (slide=0x6ffe7fc50000)
[+] 4 PE image(s) registered
```
