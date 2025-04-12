---
title: Start kernel and rootfs within qemu
date: 2025-04-12 20:26:41
tags: [kernel, rootfs, qemu]
categories:
---

## 1 Fedora gcc qemu kernel busybox 版本

```
❯ fastfetch
             .',;::::;,'.                 zyuan@matebook
         .';:cccccccccccc:;,.             --------------
      .;cccccccccccccccccccccc;.          OS: Fedora Linux 41 (Workstation Edition) x86_64
    .:cccccccccccccccccccccccccc:.        Host: MRG-WXX (M1010)
  .;ccccccccccccc;.:dddl:.;ccccccc;.      Kernel: Linux 6.13.10-200.fc41.x86_64
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.     Uptime: 6 hours, 56 mins
.:ccccccccccccc;KMMc;cc;xMMc;ccccccc:.    Packages: 2258 (rpm), 15 (flatpak)
,cccccccccccccc;MMM.;cc;;WW:;cccccccc,    Shell: bash 5.2.32
:cccccccccccccc;MMM.;cccccccccccccccc:    Display (CSO1408): 3120x2080 @ 90 Hz (as 1782x1188) in 14" [Built-in]
:ccccccc;oxOOOo;MMM000k.;cccccccccccc:    DE: GNOME 47.5
cccccc;0MMKxdd:;MMMkddc.;cccccccccccc;    WM: Mutter (Wayland)
ccccc;XMO';cccc;MMM.;cccccccccccccccc'    WM Theme: Marble-GnomeShell-blue-dark
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;     Theme: Adwaita-GTK3 [GTK2/3/4]
ccccc;0MNc.ccc.xMMd;ccccccccccccccc;      Icons: Flat-Remix-Blue-Light [GTK2/3/4]
cccccc;dNMWXXXWM0:;cccccccccccccc:,       Font: Inter (12pt) [GTK2/3/4]
cccccccc;.:odl:.;cccccccccccccc:,.        Cursor: Adwaita (24px)
ccccccccccccccccccccccccccccc:'.          Terminal: GNOME Terminal 3.54.4
:ccccccccccccccccccccccc:;,..             Terminal Font: FiraCode Nerd Font (12pt)
 ':cccccccccccccccc::;,.                  CPU: 11th Gen Intel(R) Core(TM) i5-1155G7 (8) @ 4.50 GHz
                                          GPU: Intel Iris Xe Graphics @ 1.35 GHz [Integrated]
                                          Memory: 5.78 GiB / 15.39 GiB (38%)
                                          Swap: 662.04 MiB / 8.00 GiB (8%)
                                          Disk (/): 102.50 GiB / 445.54 GiB (23%) - btrfs
                                          Local IP (wlp0s20f3): 192.168.31.86/24
                                          Battery (AP16L5J): 99% [AC Connected]
                                          Locale: en_US.UTF-8




~
❯ gcc -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/libexec/gcc/x86_64-redhat-linux/14/lto-wrapper
OFFLOAD_TARGET_NAMES=nvptx-none:amdgcn-amdhsa
OFFLOAD_TARGET_DEFAULT=1
Target: x86_64-redhat-linux
Configured with: ../configure --enable-bootstrap --enable-languages=c,c++,fortran,objc,obj-c++,ada,go,d,m2,lto --prefix=/usr --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-shared --enable-threads=posix --enable-checking=release --enable-multilib --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-linker-build-id --with-gcc-major-version-only --enable-libstdcxx-backtrace --with-libstdcxx-zoneinfo=/usr/share/zoneinfo --with-linker-hash-style=gnu --enable-plugin --enable-initfini-array --with-isl=/builddir/build/BUILD/gcc-14.2.1-build/gcc-14.2.1-20250110/obj-x86_64-redhat-linux/isl-install --enable-offload-targets=nvptx-none,amdgcn-amdhsa --enable-offload-defaulted --without-cuda-driver --enable-gnu-indirect-function --enable-cet --with-tune=generic --with-arch_32=i686 --build=x86_64-redhat-linux --with-build-config=bootstrap-lto --enable-link-serialization=1
Thread model: posix
Supported LTO compression algorithms: zlib zstd
gcc version 14.2.1 20250110 (Red Hat 14.2.1-7) (GCC)

❯ qemu-system-x86_64 --version
QEMU emulator version 9.1.3 (qemu-9.1.3-2.fc41)
Copyright (c) 2003-2024 Fabrice Bellard and the QEMU Project developers

~/mywork/kernel
❯ ls
busybox-1_37_0  linux-5.15.180  qemu  rootfs

```
## 2 目录安排

```
~/mywork/kernel
❯ tree -L 2
.
├── busybox-1_37_0

├── linux-5.15.180
├── qemu
│   ├── bzImage
│   └── initramfs.cpio.gz
└── rootfs
    ├── bin
    ├── init
    ├── linuxrc -> bin/busybox
    ├── sbin
    └── usr

65 directories, 43 files

```

## 3 编译 kernel

```
make mrproper   # 清理
make menuconfig # 没有特殊的配置，可以去掉声卡的驱动支持
make -j4        # 4线程编译
                # 成功后， 拷贝 arch/x86/boot/bzImage 到 qemu 文件夹
```

## 4 编辑 busybox

编辑成静态的bosybox，去掉 tc.c 的文件，这个文件不能和大于 6.8 的 kernel 一同编译。

```
Linux kernel 6.8 removed a number of traffic control related symbols from
include/uapi/linux/pkt_sched.h [1]. When using kernel 6.8 headers, compilation
of tc.c fails with:

networking/tc.c: In function ‘cbq_print_opt’:
networking/tc.c:236:27: error: ‘TCA_CBQ_MAX’ undeclared (first use in this
function); did you mean ‘TCA_CBS_MAX’?
  236 |         struct rtattr *tb[TCA_CBQ_MAX+1];
      |                           ^~~~~~~~~~~
      |                           TCA_CBS_MAX

[1] https://lore.kernel.org/all/20231223140154.1319084-1-jhs@mojatatu.com/T/
```

![](excludes_tc_file.png)

![](busybox_static.png)

```
make mrproper
make menuconfig
make
make install
```

拷贝 `_install` 下的全部文件到 `rootfs` 下面

## 5 生成 rootfs

rootfs 文件下新建 init 文件，写入内容：

```
#!/bin/busybox sh

/bin/busybox mkdir -p /proc && /bin/busybox mount -t proc none /proc

/bin/busybox echo "Hello world!"

export PS1='(kernle) =>'

/bin/busybox sh
```

```
chmod +x init
```

```
mywork/kernel/rootfs
❯ tree -L 1
.
├── bin
├── init
├── linuxrc -> bin/busybox
├── sbin
└── usr

4 directories, 2 files

mywork/kernel/rootfs
❯ find . -print0 | cpio -ov --null --format=newc | gzip -9 > ../qemu/initramfs.cpio.gz
```

## 6 启动 qemu

```
mywork/kernel/qemu
❯ tree
.
├── bzImage
└── initramfs.cpio.gz

1 directory, 2 files
```

```
mywork/kernel/qemu
❯ qemu-system-x86_64 -kernel ./bzImage -initrd ./initramfs.cpio.gz -m 1G -append "console=ttyS0" -nographic
```

```
[    0.000000] Linux version 5.15.180 (zyuan@matebook) (gcc (GCC) 14.2.1 20250110 (Red Hat 14.2.1-7), GNU ld version 2.43.1-5.fc41) #2 SMP Sat Apr 12 15:43:13 CST 2025
[    0.000000] Command line: console=ttyS0
[    0.000000] BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000f0000-0x00000000000fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x000000003ffdffff] usable
[    0.000000] BIOS-e820: [mem 0x000000003ffe0000-0x000000003fffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fffc0000-0x00000000ffffffff] reserved
[    0.000000] BIOS-e820: [mem 0x000000fd00000000-0x000000ffffffffff] reserved
[    0.000000] NX (Execute Disable) protection: active
[    0.000000] SMBIOS 2.8 present.
......
[    2.055352] PM:   Magic number: 5:56:837
[    2.056260] RAS: Correctable Errors collector initialized.
[    2.057524] clk: Disabling unused clocks
[    2.119959] ata2.00: ATAPI: QEMU DVD-ROM, 2.5+, max UDMA/100
[    2.133881] scsi 1:0:0:0: CD-ROM            QEMU     QEMU DVD-ROM     2.5+ PQ: 0 ANSI: 5
[    2.154184] sr 1:0:0:0: [sr0] scsi3-mmc drive: 4x/4x cd/rw xa/form2 tray
[    2.154589] cdrom: Uniform CD-ROM driver Revision: 3.20
[    2.175824] sr 1:0:0:0: Attached scsi generic sg0 type 5
[    2.185060] Freeing unused decrypted memory: 2036K
[    2.244070] Freeing unused kernel image (initmem) memory: 3208K
[    2.244465] Write protecting the kernel read-only data: 34816k
[    2.246691] Freeing unused kernel image (text/rodata gap) memory: 2036K
[    2.247947] Freeing unused kernel image (rodata/data gap) memory: 1860K
[    2.445405] x86/mm: Checked W+X mappings: passed, no W+X pages found.
[    2.445809] Run /init as init process
Hello world!
sh: can't access tty; job control turned off
(kernle) =>[    2.794868] tsc: Refined TSC clocksource calibration: 2495.967 MHz
[    2.795611] clocksource: tsc: mask: 0xffffffffffffffff max_cycles: 0x23fa57e3ae7, max_idle_ns: 440795241954 ns
[    2.796305] clocksource: Switched to clocksource tsc
[    3.092059] input: ImExPS/2 Generic Explorer Mouse as /devices/platform/i8042/serio1/input/input3

(kernle) =>
(kernle) =>
(kernle) =>ps
PID   USER     TIME  COMMAND
    1 0         0:00 {init} /bin/busybox sh /init
    2 0         0:00 [kthreadd]
    3 0         0:00 [rcu_gp]
    4 0         0:00 [rcu_par_gp]
    5 0         0:00 [slub_flushwq]
    6 0         0:00 [netns]
    7 0         0:00 [kworker/0:0-ata]
    8 0         0:00 [kworker/0:0H-ev]
    9 0         0:00 [kworker/u2:0-ev]
   10 0         0:00 [mm_percpu_wq]
   11 0         0:00 [kworker/u2:1-ev]
   12 0         0:00 [rcu_tasks_kthre]
   13 0         0:00 [rcu_tasks_rude_]
   14 0         0:00 [rcu_tasks_trace]
   15 0         0:00 [ksoftirqd/0]
```

-nographic does the same as "-serial stdio" and also hides a QEMU's graphical window.

-append "console=ttyS0": console=ttyS0 forces the guest kernel to send output to the first UART serial port ttyS0, which is redirected to the host by the -serial stdio option.


## 参考文献

1. [How to Setup QEMU Output to Console and Automate Using Shell Script](https://fadeevab.com/how-to-setup-qemu-output-to-console-and-automate-using-shell-script/)

2. [Linux Kernel 从编译内核、制作 initramfs 到使用 QEMU 运行内核](https://www.bilibili.com/video/BV1Kd4y1R7tV?spm_id_from=333.788.videopod.sections&vd_source=572192679744be9d67bc1379425716a7)
