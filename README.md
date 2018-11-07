lddtree
=======

Fork of pax-utils' lddtree.sh

This is a shell version of pax-utils' lddtree. This tool is useful for
resolving elf dependencies when creating initramfs images.

Differences from pax-utils' bash version:
* don't use /bin/bash
* resolv symlinks
* fall back to objdump and readelf if scanelf is not found

lddtree.sh depends on scanelf from pax-utils or objdump and readelf from
binutils.

```
Usage: lddtree.sh [options] <ELF file[s]>

Options:
  -a              Show all duplicated dependencies
  -x              Run with debugging
  -R <root>       Use this ROOT filesystem tree
  -i <key_words>  Stop recursively find dependency once the library full path match one of keywords which split with blank
  --no-auto-root  Do not automatically prefix input ELFs with ROOT
  -l              Display output in a flat format
  -h              Show this help output
  -V              Show version information

```

add -i option to stop recursively find dependency once the library full path match one of keywords which split with blank
for example without -i:
```
zhoub@zhoub-PC:~$ ./lddtree.sh -a NVIDIA_CUDA-9.0_Samples/0_Simple/simpleTexture/simpleTexture
simpleTexture => NVIDIA_CUDA-9.0_Samples/0_Simple/simpleTexture/simpleTexture (interpreter => /lib64/ld-linux-x86-64.so.2)
    librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1
        libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0
            libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
                ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
        ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
        ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
    libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6
            libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
                ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
        ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
        libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1
            libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
                ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
    libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
        ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
```
with -i
```
zhoub@zhoub-PC:~$ ./lddtree.sh -a -i "librt.so libpthread.so libstdc++.so" NVIDIA_CUDA-9.0_Samples/0_Simple/simpleTexture/simpleTexture
simpleTexture => NVIDIA_CUDA-9.0_Samples/0_Simple/simpleTexture/simpleTexture (interpreter => /lib64/ld-linux-x86-64.so.2)
    librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 ...
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 ...
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
        ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
    libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 ...
    libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
            ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
        ld-linux-x86-64.so.2 => /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2

```
