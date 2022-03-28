# crossdrobo
A gcc-based toolchain targeting the Drobo NAS

# Goal

This repository contains a preliminary version of a GCC-based toolchain able to target the Drobo 5N.

This is intended for all the Drobo owners and enthusiasts who want to be able to run their software on the Drobo.

For now, the toolchain is built for Apple Mac OS 11.x x86_64.

I could probably generate additional toolchains for other platforms, but I have three children...


# Install

Download [crossdrobo-x86_64-apple-darwin20.3.0-v0.1.tgz](crossdrobo-x86_64-apple-darwin20.3.0-v0.1.tgz)

Unpack the tarball:

```
export PREFIX=/opt/local/crossdrobo
sudo mkdir $PREFIX
sudo chown $USER $PREFIX
tar xvf crossdrobo-x86_64-apple-darwin20.3.0-v0.1.tgz  -C /
chmod a-w -R $PREFIX
```

# Test

Use file [hello.c](hello.c) as a trivial test input.

```
export PATH=$PATH:$PREFIX/bin
export SYS=/opt/local/crossdrobo
armv7l-unknown-linux-gnueabi-gcc hello.c --sysroot $SYS -L$SYS/lib -l:libc-2.25.so -o hello
```
This should complete successfully.

If you inspect the output executable with  `armv7l-unknown-linux-gnueabi-readelf -h ./hello`  you should see an answer confirming it's a 32-bit ELF for the ARM processor:

```
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           ARM
  Version:                           0x1
  Entry point address:               0x1030d
  Start of program headers:          52 (bytes into file)
  Start of section headers:          6836 (bytes into file)
  Flags:                             0x5000200, Version5 EABI, soft-float ABI
  Size of this header:               52 (bytes)
  Size of program headers:           32 (bytes)
  Number of program headers:         9
  Size of section headers:           40 (bytes)
  Number of section headers:         29
  Section header string table index: 28
```

Finally, run the executable by copying to a share (e.g., called "Incoming" already mounted at `/Volumes/Incoming`) and running it!

```
macbook:crossdrobo scarpaz$ cp hello /Volumes/Incoming/

macbook:crossdrobo scarpaz$ ssh mydrobo

ScarpazDrobo:~ $ cd /mnt/DroboFS/Shares/Incoming/

ScarpazDrobo:~/Shares/Incoming $ ./hello

Hello, world!
```

Yay!
