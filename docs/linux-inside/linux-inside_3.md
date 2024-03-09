# 理论

这一章描述各种理论性概念和那些不直接涉及实践，但是知道了会很有用的概念。

*   [分页](http://xinqiu.gitbooks.io/linux-insides/content/Theory/Paging.html)
*   [Elf64 格式](http://xinqiu.gitbooks.io/linux-insides/content/Theory/ELF.html)

# 分页

## Introduction

In the fifth [part](http://0xax.gitbooks.io/linux-insides/content/Booting/linux-bootstrap-5.html) of the series `Linux kernel booting process` we learned about what the kernel does in its earliest stage. In the next step the kernel will initialize different things like `initrd` mounting, lockdep initialization, and many many others things, before we can see how the kernel runs the first init process.

Yeah, there will be many different things, but many many and once again many work with **memory**.

In my view, memory management is one of the most complex part of the linux kernel and in system programming in general. This is why before we proceed with the kernel initialization stuff, we need to get acquainted with paging.

`Paging` is a mechanism that translates a linear memory address to a physical address. If you have read the previous parts of this book, you may remember that we saw segmentation in real mode when physical addresses are calculated by shifting a segment register by four and adding an offset. We also saw segmentation in protected mode, where we used the descriptor tables and base addresses from descriptors with offsets to calculate the physical addresses. Now that we are in 64-bit mode, will see paging.

As the Intel manual says:

> Paging provides a mechanism for implementing a conventional demand-paged, virtual-memory system where sections of a program’s execution environment are mapped into physical memory as needed.

So... In this post I will try to explain the theory behind paging. Of course it will be closely related to the `x86_64` version of the linux kernel for, but we will not go into too much details (at least in this post).

## Enabling paging

There are three paging modes:

*   32-bit paging;
*   PAE paging;
*   IA-32e paging.

We will only explain the last mode here. To enable the `IA-32e paging` paging mode we need to do following things:

*   set the `CR0.PG` bit;
*   set the `CR4.PAE` bit;
*   set the `IA32_EFER.LME` bit.

We already saw where those this bits were set in [arch/x86/boot/compressed/head_64.S](https://github.com/torvalds/linux/blob/master/arch/x86/boot/compressed/head_64.S):

```
movl    $(X86_CR0_PG | X86_CR0_PE), %eax
movl    %eax, %cr0 
```

and

```
movl    $MSR_EFER, %ecx
rdmsr
btsl    $_EFER_LME, %eax
wrmsr 
```

## Paging structures

Paging divides the linear address space into fixed-size pages. Pages can be mapped into the physical address space or even external storage. This fixed size is `4096` bytes for the `x86_64` linux kernel. To perform the linear address translation to a physical address special structures are used. Every structure is `4096` bytes size and contains `512` entries (this only for `PAE` and `IA32_EFER.LME` modes). Paging structures are hierarchical and the linux kernel uses 4 level of paging in the `x86_64` architecture. The CPU uses a part of the linear address to identify the entry in another paging structure which is at the lower level or physical memory region (`page frame`) or physical address in this region (`page offset`). The address of the top level paging structure located in the `cr3` register. We already saw this in [arch/x86/boot/compressed/head_64.S](https://github.com/torvalds/linux/blob/master/arch/x86/boot/compressed/head_64.S):

```
leal    pgtable(%ebx), %eax
movl    %eax, %cr3 
```

We built the page table structures and put the address of the top-level structure in the `cr3` register. Here `cr3` is used to store the address of the top-level structure, the `PML4` or `Page Global Directory` as it is called in the linux kernel. `cr3` is 64-bit register and has the following structure:

```
63                  52 51                                                        32
 --------------------------------------------------------------------------------
|                     |                                                          |
|    Reserved MBZ     |            Address of the top level structure            |
|                     |                                                          |
 --------------------------------------------------------------------------------
31                                  12 11            5     4     3 2             0
 --------------------------------------------------------------------------------
|                                     |               |  P  |  P  |              |
|  Address of the top level structure |   Reserved    |  C  |  W  |    Reserved  |
|                                     |               |  D  |  T  |              |
 -------------------------------------------------------------------------------- 
```

These fields have the following meanings:

*   Bits 2:0 - ignored;
*   Bits 51:12 - stores the address of the top level paging structure;
*   Bit 3 and 4 - PWT or Page-Level Writethrough and PCD or Page-level cache disable indicate. These bits control the way the page or Page Table is handled by the hardware cache;
*   Reserved - reserved must be 0;
*   Bits 63:52 - reserved must be 0.

The linear address translation address is following:

*   A given linear address arrives to the [MMU](http://en.wikipedia.org/wiki/Memory_management_unit) instead of memory bus.
*   64-bit linear address splits on some parts. Only low 48 bits are significant, it means that `2⁴⁸` or 256 TBytes of linear-address space may be accessed at any given time.
*   `cr3` register stores the address of the 4 top-level paging structure.
*   `47:39` bits of the given linear address stores an index into the paging structure level-4, `38:30` bits stores index into the paging structure level-3, `29:21` bits stores an index into the paging structure level-2, `20:12` bits stores an index into the paging structure level-1 and `11:0` bits provide the byte offset into the physical page.

schematically, we can imagine it like this:

![4-level paging](img/8839c62a.jpg)

Every access to a linear address is either a supervisor-mode access or a user-mode access. This access is determined by the `CPL` (current privilege level). If `CPL < 3` it is a supervisor mode access level otherwise, otherwise it is a user mode access level. For example, the top level page table entry contains access bits and has the following structure:

```
63  62                  52 51                                                    32
 --------------------------------------------------------------------------------
| N |                     |                                                     |
|   |     Available       |     Address of the paging structure on lower level  |
| X |                     |                                                     |
 --------------------------------------------------------------------------------
31                                              12 11  9 8 7 6 5   4   3 2 1     0
 --------------------------------------------------------------------------------
|                                                |     | M |I| | P | P |U|W|    |
| Address of the paging structure on lower level | AVL | B |G|A| C | W | | |  P |
|                                                |     | Z |N| | D | T |S|R|    |
 -------------------------------------------------------------------------------- 
```

Where:

*   63 bit - N/X bit (No Execute Bit) - presents ability to execute the code from physical pages mapped by the table entry;
*   62:52 bits - ignored by CPU, used by system software;
*   51:12 bits - stores physical address of the lower level paging structure;
*   12:9 bits - ignored by CPU;
*   MBZ - must be zero bits;
*   Ignored bits;
*   A - accessed bit indicates was physical page or page structure accessed;
*   PWT and PCD used for cache;
*   U/S - user/supervisor bit controls user access to the all physical pages mapped by this table entry;
*   R/W - read/write bit controls read/write access to the all physical pages mapped by this table entry;
*   P - present bit. Current bit indicates was page table or physical page loaded into primary memory or not.

Ok, we know about the paging structures and their entries. Now let's see some details about 4-level paging in the linux kernel.

## Paging structures in the linux kernel

As we've seen, the linux kernel in `x86_64` uses 4-level page tables. Their names are:

*   Page Global Directory
*   Page Upper Directory
*   Page Middle Directory
*   Page Table Entry

After you've compiled and installed the linux kernel, you can see the `System.map` file which stores the virtual addresses of the functions that are used by the kernel. For example:

```
$ grep "start_kernel" System.map
ffffffff81efe497 T x86_64_start_kernel
ffffffff81efeaa2 T start_kernel 
```

We can see `0xffffffff81efe497` here. I doubt you really have that much RAM installed. But anyway, `start_kernel` and `x86_64_start_kernel` will be executed. The address space in `x86_64` is `2⁶⁴` size, but it's too large, that's why a smaller address space is used, only 48-bits wide. So we have a situation where the physical address space is limited to 48 bits, but addressing still performed with 64 bit pointers. How is this problem solved? Look at this diagram:

```
0xffffffffffffffff  +-----------+
                    |           |
                    |           | Kernelspace
                    |           |
 0xffff800000000000 +-----------+
                    |           |
                    |           |
                    |   hole    |
                    |           |
                    |           |
0x00007fffffffffff  +-----------+
                    |           |
                    |           |  Userspace
                    |           |
0x0000000000000000  +-----------+ 
```

This solution is `sign extension`. Here we can see that the lower 48 bits of a virtual address can be used for addressing. Bits `63:48` can be either only zeroes or only ones. Note that the virtual address space is split in 2 parts:

*   Kernel space
*   Userspace

Userspace occupies the lower part of the virtual address space, from `0x000000000000000` to `0x00007fffffffffff` and kernel space occupies the highest part from `0xffff8000000000` to `0xffffffffffffffff`. Note that bits `63:48` is 0 for userspace and 1 for kernel space. All addresses which are in kernel space and in userspace or in other words which higher `63:48` bits are zeroes or ones are called `canonical` addresses. There is a `non-canonical` area between these memory regions. Together these two memory regions (kernel space and user space) are exactly `2⁴⁸` bits wide. We can find the virtual memory map with 4 level page tables in the [Documentation/x86/x86_64/mm.txt](https://github.com/torvalds/linux/blob/master/Documentation/x86/x86_64/mm.txt):

```
0000000000000000 - 00007fffffffffff (=47 bits) user space, different per mm
hole caused by [48:63] sign extension
ffff800000000000 - ffff87ffffffffff (=43 bits) guard hole, reserved for hypervisor
ffff880000000000 - ffffc7ffffffffff (=64 TB) direct mapping of all phys. memory
ffffc80000000000 - ffffc8ffffffffff (=40 bits) hole
ffffc90000000000 - ffffe8ffffffffff (=45 bits) vmalloc/ioremap space
ffffe90000000000 - ffffe9ffffffffff (=40 bits) hole
ffffea0000000000 - ffffeaffffffffff (=40 bits) virtual memory map (1TB)
... unused hole ...
ffffec0000000000 - fffffc0000000000 (=44 bits) kasan shadow memory (16TB)
... unused hole ...
ffffff0000000000 - ffffff7fffffffff (=39 bits) %esp fixup stacks
... unused hole ...
ffffffff80000000 - ffffffffa0000000 (=512 MB)  kernel text mapping, from phys 0
ffffffffa0000000 - ffffffffff5fffff (=1525 MB) module mapping space
ffffffffff600000 - ffffffffffdfffff (=8 MB) vsyscalls
ffffffffffe00000 - ffffffffffffffff (=2 MB) unused hole 
```

We can see here the memory map for user space, kernel space and the non-canonical area in-between them. The user space memory map is simple. Let's take a closer look at the kernel space. We can see that it starts from the guard hole which is reserved for the hypervisor. We can find the definition of this guard hole in [arch/x86/include/asm/page_64_types.h](https://github.com/torvalds/linux/blob/master/arch/x86/include/asm/page_64_types.h):

```
#define __PAGE_OFFSET _AC(0xffff880000000000, UL) 
```

Previously this guard hole and `__PAGE_OFFSET` was from `0xffff800000000000` to `0xffff80ffffffffff` to prevent access to non-canonical area, but was later extended by 3 bits for the hypervisor.

Next is the lowest usable address in kernel space - `ffff880000000000`. This virtual memory region is for direct mapping of the all physical memory. After the memory space which maps all physical addresses, the guard hole. It needs to be between the direct mapping of all the physical memory and the vmalloc area. After the virtual memory map for the first terabyte and the unused hole after it, we can see the `kasan` shadow memory. It was added by [commit](https://github.com/torvalds/linux/commit/ef7f0d6a6ca8c9e4b27d78895af86c2fbfaeedb2) and provides the kernel address sanitizer. After the next unused hole we can see the `esp` fixup stacks (we will talk about it in other parts of this book) and the start of the kernel text mapping from the physical address - `0`. We can find the definition of this address in the same file as the `__PAGE_OFFSET`:

```
#define __START_KERNEL_map      _AC(0xffffffff80000000, UL) 
```

Usually kernel's `.text` start here with the `CONFIG_PHYSICAL_START` offset. We saw it in the post about [ELF64](https://github.com/0xAX/linux-insides/blob/master/Theory/ELF.md):

```
readelf -s vmlinux | grep ffffffff81000000
     1: ffffffff81000000     0 SECTION LOCAL  DEFAULT    1 
 65099: ffffffff81000000     0 NOTYPE  GLOBAL DEFAULT    1 _text
 90766: ffffffff81000000     0 NOTYPE  GLOBAL DEFAULT    1 startup_64 
```

Here i checked `vmlinux` with the `CONFIG_PHYSICAL_START` is `0x1000000`. So we have the start point of the kernel `.text` - `0xffffffff80000000` and offset - `0x1000000`, the resulted virtual address will be `0xffffffff80000000 + 1000000 = 0xffffffff81000000`.

After the kernel `.text` region there is the virtual memory region for kernel modules, `vsyscalls` and an unused hole of 2 megabytes.

We've seen how the kernel's virtual memory map is laid out and how a virtual address is translated into a physical one. Let's take for example following address:

```
0xffffffff81000000 
```

In binary it will be:

```
1111111111111111 111111111 111111110 000001000 000000000 000000000000
      63:48        47:39     38:30     29:21     20:12      11:0 
```

This virtual address is split in parts as described above:

*   `63:48` - bits not used;
*   `47:39` - bits of the given linear address stores an index into the paging structure level-4;
*   `38:30` - bits stores index into the paging structure level-3;
*   `29:21` - bits stores an index into the paging structure level-2;
*   `20:12` - bits stores an index into the paging structure level-1;
*   `11:0` - bits provide the byte offset into the physical page.

That is all. Now you know a little about theory of `paging` and we can go ahead in the kernel source code and see the first initialization steps.

## Conclusion

It's the end of this short part about paging theory. Of course this post doesn't cover every detail of paging, but soon we'll see in practice how the linux kernel builds paging structures and works with them.

**Please note that English is not my first language and I am really sorry for any inconvenience. If you've found any mistakes please send me PR to [linux-internals](https://github.com/0xAX/linux-internals).**

## Links

*   [Paging on Wikipedia](http://en.wikipedia.org/wiki/Paging)
*   [Intel 64 and IA-32 architectures software developer's manual volume 3A](http://www.intel.com/content/www/us/en/processors/architectures-software-developer-manuals.html)
*   [MMU](http://en.wikipedia.org/wiki/Memory_management_unit)
*   [ELF64](https://github.com/0xAX/linux-insides/blob/master/Theory/ELF.md)
*   [Documentation/x86/x86_64/mm.txt](https://github.com/torvalds/linux/blob/master/Documentation/x86/x86_64/mm.txt)
*   [Last part - Kernel booting process](http://0xax.gitbooks.io/linux-insides/content/Booting/linux-bootstrap-5.html)

# Elf64 格式

# ELF 文件格式

ELF (Executable and Linkable Format)是一种为可执行文件，目标文件，共享链接库和内核转储(core dumps)准备的标准文件格式。 Linux 和很多类 Unix 操作系统都使用这个格式。 让我们来看一下 64 位 ELF 文件格式的结构以及内核源码中有关于它的一些定义。

一个 ELF 文件由以下三部分组成：

*   ELF 头(ELF header) - 描述文件的主要特性：类型，CPU 架构，入口地址，现有部分的大小和偏移等等；

*   程序头表(Program header table) - 列举了所有有效的段(segments)和他们的属性。 程序头表需要加载器将文件中的节加载到虚拟内存段中；

*   节头表(Section header table) - 包含对节(sections)的描述。

现在让我们对这些部分有一些更深的了解。

**ELF 头(ELF header)**

ELF 头(ELF header)位于文件的开始位置。 它的主要目的是定位文件的其他部分。 文件头主要包含以下字段：

*   ELF 文件鉴定 - 一个字节数组用来确认文件是否是一个 ELF 文件，并且提供普通文件特征的信息；
*   文件类型 - 确定文件类型。 这个字段描述文件是一个重定位文件，或可执行文件,或...；
*   目标结构；
*   ELF 文件格式的版本；
*   程序入口地址；
*   程序头表的文件偏移；
*   节头表的文件偏移；
*   ELF 头(ELF header)的大小；
*   程序头表的表项大小；
*   其他字段...

你可以在内核源码种找到表示 ELF64 header 的结构体 `elf64_hdr`：

```
typedef struct elf64_hdr {
    unsigned char    e_ident[EI_NIDENT];
    Elf64_Half e_type;
    Elf64_Half e_machine;
    Elf64_Word e_version;
    Elf64_Addr e_entry;
    Elf64_Off e_phoff;
    Elf64_Off e_shoff;
    Elf64_Word e_flags;
    Elf64_Half e_ehsize;
    Elf64_Half e_phentsize;
    Elf64_Half e_phnum;
    Elf64_Half e_shentsize;
    Elf64_Half e_shnum;
    Elf64_Half e_shstrndx;
} Elf64_Ehdr; 
```

这个结构体定义在 [elf.h](https://github.com/torvalds/linux/blob/master/include/uapi/linux/elf.h#L220)

**节(sections)**

所有的数据都存储在 ELF 文件的节(sections)中。 我们通过节头表中的索引(index)来确认节(sections)。 节头表表项包含以下字段：

*   节的名字；
*   节的类型；
*   节的属性；
*   内存地址；
*   文件中的偏移；
*   节的大小；
*   到其他节的链接；
*   各种各样的信息；
*   地址对齐；
*   这个表项的大小，如果有的话；

而且，在 linux 内核中结构体 `elf64_shdr` 如下所示:

```
typedef struct elf64_shdr {
    Elf64_Word sh_name;
    Elf64_Word sh_type;
    Elf64_Xword sh_flags;
    Elf64_Addr sh_addr;
    Elf64_Off sh_offset;
    Elf64_Xword sh_size;
    Elf64_Word sh_link;
    Elf64_Word sh_info;
    Elf64_Xword sh_addralign;
    Elf64_Xword sh_entsize;
} Elf64_Shdr; 
```

[elf.h](https://github.com/torvalds/linux/blob/master/include/uapi/linux/elf.h#L312)

**程序头表(Program header table)**

在可执行文件或者共享链接库中所有的节(sections)都被分为多个段(segments)。 程序头是一个结构的数组，每一个结构都表示一个段(segments)。 它的结构就像这样：

```
typedef struct elf64_phdr {
    Elf64_Word p_type;
    Elf64_Word p_flags;
    Elf64_Off p_offset;
    Elf64_Addr p_vaddr;
    Elf64_Addr p_paddr;
    Elf64_Xword p_filesz;
    Elf64_Xword p_memsz;
    Elf64_Xword p_align;
} Elf64_Phdr; 
```

在内核源码中。

`elf64_phdr` 定义在相同的 [elf.h](https://github.com/torvalds/linux/blob/master/include/uapi/linux/elf.h#L254) 文件中.

EFL 文件也包含其他的字段或结构。 你可以在 [Documentation](http://www.uclibc.org/docs/elf-64-gen.pdf) 中查看。 现在我们来查看一下 `vmlinux` 这个 ELF 文件。

## vmlinux

`vmlinux` 也是一个可重定位的 ELF 文件。 我们可以使用 `readelf` 工具来查看它。 首先，让我们看一下它的头部：

```
$ readelf -h  vmlinux
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1000000
  Start of program headers:          64 (bytes into file)
  Start of section headers:          381608416 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         5
  Size of section headers:           64 (bytes)
  Number of section headers:         73
  Section header string table index: 70 
```

我们可以看出 `vmlinux` 是一个 64 位可执行文件。 我们可以从 [Documentation/x86/x86_64/mm.txt](https://github.com/torvalds/linux/blob/master/Documentation/x86/x86_64/mm.txt#L19) 读到相关信息:

```
ffffffff80000000 - ffffffffa0000000 (=512 MB)  kernel text mapping, from phys 0 
```

之后我们可以在 `vmlinux` ELF 文件中查看这个地址：

```
$ readelf -s vmlinux | grep ffffffff81000000
     1: ffffffff81000000     0 SECTION LOCAL  DEFAULT    1 
 65099: ffffffff81000000     0 NOTYPE  GLOBAL DEFAULT    1 _text
 90766: ffffffff81000000     0 NOTYPE  GLOBAL DEFAULT    1 startup_64 
```

值得注意的是，`startup_64` 例程的地址不是 `ffffffff80000000`, 而是 `ffffffff81000000`。 现在我们来解释一下。

我们可以在 [arch/x86/kernel/vmlinux.lds.S](https://github.com/torvalds/linux/blob/master/arch/x86/kernel/vmlinux.lds.S) 看见如下的定义 :

```
 . = __START_KERNEL;
    ...
    ...
    ..
    /* Text and read-only data */
    .text :  AT(ADDR(.text) - LOAD_OFFSET) {
        _text = .;
        ...
        ...
        ...
    } 
```

其中，`__START_KERNEL` 定义如下:

```
#define __START_KERNEL        (__START_KERNEL_map + __PHYSICAL_START) 
```

从这个文档中看出，`__START_KERNEL_map` 的值是 `ffffffff80000000` 以及 `__PHYSICAL_START` 的值是 `0x1000000`。 这就是 `startup_64`的地址是 `ffffffff81000000`的原因了。

最后我们通过以下命令来得到程序头表的内容：

```
readelf -l vmlinux

Elf file type is EXEC (Executable file)
Entry point 0x1000000
There are 5 program headers, starting at offset 64

Program Headers:
  Type           Offset             VirtAddr           PhysAddr
                 FileSiz            MemSiz              Flags  Align
  LOAD           0x0000000000200000 0xffffffff81000000 0x0000000001000000
                 0x0000000000cfd000 0x0000000000cfd000  R E    200000
  LOAD           0x0000000001000000 0xffffffff81e00000 0x0000000001e00000
                 0x0000000000100000 0x0000000000100000  RW     200000
  LOAD           0x0000000001200000 0x0000000000000000 0x0000000001f00000
                 0x0000000000014d98 0x0000000000014d98  RW     200000
  LOAD           0x0000000001315000 0xffffffff81f15000 0x0000000001f15000
                 0x000000000011d000 0x0000000000279000  RWE    200000
  NOTE           0x0000000000b17284 0xffffffff81917284 0x0000000001917284
                 0x0000000000000024 0x0000000000000024         4

 Section to Segment mapping:
  Segment Sections...
   00     .text .notes __ex_table .rodata __bug_table .pci_fixup .builtin_fw
          .tracedata __ksymtab __ksymtab_gpl __kcrctab __kcrctab_gpl
          __ksymtab_strings __param __modver 
   01     .data .vvar 
   02     .data..percpu 
   03     .init.text .init.data .x86_cpu_dev.init .altinstructions
          .altinstr_replacement .iommu_table .apicdrivers .exit.text
          .smp_locks .data_nosave .bss .brk 
```

这里我们可以看出五个包含节(sections)列表的段(segments)。 你可以在生成的链接器脚本 - `arch/x86/kernel/vmlinux.lds` 中找到所有的节(sections)。

就这样吧。 当然，它不是 ELF(Executable and Linkable Format)的完整描述，但是如果你想要知道更多，可以参考这个文档 - [这里](http://www.uclibc.org/docs/elf-64-gen.pdf)