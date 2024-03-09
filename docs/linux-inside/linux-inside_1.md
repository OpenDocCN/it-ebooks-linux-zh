# 引导

# 内核引导过程

本章介绍了 Linux 内核引导过程。你将在这看到一些描述内核加载过程的整个周期的相关文章：

*   [从引导加载程序内核](http://xinqiu.gitbooks.io/linux-inside-zh/content/Booting/index.html/linux-bootstrap-1.html) - 介绍了从启动计算机到内核执行第一条指令之前的所有阶段;
*   [在内核安装代码的第一步](http://xinqiu.gitbooks.io/linux-inside-zh/content/Booting/linux-bootstrap-2.html) - 介绍了在内核设置代码的第一个步骤。你会看到堆的初始化，查询不同的参数，如 EDD，IST 和等...
*   [视频模式初始化和转换到保护模式](http://xinqiu.gitbooks.io/linux-inside-zh/content/Booting/linux-bootstrap-3.html) - 介绍了视频模式初始化内核设置代码并过渡到保护模式。
*   [过渡到 64 位模式](http://xinqiu.gitbooks.io/linux-inside-zh/content/Booting/linux-bootstrap-4.html) - 介绍了过渡到 64 位模式的准备并过渡到 64 位。
*   [内核解压缩](http://xinqiu.gitbooks.io/linux-inside-zh/content/Booting/linux-bootstrap-5.html) - 介绍了内核解压缩之前的准备然后直接解压缩。

# 从引导加载程序内核

# 内核引导过程. Part 1.

## 从引导加载程序内核

如果你已经看过我之前的[文章](http://0xax.blogspot.com/search/label/asm)，就知道之前我开始和底层编程打交道。我写了一些关于 Linux x86_64 汇编的文章。同时，我开始深入研究 Linux 源代码。底层是如果工作的，程序是如何在电脑上运行的，他们是如何在内存中定位的，内核是如何管理进程和内存，网络堆栈是如何在底层工作的等等，这些我都非常感兴趣。因此，我决定去写另外的一系列文章关于**x86_64**框架的 Linux 内核。

值得注意的是我不是一个专业的内核黑客并且我的工作不是为内核贡献代码。这只是小兴趣。我只是喜欢底层的东西，底层是如何工作的让我产生了很大的兴趣。如果你发现任何迷惑的地方或者你有任何问题/备注，[twitter](https://twitter.com/0xAX)，email 我或者提一个[issue](https://github.com/0xAX/linux-insides).(PS:翻译上的问题请 mail 我:xinqiu.94@gmail.com 或 github 上@xinqiu)。我会很高兴。所有的文章也可以在[linux-insides](https://github.com/0xAX/linux-insides)上看，如果你发现哪里英文或内容错误，随意提个 PR。(PS:中文版地址：[`github.com/xinqiu/linux-insides`](https://github.com/xinqiu/linux-insides))

*注意这不是官方文档，只是学习和分享知识*

**需要的基础知识**

*   理解 C 代码
*   理解 汇编语言 代码 （AT&T 语法）

不管怎样，如果你才开始学一些，我会在这些文章中尝试去解释一些部分。好了，小的介绍结束，我们开始深入内核和底层。

所有的代码实际上是内核 - 3.18.如果有任何改变，我将会做相应的更新。

## 神奇的电源按钮，接下来会发生什么？

尽管这一系列文章关于 Linux 内核，我们还没有从内核代码（至少在这一章）开始。好了，当你按下你笔记本或台式机的神奇电源按钮，它开始工作。在主板发送一个信号给[电源](https://en.wikipedia.org/wiki/Power_supply)，电源提供电脑适当量的电力。一旦主板收到了[电源备妥信号](https://en.wikipedia.org/wiki/Power_good_signal),它会尝试运行 CPU 。CPU 复位寄存器里的所有剩余数据，设置预定义的值给每个寄存器。

[80386](https://en.wikipedia.org/wiki/Intel_80386) 以及后来的 CPUs 在电脑复位后，在 CPU 寄存器中定义了如下预定义数据：

```
IP          0xfff0
CS selector 0xf000
CS base     0xffff0000 
```

处理器开始在[实模式](https://en.wikipedia.org/wiki/Real_mode)工作，我们需要退回一点去理解在这种模式下的内存分割。所有 x86 兼容处理器都支持实模式，从[8086](https://en.wikipedia.org/wiki/Intel_8086)到现在的 Intel 64 位 CPU。8086 处理器有一个 20 位寻址总线，这意味着它可以对 0 到 2²⁰ 位地址空间进行操作(1Mb).不过它只有 16 位的寄存器，通过这个 16 位寄存器最大寻址是 2¹⁶ 即 0xffff(64 Kb)。[内存分配](http://en.wikipedia.org/wiki/Memory_segmentation) 被用来充分利用所有空闲地址空间。所有内存被分成固定的 65535 字节或 64 KB 大小的小块。由于我们不能用 16 位寄存器寻址小于 64KB 的内存，一种替代的方法被设计出来了。一个地址包括两个部分：数据段起始地址和从该数据段起的偏移量。为了得到内存中的物理地址，我们要让数据段乘 16 并加上偏移量：

```
PhysicalAddress = Segment * 16 + Offset 
```

举个例子，如果 `CS:IP` 是 `0x2000:0x0010`, 相关的物理地址将会是：

```
>>> hex((0x2000 << 4) + 0x0010)
'0x20010' 
```

不过如果我们让最大端进行偏移：`0xffff:0xffff`，将会是：

```
>>> hex((0xffff << 4) + 0xffff)
'0x10ffef' 
```

这超出 1MB65519 字节。既然只有 1MB 在实模式中可以访问，`0x10ffef` 变成有[A20](https://en.wikipedia.org/wiki/A20_line)缺陷的 `0x00ffef`。

我们知道实模式和内存地址。回到复位后的寄存器值。

`CS` register consists of two parts: the visible segment selector and hidden base address. We know predefined `CS` base and `IP` value, logical address will be:

```
0xffff0000:0xfff0 
```

In this way starting address formed by adding the base address to the value in the EIP register:

```
>>> 0xffff0000 + 0xfff0
'0xfffffff0' 
```

We get `0xfffffff0` which is 4GB - 16 bytes. This point is the [Reset vector](http://en.wikipedia.org/wiki/Reset_vector). This is the memory location at which CPU expects to find the first instruction to execute after reset. It contains a [jump](http://en.wikipedia.org/wiki/JMP_%28x86_instruction%29) instruction which usually points to the BIOS entry point. For example, if we look in [coreboot](http://www.coreboot.org/) source code, we will see it:

```
 .section ".reset"
    .code16
.globl    reset_vector
reset_vector:
    .byte  0xe9
    .int   _start - ( . + 2 )
    ... 
```

We can see here the jump instruction [opcode](http://ref.x86asm.net/coder32.html#xE9) - 0xe9 to the address `_start - ( . + 2)`. And we can see that `reset` section is 16 bytes and starts at `0xfffffff0`:

```
SECTIONS {
    _ROMTOP = 0xfffffff0;
    . = _ROMTOP;
    .reset . : {
        *(.reset)
        . = 15 ;
        BYTE(0x00);
    }
} 
```

Now the BIOS has started to work. After initializing and checking the hardware, it needs to find a bootable device. A boot order is stored in the BIOS configuration. The function of boot order is to control which devices the kernel attempts to boot. In the case of attempting to boot a hard drive, the BIOS tries to find a boot sector. On hard drives partitioned with an MBR partition layout, the boot sector is stored in the first 446 bytes of the first sector (512 bytes). The final two bytes of the first sector are `0x55` and `0xaa` which signals the BIOS that the device is bootable. For example:

```
;
; Note: this example is written in Intel Assembly syntax
;
[BITS 16]
[ORG  0x7c00]

boot:
    mov al, '!'
    mov ah, 0x0e
    mov bh, 0x00
    mov bl, 0x07

    int 0x10
    jmp $

times 510-($-$$) db 0

db 0x55
db 0xaa 
```

Build and run it with:

```
nasm -f bin boot.nasm && qemu-system-x86_64 boot 
```

This will instruct [QEMU](http://qemu.org) to use the `boot` binary we just built as a disk image. Since the binary generated by the assembly code above fulfills the requirements of the boot sector (the origin is set to `0x7c00`, and we end with the magic sequence). QEMU will treat the binary as the master boot record(MBR) of a disk image.

We will see:

![Simple bootloader which prints only `!`](img/37014685.jpg)

In this example we can see that this code will be executed in 16 bit real mode and will start at 0x7c00 in memory. After the start it calls the [0x10](http://www.ctyme.com/intr/rb-0106.htm) interrupt which just prints `!` symbol. It fills rest of 510 bytes with zeros and finish with two magic bytes `0xaa` and `0x55`.

You can see binary dump of it with `objdump` util:

```
nasm -f bin boot.nasm
objdump -D -b binary -mi386 -Maddr16,data16,intel boot 
```

A real-world boot sector has code for continuing the boot process and the partition table instead of a bunch of 0's and an exclamation point :) Ok so, from this point onwards BIOS hands over the control to the bootloader and we can go ahead.

**NOTE**: As you can read above the CPU is in real mode. In real mode, calculating the physical address in memory is done as following:

```
PhysicalAddress = Segment * 16 + Offset 
```

Same as I mentioned before. But we have only 16 bit general purpose registers. The maximum value of 16 bit register is: `0xffff`; So if we take the biggest values the result will be:

```
>>> hex((0xffff * 16) + 0xffff)
'0x10ffef' 
```

Where `0x10ffef` is equal to `1MB + 64KB - 16b`. But a [8086](https://en.wikipedia.org/wiki/Intel_8086) processor, which was the first processor with real mode. It had 20 bit address line and `2²⁰ = 1048576.0` is 1MB. So, it means that the actual memory available is 1MB.

General real mode's memory map is:

```
0x00000000 - 0x000003FF - Real Mode Interrupt Vector Table
0x00000400 - 0x000004FF - BIOS Data Area
0x00000500 - 0x00007BFF - Unused
0x00007C00 - 0x00007DFF - Our Bootloader
0x00007E00 - 0x0009FFFF - Unused
0x000A0000 - 0x000BFFFF - Video RAM (VRAM) Memory
0x000B0000 - 0x000B7777 - Monochrome Video Memory
0x000B8000 - 0x000BFFFF - Color Video Memory
0x000C0000 - 0x000C7FFF - Video ROM BIOS
0x000C8000 - 0x000EFFFF - BIOS Shadow Area
0x000F0000 - 0x000FFFFF - System BIOS 
```

But stop, at the beginning of post I wrote that first instruction executed by the CPU is located at the address `0xFFFFFFF0`, which is much bigger than `0xFFFFF` (1MB). How can CPU access it in real mode? As I write about it and you can read in [coreboot](http://www.coreboot.org/Developer_Manual/Memory_map) documentation:

```
0xFFFE_0000 - 0xFFFF_FFFF: 128 kilobyte ROM mapped into address space 
```

At the start of execution BIOS is not in RAM, it is located in the ROM.

## Bootloader

There are a number of bootloaders which can boot Linux, such as [GRUB 2](https://www.gnu.org/software/grub/) and [syslinux](http://www.syslinux.org/wiki/index.php/The_Syslinux_Project). The Linux kernel has a [Boot protocol](https://github.com/torvalds/linux/blob/master/Documentation/x86/boot.txt) which specifies the requirements for bootloaders to implement Linux support. This example will describe GRUB 2.

Now that the BIOS has chosen a boot device and transferred control to the boot sector code, execution starts from [boot.img](http://git.savannah.gnu.org/gitweb/?p=grub.git;a=blob;f=grub-core/boot/i386/pc/boot.S;hb=HEAD). This code is very simple due to the limited amount of space available, and contains a pointer that it uses to jump to the location of GRUB 2's core image. The core image begins with [diskboot.img](http://git.savannah.gnu.org/gitweb/?p=grub.git;a=blob;f=grub-core/boot/i386/pc/diskboot.S;hb=HEAD), which is usually stored immediately after the first sector in the unused space before the first partition. The above code loads the rest of the core image into memory, which contains GRUB 2's kernel and drivers for handling filesystems. After loading the rest of the core image, it executes [grub_main](http://git.savannah.gnu.org/gitweb/?p=grub.git;a=blob;f=grub-core/kern/main.c).

`grub_main` initializes console, gets base address for modules, sets root device, loads/parses grub configuration file, loads modules etc. At the end of execution, `grub_main` moves grub to normal mode. `grub_normal_execute` (from `grub-core/normal/main.c`) completes last preparation and shows a menu for selecting an operating system. When we select one of grub menu entries, `grub_menu_execute_entry` begins to be executed, which executes grub `boot` command. It starts to boot the selected operating system.

As we can read in the kernel boot protocol, the bootloader must read and fill some fields of kernel setup header which starts at `0x01f1` offset from the kernel setup code. Kernel header [arch/x86/boot/header.S](https://github.com/torvalds/linux/blob/master/arch/x86/boot/header.S) starts from:

```
 .globl hdr
hdr:
    setup_sects: .byte 0
    root_flags:  .word ROOT_RDONLY
    syssize:     .long 0
    ram_size:    .word 0
    vid_mode:    .word SVGA_MODE
    root_dev:    .word 0
    boot_flag:   .word 0xAA55 
```

The bootloader must fill this and the rest of the headers (only marked as `write` in the Linux boot protocol, for example [this](https://github.com/torvalds/linux/blob/master/Documentation/x86/boot.txt#L354)) with values which it either got from command line or calculated. We will not see description and explanation of all fields of kernel setup header, we will get back to it when kernel uses it. Anyway, you can find description of any field in the [boot protocol](https://github.com/torvalds/linux/blob/master/Documentation/x86/boot.txt#L156).

As we can see in kernel boot protocol, the memory map will be the following after kernel loading:

```
 | Protected-mode kernel  |
100000   +------------------------+
         | I/O memory hole        |
0A0000   +------------------------+
         | Reserved for BIOS      | Leave as much as possible unused
         ~                        ~
         | Command line           | (Can also be below the X+10000 mark)
X+10000  +------------------------+
         | Stack/heap             | For use by the kernel real-mode code.
X+08000  +------------------------+
         | Kernel setup           | The kernel real-mode code.
         | Kernel boot sector     | The kernel legacy boot sector.
       X +------------------------+
         | Boot loader            | 
```

So after the bootloader transferred control to the kernel, it starts somewhere at:

```
0x1000 + X + sizeof(KernelBootSector) + 1 
```

where `X` is the address of kernel bootsector loaded. In my case `X` is `0x10000`, we can see it in memory dump:

![kernel first address](img/49d843b3.jpg)

Ok, now the bootloader has loaded Linux kernel into the memory, filled header fields and jumped to it. Now we can move directly to the kernel setup code.

## Start of Kernel Setup

Finally we are in the kernel. Technically kernel didn't run yet, first of all we need to setup kernel, memory manager, process manager etc. Kernel setup execution starts from [arch/x86/boot/header.S](https://github.com/torvalds/linux/blob/master/arch/x86/boot/header.S) at the [_start](https://github.com/torvalds/linux/blob/master/arch/x86/boot/header.S#L293). It is a little strange at the first look, there are many instructions before it.

Actually Long time ago Linux kernel had its own bootloader, but now if you run for example:

```
qemu-system-x86_64 vmlinuz-3.18-generic 
```

You will see:

![Try vmlinuz in qemu](img/a5ed9787.jpg)

Actually `header.S` starts from [MZ](https://en.wikipedia.org/wiki/DOS_MZ_executable) (see image above), error message printing and following [PE](https://en.wikipedia.org/wiki/Portable_Executable) header:

```
#ifdef CONFIG_EFI_STUB
# "MZ", MS-DOS header
.byte 0x4d
.byte 0x5a
#endif
...
...
...
pe_header:
    .ascii "PE"
    .word 0 
```

It needs this for loading the operating system with [UEFI](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface). Here we will not see how it works (we will these later in the next parts).

So the actual kernel setup entry point is:

```
// header.S line 292
.globl _start
_start: 
```

Bootloader (grub2 and others) knows about this point (`0x200` offset from `MZ`) and makes a jump directly to this point, despite the fact that `header.S` starts from `.bstext` section which prints error message:

```
//
// arch/x86/boot/setup.ld
//
. = 0;                    // current position
.bstext : { *(.bstext) }  // put .bstext section to position 0
.bsdata : { *(.bsdata) } 
```

So kernel setup entry point is:

```
 .globl _start
_start:
    .byte 0xeb
    .byte start_of_setup-1f
1:
    //
    // rest of the header
    // 
```

Here we can see `jmp` instruction opcode - `0xeb` to the `start_of_setup-1f` point. `Nf` notation means following: `2f` refers to the next local `2:` label. In our case it is label `1` which goes right after jump. It contains rest of setup [header](https://github.com/torvalds/linux/blob/master/Documentation/x86/boot.txt#L156) and right after setup header we can see `.entrytext` section which starts at `start_of_setup` label.

Actually it's the first code which starts to execute besides previous jump instruction. After kernel setup got the control from bootloader, first `jmp` instruction is located at `0x200` (first 512 bytes) offset from the start of kernel real mode. This we can read in Linux kernel boot protocol and also see in grub2 source code:

```
 state.gs = state.fs = state.es = state.ds = state.ss = segment;
  state.cs = segment + 0x20; 
```

It means that segment registers will have following values after kernel setup starts to work:

```
fs = es = ds = ss = 0x1000
cs = 0x1020 
```

for my case when kernel loaded at `0x10000`.

After jump to `start_of_setup`, it needs to do the following things:

*   Be sure that all values of all segment registers are equal
*   Setup correct stack if needed
*   Setup [bss](https://en.wikipedia.org/wiki/.bss)
*   Jump to C code at [main.c](https://github.com/torvalds/linux/blob/master/arch/x86/boot/main.c)

Let's look at implementation.

## Segment registers align

First of all it ensures that `ds` and `es` segment registers point to the same address and enables interrupts with `sti` instruction:

```
 movw    %ds, %ax
    movw    %ax, %es
    sti 
```

As I wrote above, grub2 loads kernel setup code at `0x10000` address and `cs` at `0x1020` because execution doesn't start from the start of file, but from:

```
_start:
    .byte 0xeb
    .byte start_of_setup-1f 
```

`jump`, which is 512 bytes offset from the [4d 5a](https://github.com/torvalds/linux/blob/master/arch/x86/boot/header.S#L47). Also need to align `cs` from `0x10200` to `0x10000` as all other segment registers. After that we setup the stack:

```
 pushw    %ds
    pushw    $6f
    lretw 
```

push `ds` value to stack, and address of [6](https://github.com/torvalds/linux/blob/master/arch/x86/boot/header.S#L494) label and execute `lretw` instruction. When we call `lretw`, it loads address of label `6` to [instruction pointer](https://en.wikipedia.org/wiki/Program_counter) register and `cs` with value of `ds`. After it we will have `ds` and `cs` with the same values.

## Stack Setup

Actually, almost all of the setup code is preparation for C language environment in the real mode. The next [step](https://github.com/torvalds/linux/blob/master/arch/x86/boot/header.S#L467) is checking of `ss` register value and making of correct stack if `ss` is wrong:

```
 movw    %ss, %dx
    cmpw    %ax, %dx
    movw    %sp, %dx
    je    2f 
```

Generally, it can be 3 different cases:

*   `ss` has valid value 0x10000 (as all other segment registers beside `cs`)
*   `ss` is invalid and `CAN_USE_HEAP` flag is set (see below)
*   `ss` is invalid and `CAN_USE_HEAP` flag is not set (see below)

Let's look at all of these cases:

1.  `ss` has a correct address (0x10000). In this case we go to label [2](https://github.com/torvalds/linux/blob/master/arch/x86/boot/header.S#L481):

```
2:     andw    $~3, %dx
    jnz    3f
    movw    $0xfffc, %dx
3:  movw    %ax, %ss
    movzwl %dx, %esp
    sti 
```

Here we can see aligning of `dx` (contains `sp` given by bootloader) to 4 bytes and checking that it is not zero. If it is zero we put `0xfffc` (4 byte aligned address before maximum segment size - 64 KB) to `dx`. If it is not zero we continue to use `sp` given by bootloader (0xf7f4 in my case). After this we put `ax` value to `ss` which stores correct segment address `0x10000` and set up correct `sp`. After it we have correct stack:

![stack](img/b10804f1.jpg)

1.  In the second case (`ss` != `ds`), first of all put [_end](https://github.com/torvalds/linux/blob/master/arch/x86/boot/setup.ld#L52) (address of end of setup code) value in `dx`. And check `loadflags` header field with `testb` instruction too see if we can use heap or not. [loadflags](https://github.com/torvalds/linux/blob/master/arch/x86/boot/header.S#L321) is a bitmask header which is defined as:

```
#define LOADED_HIGH        (1<<0)
#define QUIET_FLAG        (1<<5)
#define KEEP_SEGMENTS    (1<<6)
#define CAN_USE_HEAP    (1<<7) 
```

And as we can read in the boot protocol:

```
Field name:    loadflags

  This field is a bitmask.

  Bit 7 (write): CAN_USE_HEAP
    Set this bit to 1 to indicate that the value entered in the
    heap_end_ptr is valid.  If this field is clear, some setup code
    functionality will be disabled. 
```

If `CAN_USE_HEAP` bit is set, put `heap_end_ptr` to `dx` which points to `_end` and add `STACK_SIZE` (minimal stack size - 512 bytes) to it. After this if `dx` is not carry, jump to `2` (it will not be carry, dx = _end + 512) label as in previous case and make correct stack.

![stack](img/e358e174.jpg)

1.  The last case when `CAN_USE_HEAP` is not set, we just use minimal stack from `_end` to `_end + STACK_SIZE`:

![minimal stack](img/454d30ad.jpg)

## BSS Setup

The last two steps that need to happen before we can jump to the main C code, are that we need to set up the [BSS](https://en.wikipedia.org/wiki/.bss) area, and check the "magic" signature. Firstly, signature checking:

```
cmpl    $0x5a5aaa55, setup_sig
jne    setup_bad 
```

This simply consists of comparing the [setup_sig](https://github.com/torvalds/linux/blob/master/arch/x86/boot/setup.ld#L39) against the magic number `0x5a5aaa55`. If they are not equal, a fatal error is reported.

But if the magic number matches, knowing we have a set of correct segment registers, and a stack, we need only setup the BSS section before jumping into the C code.

The BSS section is used for storing statically allocated, uninitialized, data. Linux carefully ensures this area of memory is first blanked, using the following code:

```
 movw    $__bss_start, %di
    movw    $_end+3, %cx
    xorl    %eax, %eax
    subw    %di, %cx
    shrw    $2, %cx
    rep; stosl 
```

First of all the [__bss_start](https://github.com/torvalds/linux/blob/master/arch/x86/boot/setup.ld#L47) address is moved into `di`, and the `_end + 3` address (+3 - aligns to 4 bytes) is moved into `cx`. The `eax` register is cleared (using an `xor` instruction), and the bss section size (`cx`-`di`) is calculated and put into `cx`. Then, `cx` is divided by four (the size of a 'word'), and the `stosl` instruction is repeatedly used, storing the value of `eax` (zero) into the address pointed to by `di`, and automatically increasing `di` by four (this occurs until `cx` reaches zero). The net effect of this code, is that zeros are written through all words in memory from `__bss_start` to `_end`:

![bss](img/1d9d2ae7.jpg)

## Jump to main

That's all, we have the stack, BSS and now we can jump to the `main()` C function:

```
 calll main 
```

The `main()` function is located in [arch/x86/boot/main.c](https://github.com/torvalds/linux/blob/master/arch/x86/boot/main.c). What will be there? We will see it in the next part.

## Conclusion

This is the end of the first part about Linux kernel internals. If you have questions or suggestions, ping me in twitter [0xAX](https://twitter.com/0xAX), drop me email or just create [issue](https://github.com/0xAX/linux-internals/issues/new). In the next part we will see first C code which executes in Linux kernel setup, implementation of memory routines as `memset`, `memcpy`, `earlyprintk` implementation and early console initialization and many more.

**Please note that English is not my first language and I am really sorry for any inconvenience. If you found any mistakes please send me PR to [linux-internals](https://github.com/0xAX/linux-internals).**

## Links

*   [Intel 80386 programmer's reference manual 1986](http://css.csail.mit.edu/6.858/2014/readings/i386.pdf)
*   [Minimal Boot Loader for Intel® Architecture](https://www.cs.cmu.edu/~410/doc/minimal_boot.pdf)
*   [8086](http://en.wikipedia.org/wiki/Intel_8086)
*   [80386](http://en.wikipedia.org/wiki/Intel_80386)
*   [Reset vector](http://en.wikipedia.org/wiki/Reset_vector)
*   [Real mode](http://en.wikipedia.org/wiki/Real_mode)
*   [Linux kernel boot protocol](https://www.kernel.org/doc/Documentation/x86/boot.txt)
*   [CoreBoot developer manual](http://www.coreboot.org/Developer_Manual)
*   [Ralf Brown's Interrupt List](http://www.ctyme.com/intr/int.htm)
*   [Power supply](http://en.wikipedia.org/wiki/Power_supply)
*   [Power good signal](http://en.wikipedia.org/wiki/Power_good_signal)