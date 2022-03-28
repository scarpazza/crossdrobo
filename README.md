# crossdrobo
A gcc-based toolchain targeting the Drobo NAS

## Goal

This repository contains a preliminary version of a GCC-based toolchain able to target the Drobo 5N.

This is intended for all the Drobo owners and enthusiasts who want to be able to run their software on the Drobo.

For now, the toolchain is built for Apple Mac OS 11.x x86_64 and targets the Drobo 5N with the exact software version I have,
I could probably generate additional toolchains for other platforms, but I have three children...


## Disclaimer
This toolchain allows you build software that you can run on your NAS with little to no protection for the NAS contents.
As a result, using this toolchain you can quite easily build and run software that can destroy your NAS contents.
I offer zero warranties. You are on your own. Don't sue me. 
For the complete avoidance of doubt, this legal disclaimer intends to clarify that you have no legal grounds to hold me liable for 
any losses of any kind you incur as a result of you using this software.
I'm giving you a pair of sharp scissors. Don't run down the stairs while holding them.

## Installation

Download [crossdrobo-x86_64-apple-darwin20.3.0-v0.1.tgz](https://github.com/scarpazza/crossdrobo/raw/main/crossdrobo-x86_64-apple-darwin20.3.0-v0.1.tgz)

Unpack the tarball:

```
export PREFIX=/opt/local/crossdrobo
sudo mkdir $PREFIX
sudo chown $USER $PREFIX
tar xvf crossdrobo-x86_64-apple-darwin20.3.0-v0.1.tgz  -C /
chmod a-w  -R $PREFIX
chmod a+rX -R $PREFIX
```

## Test

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


## Details

This toolchain was built with:
- gcc 7.2.0
- binutils 2.34
- linux kernel headers 3.2.96
- glibc 2.25


## Drobo Environment

This toolchain matches accurately the software I found on my Drobo 5N,
which runs Linux 3.2.96:
 
 
 ```
$ uname -a
Linux ScarpazDrobo 3.2.96-3 #1 SMP Tue May 21 21:58:06 UTC 2019 armv7l GNU/Linux
```

... and hosts a 3-core Marvell Armada:

```
ScarpazDrobo:~ $ cat /proc/cpuinfo
Processor	: Marvell PJ4Bv7 Processor rev 2 (v7l)
processor	: 0
BogoMIPS	: 1196.85

processor	: 1
BogoMIPS	: 1196.85

processor	: 2
BogoMIPS	: 1196.85

Features	: swp half thumb fastmult vfp edsp vfpv3 tls
CPU implementer	: 0x56
CPU architecture: 7
CPU variant	: 0x2
CPU part	: 0x584
CPU revision	: 2

Hardware	: Marvell Armada XP Development Board
Revision	: 0000
Serial		: 0000000000000000
```

According to [OpenBanchmarking](https://openbenchmarking.org/s/Marvell%20PJ4Bv7%20rev%202) is a quad-core, little endian Arm 7 CPU:

```
Architecture:          armv7l
Byte Order:            Little Endian
CPU(s):                4
On-line CPU(s) list:   0-3
Thread(s) per core:    1
Core(s) per socket:    4
Socket(s):             1
```

