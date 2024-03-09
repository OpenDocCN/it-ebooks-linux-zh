# 调试入口

主讲调试相关内容，目前主要关注内核部分的调试，后续会补充用户空间的调试内容。

# 内核调试

# Kernel Debugging

About kernel debugging tools and tips.

# Debugging by printing / Printk

> From: [eLinux.org](http://eLinux.org/Debugging_by_printing "http://eLinux.org/Debugging_by_printing")

# Debugging by printing

Probably the simplest way to get some debug information from your kernel code is by printing out various information with the kernel's equivalent of printf - the printk function and its derivatives. The k in printk is used to specifically remind kernel developers that the environment is different.

## Contents

*   1 Usage
*   2 Log Levels
*   3 Rate limiting and one time messages
*   4 Printk from userspace
*   5 Internals / Changing the size of the printk buffer
*   6 Pros and Cons
*   7 Debugging early boot problems
    *   7.1 Accessing the printk buffer after a silent hang on boot
        *   7.1.1 Redboot example on a Freescale ADS board
        *   7.1.2 U-boot example on an OMAP OSK board
        *   7.1.3 Grub
    *   7.2 Using CONFIG-DEBUG-LL and printascii() (ARM only)
*   8 NetConsole
    *   8.1 Netconsole resources
*   9 Misc
    *   9.1 Dmesg / Clearing the buffer
    *   9.2 Printk Timestamps
    *   9.3 Printing buffers as hex
    *   9.4 Dynamic Debugging
*   10 Disabling printk messages at compile time
*   11 References and external links

## Usage

printk works more or less the same way as printf in userspace, so if you ever debugged your userspace program using printf, you are ready to do the same with your kernel code, e.g. by adding:

```
printk("My Debugger is Printk\n"); 
```

This wasn't that difficult, was it?

Usually you would print out some more interesting information like

```
printk("Var1 %d var2 %d\n", var1, var2); 
```

just like in userspace.

In order to see the kernel messages, just use the

```
$ dmesg 
```

command in one of your shells - this one will print out the whole kernel log buffer to you.

Most of the conversion specifiers supported by the user-space library routine printf() are also available in the kernel; there are some notable additions, including "%pf", which will print the symbol name in place of the numeric pointer value, if available.

The supported format strings are quite extensively documented in [Documentation/printk-formats.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux.git;a=blob;f=Documentation/printk-formats.txt;hb=HEAD)

However please **note**: always use *%zu*, *%zd* or *%zx* for printing *size_t* and *ssize_t* values. ssize_t and size_t are quite common values in the kernel, so please use the *%z* to avoid annoying compile warnings.

* * *

**Author's practical advice:** If you want to debug an oops (e.g caused by releasing a resource twice) in your driver and you don't have a clue where the oops happens, simply add this line

```
printk(KERN_ALERT "DEBUG: Passed %s %d \n",__FUNCTION__,__LINE__); 
```

after each possibly offending statement. Recompile and (re-)load the module and trigger the error condition - your log now shows you the last line that was successfully executed before the oops happened.

Of course you should **remove** these 'rude' statements before shipping your module ;)

* * *

## Log Levels

If you look into real kernel code you will always see something like:

```
printk(KERN_ERR "something went wrong, return code: %d\n",ret); 
```

where "*KERN_ERR*" is one of the eight different log levels defined in [include/linux/kern_levels.h](http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/include/linux/kern_levels.h?id=HEAD) and specifies the severity of the error message.

Note that there is **NO** comma between the *KERN_ERR* and the format string (as the preprocessor concatenates both strings)

The log levels are:

| Name | String | Meaning | alias function |
| --- | --- | --- | --- |
| KERN_EMERG | "0" | Emergency messages, system is about to crash or is unstable | pr_emerg |
| KERN_ALERT | "1" | Something bad happened and action must be taken immediately | pr_alert |
| KERN_CRIT | "2" | A critical condition occurred like a serious hardware/software failure | pr_crit |
| KERN_ERR | "3" | An error condition, often used by drivers to indicate difficulties with the hardware | pr_err |
| KERN_WARNING | "4" | A warning, meaning nothing serious by itself but might indicate problems | pr_warning |
| KERN_NOTICE | "5" | Nothing serious, but notably nevertheless. Often used to report security events. | pr_notice |
| KERN_INFO | "6" | Informational message e.g. startup information at driver initialization | pr_info |
| KERN_DEBUG | "7" | Debug messages | pr_debug, pr_devel if DEBUG is defined |
| KERN_DEFAULT | "d" | The default kernel loglevel |  |
| KERN_CONT | "" | "continued" line of log printout (only done after a line that had no enclosing \n) [[1]](http://lwn.net/Articles/252651/) | pr_cont |

Note that the actual values of the log levels are prepended by the KERN_SOH character whose ASCII value is '\001'. Read the source for more details.

The pr_* macros (with exception of pr_debug) are simple shorthand definitions in *include/linux/printk.h* for their respective printk call and should probably be used in newer drivers.

*pr_devel* and *pr_debug* are replaced with *printk(KERN_DEBUG ...* if the kernel was compiled with *DEBUG*, otherwise replaced with an empty statement.

For drivers the pr_debug should not be used anymore (use dev_dbg instead).

If you don't specify a log level in your message it defaults to *DEFAULT_MESSAGE_LOGLEVEL* (usually *"4"*=*KERN_WARNING*) which can be set via the *CONFIG_DEFAULT_MESSAGE_LOGLEVEL* kernel config option (*make menuconfig-> Kernel Hacking -> Default message log level*)

The log level is used by the kernel to determine the importance of a message and to decide whether it should be presented to the user immediately, by printing it to the current console (where console could also be a serial line or even a printer, not an xterm).

For this the kernel compares the log level of the message to the *console_loglevel* (a kernel variable) and if the priority is higher (i.e. a lower value) than the *console_loglevel* the message will be printed to the current console.

To determine your current *console_loglevel* you simply enter:

```
$ cat /proc/sys/kernel/printk
    7       4       1       7
    current default minimum boot-time-default 
```

The first integer shows you your current console_loglevel; the second the default log level that you have seen above.

To change your current console_loglevel simply write to this file, so in order to get all messages printed to the console do a simple

```
# echo 8 > /proc/sys/kernel/printk 
```

and every kernel message will appear on your console.

Another way to change the console log level is to use *dmesg* with the *-n* parameter

```
# #set console_loglevel to print KERN_WARNING (4) or more severe messages
# dmesg -n 5 
```

Only messages with a value lower (**not** lower equal) than the console_loglevel will be printed.

You can also specify the console_loglevel at boot time using the *loglevel* boot parameter. (see [Documentation/kernel-parameters.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux.git;a=blob;f=Documentation/kernel-parameters.txt;hb=HEAD#l1334) for more details)

* * *

**Author's practical advice:** Of course you should always specify an appropriate log level for your messages, but for **debugging**, I guess most developers leave the console_loglevel unchanged and simply use KERN_ERR or KERN_CRIT to ensure the message reaches the console.

```
pr_err("REMOVE ME: my debug statement that I swear to remove when I'm done\"); 
```

Please make sure to remove these 'inappropriately' tagged messages before shipping the module ;)

* * *

*KERN_CONT* and *pr_cont* are special cases since they do not specify an urgency but rather indicate a 'continued message' e.g.:

```
printk(KERN_ERR "Doing something was ");
/* <100 lines of whatever>*/
if (success)
   printk(KERN_CONT "successful\n");
else
   printk(KERN_CONT "NOT successful\n");

-> "Doing something was successful" 
```

**Important Note:** *KERN_CONT* and *pr_cont* should only be used by core/arch code during early bootup (a continued line is not SMP-safe otherwise).[[2]](http://lwn.net/Articles/252651/)

## Rate limiting and one time messages

Occasionally you have to insert a message in a section which gets called quite often. This not only might have a severe performance impact, it also could overwrite and spam your kernel buffer so it should be avoided.

As always the kernel already provides you with nice functions that solve your problems:

```
printk_ratelimited(...) 
```

and

```
printk_once(...) 
```

*printk_once* is fairly trivial - no matter how often you call it, it prints once and never again.

*printk_ratelimited* is a little bit more complicated - it prints by default not more than 10 times in every 5 seconds (for each function it is called in).

If you need other values for the maximum burst count and the timeout, you can always setup your own ratelimit using the *DEFINE_RATELIMIT_STATE* macro and the *__ratelimit* function - see the implementation of *printk_ratelimited* for an example.

Be sure to *#include \* in order to use *printk_ratelimited()*

Both functions have also their *pr_** equivalents like* pr_info_ratelimited *for* printk_ratelimited(KERN_INFO... *and* pr_crit_once *for* printk_once(KERN_CRIT...*

**Note: both did not work as expected in my tests here, will probably investigate further**

## Printk from userspace

Sometimes, especially when doing automated testing, it is quite useful to insert some messages in the kernel log buffer in order to annotate what's going on.

It is as simple as

```
# echo "Hello Kernel-World" > /dev/kmsg 
```

Of course this messages gets the default log level assigned, if you want e.g. to issue a KERN_CRIT message you have to use the string representation of the log level - in this case "2"

```
# echo "2Writing critical printk messages from userspace" >/dev/kmsg 
```

The message will appear like any other **kernel** message - there is **no way** to distinguish them!

**Note:** Don't confuse this with printf - we are printing a kernel message from userspace here.

If /dev/kmsg does not exist, it can be created with: 'mknod -m 600 /dev/kmsg c 1 11'

## Internals / Changing the size of the printk buffer

Printk is implemented by using a ring buffer in the kernel with a size of *__LOG_BUF_LEN* bytes where *__LOG_BUF_LEN* equals *(1 \<\<* CONFIG_LOG_BUF_SHIFT)*(see* [kernel/printk.c](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=kernel/printk.c;hb=HEAD) for details).

You can specify the size of the buffer in your kernel config by setting *CONFIG_LOG_BUF_SHIFT* to an appropriate value (e.g. 17 for 128Kb) (*make menuconfig -> General Setup -> Kernel log buffer size*).

Using a ring buffer implies that older messages get overwritten once the buffer fills up, but this is only a minor drawback compared to the robustness of this solution (i.e. minimum memory footprint, callable from every context, not many resources wasted if nobody reads the buffer, no filling up of disk space/ram when some kernel process goes wild and spams the buffer, ...). Using a reasonably large buffer size should give you enough time to read your important messages before they are overwritten.

**Note:** dmesg reads by default a buffer of max 16392 bytes, so if you use a larger logbuffer you have to invoke dmesg with the *-s* parameter e.g.:

```
### CONFIG_LOG_BUF_SHIFT 17 = 128k
$ dmesg -s 128000 
```

The kernel log buffer is accessible for reading from userspace by */proc/kmsg*. */proc/kmsg* behaves more or less like a FIFO and blocks until new messages appear.

**Please note** - reading from */proc/kmsg* consumes the messages in the ring buffer so they may not be available for other programs. It is usually a good idea to let *klogd* or *syslog* do this job and read the content of the buffer via *dmesg*.

## Pros and Cons

The main advantage of printk over other debugging solutions is that it requires no sophisticated setup and can be called anywhere from any time. Printk can be called while holding a lock, from interrupt and process context, is SMP safe and does not need any special preparation. It is just there and just works. The only precondition is that you have some kind of working console to display the messages.

For the early stages in the boot process, where no console is available yet, there is a special function named *early_printk*, this function writes directly to the VGA buffer or a serial line but otherwise works just like printk -- you have to enable this function by setting *CONFIG_EARLY_PRINTK* in your kernel config (*make menuconfig -> Kernel Hacking -> Early printk*).

The major drawback is that printk is quite static, so you have to figure out what you want to trace beforehand and if you want to trace something different you have to recompile your code - which can become quite cumbersome. (And of course printk is not interactive at all, so you can't modify any variables or the like.)

The other drawback is that printing usually consumes quite some processing power and io time, so if you're trying to debug a timing critical section or a timing bug, you're probably out of luck.

## Debugging early boot problems

### Accessing the printk buffer after a silent hang on boot

Sometimes, if the kernel hangs early in the boot process, you get no messages on the console before the hang. However, there may still be messages in the printk buffer, which can give you an idea of where the problem is.

The kernel starts putting messages into the printk buffer as soon as it starts. They stay buffered there until the console code has a chance to initialize the console device (often the serial port for embedded devices). Even though these messages are not printed before the hang, it is still possible in some circumstances to dump the printk buffer and see the messages.

The tricky parts of doing this are:

1.  using a warm reset (if possible) so the memory contents are not lost when transitioning from the stuck kernel to the bootloader. You *can* do a cold boot, but if the memory is left unpowered for very long, you will start to see memory corruption.
2.  figuring out the address to use in the bootloader, based on the address of __log_buf in System.map. You will probably need to subtract the value of CONFIG_PAGE_OFFSET from the __log_buf address. However, there may be other offsets present as well (such as TEXT_OFFSET). Sometimes you can find the buffer by dumping the memory in a suspected area and locating the kernel messages visually in the dump. Note that the mapping offset between the kernel map of memory and the bootloader map of memory should not change. So once you figure it out you are set.

Quinn Jensen writes:

Something I've found handy when the console is silent is to dump the printk buffer from the boot loader. To do it you have to know how your boot loader maps memory compared to the kernel.

#### Redboot example on a Freescale ADS board

Quinn says: Here's what I do with Redboot on i.MX31:

```
fgrep printk_buf System.map 
```

this shows the linked address of the printk_buf, e.g.:

```
c02338f0 b printk_buf.16194 
```

The address "c02338f0" is in kernel virtual, which, in the case of i.MX31 ADS, redboot will have mapped to 0x802338f0\. So, after resetting the target board, but without letting it try to boot again, at the redboot prompt:

```
dump -b 0x802338f0 -l 10000 
```

And you see the printk buffer that never got flushed to the UART. Knowing what's there can be **very** useful in debugging your console.

#### U-boot example on an OMAP OSK board

Tim Bird tried these steps and they worked:

```
grep __log_buf System.map 
```

or

```
grep __log_buf /proc/kallsyms 
```

These show:

```
c0352d88 B __log_buf 
```

In the case of the OSK, this address maps to 0x10352d88\. So I reset the target board and use the following:

```
OMAP5912 OSK # md 10352d88
10352d88: 4c3e353c 78756e69 72657620 6e6f6973    <5>Linux version
10352d98: 362e3220 2e32322e 612d3631 6e5f706c     2.6.22.16-alp_n
10352da8: 7428206c 64726962 6d697440 6b736564    l (tbird@timdesk
10352db8: 2e6d612e 796e6f73 6d6f632e 67282029    .am.sony.com) (g
10352dc8: 76206363 69737265 33206e6f 342e342e    cc version 3.4.4
10352dd8: 34232029 45525020 54504d45 65755420    ) #4 PREEMPT Tue
... 
```

#### Grub

Grub also supports a dump command which you can invoke from the grub prompt.

```
dump  [ -s offset ] [-n length] { FILE | (mem) } 
```

### Using CONFIG_DEBUG_LL and printascii() (ARM only)

If the kernel fails before the serial console is enabled, you can use CONFIG_DEBUG_LL to add extra debug output routines to the kernel. These are printascii, printch and printhex. These routines print directly to the serial port, bypassing the console code, and are available earlier in machine initialization.

To see printks earlier in the boot sequence (before the console is initialized), set CONFIG_DEBUG_LL=y and CONFIG_EARLY_PRINTK=y.

Alternatively, add your own calls to printascii, printch, and printhex where you believe the problems are located.

Here is an e-mail exchange seen on the linux-embedded mailing list (with answer by George Davis):[[3]](http://www.mail-archive.com/linux-embedded@vger.kernel.org/msg00223.html)

```
> When we try to boot a 2.6.21 kernel after uncompressing the kernel the boot process dies somehow.
> We've figured out so far that the kernel dies somewhere between the gunzip and start_kernel.

Try enabling DEBUG_LL to see if it's an machine ID error.  If you see:

Error: unrecognized/unsupported processor variant.

or:

Error: unrecognized/unsupported machine ID...

Then you either don't have proper processor support enabled for your target or your bootloader is passing in the wrong machine number.

If you still don't see anything, try hacking printk.c to call printascii() (enabled for the DEBUG_LL case) to print directly to the serial port w/o a driver, etc.,.  You can find more details on these low-level debugging hacks via a little googling... 
```

## NetConsole

Sometimes you are in the unlucky situation that the machine crashes or hangs and you have no way to access the console afterwards, e.g. the graphic driver hangs and and the kernel dies soon after. In this case you could either hook up a serial line to your crashing target machine (if a serial port is available) or use the kernels netconsole feature to enable printk logging via UDP.

In order to use it you have to enable the *CONFIG_NETCONSOLE* kernel config option (*make menuconfig -> Device Drivers -> Network device support -> Network core driver support -> Network console logging support*) and configure it appropriately by using the *netconsole* configuration parameter

```
netconsole=[src-port]@[src-ip]/[<dev>],[tgt-port]@<tgt-ip>/[tgt-macaddr]
    where
        src-port      source for UDP packets (defaults to 6665)
        src-ip        source IP to use (interface address)
        dev           network interface (eth0)
        tgt-port      port for logging agent (6666)
        tgt-ip        IP address for logging agent
        tgt-macaddr   ethernet MAC address for logging agent (broadcast) 
```

e.g. specify at kernel commandline (in your bootloader)

```
linux netconsole=4444@10.0.0.1/eth1,9353@10.0.0.2/12:34:56:78:9a:bc 
```

to send messages from 10.0.0.1 port 4444 via eth1 to 10.0.0.2 port 9353 with the MAC 12:34:56:78:9a:bc

or while loading the module e.g

```
# insmod netconsole netconsole=@/,@10.0.0.2/ 
```

to send messages via broadcast to 10.0.0.2 port 6666

On the other machine you can now simply fire up

```
# netcat -u -l -p <port> 
```

e.g.

```
$ netcat -u -l -p 6666 
```

and see the printk messages from your target dribbling in.

If you don't see any messages you might have to set the console_loglevel to a higher value (see above) or test the connection via telnet e.g. from the target type

```
$ telnet 10.0.0.2 6666 
```

### Netconsole resources

See [Documentation/networking/netconsole.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=Documentation/networking/netconsole.txt;hb=HEAD) for more details.

See [Sarah Sharp's blog entry about using netconsole](http://sarah.thesharps.us/2009-02-22-09-00)

## Misc

### Dmesg / Clearing the buffer

```
dmesg -c 
```

clears the dmesg buffer. Sometimes it is nice to start with a blank buffer, so you will only see new messages when you invoke *dmesg*

### Printk Timestamps

```
CONFIG_PRINTK_TIME 
```

Setting this kernel config option prepends every printk statement with a timestamp representing the time since boot. This is particularly useful to get a general idea about the timings of your code.

You can also specify an argument on the kernel command line to enable this, or you can enable it any time at runtime by doing the following:

```
 $echo 1 >/sys/module/printk/parameters/time 
```

Also, there are tools available to use the information to show relative times between printks (scripts/show_delta) and create graphs of durations in the kernel (scripts/bootgraph.pl)

See Printk Times for more details

### Printing buffers as hex

If you want to print a buffer as hex within the kernel, don't reinvent the wheel use *printk_hex_dump_bytes()* instead.

```
print_hex_dump_bytes(const char *prefix_str, int prefix_type, const void *buf, size_t len) 
```

this function prints a buffer as hex values to the kernel log buffer (with level KERN_DEBUG) Example:

```
Kernel Code:
char mybuf[] = "abcdef";
print_hex_dump_bytes("", DUMP_PREFIX_NONE, mybuf, ARRAY_SIZE(mybuf));

dmesg output:
61 62 63 64 65 66 00                             abcdef. 
```

If you need something more sophisticated and flexible maybe have a look at *print_hex_dump()* and *hex_dump_to_buffer()*

### Dynamic Debugging

It is also possible to enable/disable debug information at runtime using the dynamic debug functionality of the kernel. For this the *CONFIG_DYNAMIC_DEBUG* kernel config option must be set. See [Documentation/dynamic-debug-howto.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=Documentation/dynamic-debug-howto.txt;hb=HEAD) for more information.

## Disabling printk messages at compile time

There is a configuration option which allows you to turn off all the printk messages in the whole kernel (*CONFIG_PRINTK*). This reduces the size the kernel, usually by at least 100k, since all message strings are not compiled into the kernel binary image.

However, it also means you get absolutely no output from the kernel while it is running. Disabling kernel printk messages is usually the last thing you do when you are tuning your kernel for size.

## References and external links

*   [Linux Kernel Development](http://eLinux.org/Linux_Kernel_Development_-_by_Robert_Love "Linux Kernel Development - by Robert Love"), Robert Love, 3rd Edition, Chapter 18 Debugging
*   [Linux Device Drivers](http://eLinux.org/Linux_Device_Drivers "Linux Device Drivers"), Corbet, Rubini and Kroah-Hartmann, 3rd Edition, Chapter 4 Section 2
*   [Essential Linux Device Drivers](http://eLinux.org/Essential_Linux_Device_Drivers "Essential Linux Device Drivers")

*   [Documentation/printk-formats.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=Documentation/printk-formats.txt;hb=HEAD)
*   [Documentation/dynamic-debug-howto.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=Documentation/dynamic-debug-howto.txt;hb=HEAD)
*   [Documentation/networking/netconsole.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=Documentation/networking/netconsole.txt;hb=HEAD)
*   [kernel/printk.c](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=kernel/printk.c;hb=HEAD) Implementation of printk and others
*   [include/linux/printk.h](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux.git;a=blob;f=include/linux/printk.h;hb=HEAD) printk header file
*   [include/linux/kern_levels.h](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux.git;a=blob;f=include/linux/kern_levels.h;hb=HEAD) logging levels header file
*   [Blog Entry about the different %p format specifiers](http://www.embedded-bits.co.uk/2010/printk-format-specifiers/)
*   [LWN.net: The perils of pr_info()](http://lwn.net/Articles/487437/)
*   [Kernel logging: APIs and implementation, Tim Jones (for IBM)](http://www.ibm.com/developerworks/linux/library/l-kernel-logging-apis/index.html) (nice article)

Some page related to printk:

*   Printk Times - has information about how to turn on timestamps for each printk message
    *   printk time stamps sample
*   [printk size information](http://eLinux.org/Printk_Size_Info "Printk Size Info")
*   [Do Printk](http://eLinux.org/Do_Printk "Do Printk") - has information about a method of disabling printk messages on a per-module basis

[Categories](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")
*   [Tips and Tricks](http://eLinux.org/Category:Tips_and_Tricks "Category:Tips and Tricks")
*   [Printk](http://eLinux.org/Category:Printk "Category:Printk")

# Kernel Debugging Tips

> From: [eLinux.org](http://eLinux.org/Kernel_Debugging_Tips "http://eLinux.org/Kernel_Debugging_Tips")

# Kernel Debugging Tips

Here are some miscellaneous tips for debugging a kernel:

## Contents

*   1 Using printks
    *   1.1 Log levels
    *   1.2 Adding timing information
    *   1.3 Viewing log messages
    *   1.4 Controlling console output
    *   1.5 Changing the size of the printk buffer
*   2 Using kernel symbols
*   3 Using a kernel debugger
*   4 Debugging early boot problems
*   5 Triggering a kernel event
    *   5.1 Overloading the sync system call
*   6 Interpreting an Oops message
*   7 Compilation tricks for the kernel
    *   7.1 Build an individual file
    *   7.2 Create the preprocessed file for an individual source file
    *   7.3 Create the assembly file for an individual source file
    *   7.4 Alter the flags for a compilation

## Using printks

To add your own debug message to the kernel, you can place a "printk()" in the kernel code.

See [Debugging by printing -> Usage](http://eLinux.org/Debugging_by_printing#Usage "Debugging by printing") for more details.

### Log levels

Each kernel message can be pre-pended with a tag indicating the importance of the message.

See [Debugging by printing -> Log_Levels](http://eLinux.org/Debugging_by_printing#Log_Levels "Debugging by printing") for more details.

### Adding timing information

Sometimes, it is useful to add timing information to the printk values, so you can see when a particular event occurred. The kernel includes an feature for doing this called printk times.

See the help for CONFIG_PRINTK_TIMES in the file lib/Kconfig.debug for more information on this feature. This option is found on the "Kernel Hacking" menu when configuring the kernel.

The timestamps which are inserted into the printk output consist of seconds and microseconds, as absolute values from the start of machine operation (or from the start of kernel timekeeping).

There is also tool in the kernel source which will convert the timestamp values to relative values (so you can see the interval between events). This tools is called show_delta and is located in the kernel 'scripts' directory.

See Printk Times for more information.

### Viewing log messages

The `klogd` program will extract messages from the kernel log buffer, and send them to the system log (which winds up in /var/log/messages on most systems). This command runs in the background on most desktop or server systems, and continually transfers messages from the kernel log buffer to the system log.

You can view the contents of the log buffer directly, using the `dmesg` command. Note that by default `dmesg` displays the messages from the buffer, but does not remove them. So this command can be run multiple times to view the kernel printk messages. See the dmesg man page for more things you can do with this tool.

### Controlling console output

In order to have the kernel boot be less "noisy", or in order to boot more quickly, it is sometimes useful to control the amount of messages displayed to the console during boot. You can do this by setting the kernel log level at boot time via a kernel command line option. See the "loglevel=" argument in [`Documentation/kernel-parameters.txt`](https://www.kernel.org/doc/Documentation/kernel-parameters.txt).

You can turn off all messages using the kernel command line option "quiet". See Disable Console for information on how much time this can save at boot up.

Note that even if the log level is changed, or "quiet" is used, although the printk messages are not print to console, they are still entered into the log buffer, and they can still be extracted and displayed later using the `dmesg` command.

### Changing the size of the printk buffer

See [Debugging by printing -> Internals / Changing the size of the printk buffer](http://eLinux.org/Debugging_by_printing#Internals_.2F_Changing_the_size_of_the_printk_buffer "Debugging by printing")

## Using kernel symbols

You can look up the source code for a function address using your toolchain's addr2line program. See [Find a kernel function line](http://eLinux.org/Find_a_kernel_function_line "Find a kernel function line") or [Addr2line for kernel debugging](http://eLinux.org/Addr2line_for_kernel_debugging "Addr2line for kernel debugging").

## Using a kernel debugger

You can use the in-kernel debugger: KDB

You can use the in-kernel remote debugger: Kgdb

Also, you can use QEMU and gdb (and a high-level IDE like eclipse).

See [Debugging the Linux kernel using Eclipse/CDT and Qemu](http://issaris.blogspot.com/2007/12/download-linux-kernel-sourcecode-from.html) for a great article on using Eclipse (with the CDT plugin) to debug the Linux kernel.

## Debugging early boot problems

See [Debugging_by_printing#Debugging_early_boot_problems](http://eLinux.org/Debugging_by_printing#Debugging_early_boot_problems "Debugging by printing")

## Triggering a kernel event

### Overloading the sync system call

Sometimes, it is nice to trigger an event to happen in the kernel from user space. Instead of creating infrastructure to handle a /proc event, an ioctl() or making a new syscall, it can be quick and easy to just overload an existing function. One function not used very often is sync. (I have found that the sync system call is not normally called by user space programs (or during standard linux booting).

It is quite easy to put a hook to your own kernel program in the sys_sync() routine (located in fs/sync.c) and cause it to execute by issuing 'sync' from the shell command line. This is handy as a temporary mechanism to test things that you have put in the kernel.

## Interpreting an Oops message

When the kernel encounters an internal fault, it will print an Oops message. Here are some tips on using the Oops message to find the source of the problem.

*   See [Documentation/oops-tracing.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob_plain;f=Documentation/oops-tracing.txt;hb=HEAD)
*   See [HOWTO find oops location](http://vmlinux.org/jocke/linux/howto-find-oops-location.shtml) by Denis Vlasenko

## Compilation tricks for the kernel

Sometimes, you want to modify how the compiler builds an individual kernel file. The following are tips for doing tasks related to this.

### Build an individual file

You can build an individual output object file, with:

```
make fs/buffer.o 
```

This will build JUST fs/buffer.o (if it needs rebuilding) and not the entire kernel. To force it to need re-building, use 'touch' on the associated source file:

```
touch fs/buffer.c 
```

### Create the preprocessed file for an individual source file

Using the same technique, you can create the preprocessed file for a C source file. This is useful if you're having trouble tracking down macro expansion or where defines/prototypes are coming from exactly.

```
make fs/buffer.i 
```

### Create the assembly file for an individual source file

Using the same technique, you can create the assembly file for a C source file. This is useful to get an idea what actual machine instructions are generated from the C source code.

```
make fs/buffer.s 
```

Another way to get the raw assembly, is to dump the object file using 'objdump'

```
objdump -d fs/buffer.o > fs/buffer.disassem 
```

This will produce a disassembly of the object file, which should show how the assembly was translated into machine instructions.

If the object has been compiled with debug symbols (using '-g'), then you might get more information using the '-S' option with objdump:

```
objdump -S -d fs/buffer.o >fs/buffer.disassem 
```

You can also request that the toolchain show mixed source and assembly, by passing extra flags:

```
make EXTRA_CFLAGS="-g -Wa,-a,-ad -fverbose-asm" fs/buffer.o >fs/buffer.mixed 
```

### Alter the flags for a compilation

Sometimes, you need to alter the compilation flags for an individual file. There are two ways to do this. One is to add the extra flags on the make command line:

```
make EXTRA_CFLAGS="-g -finstrument-functions" fs/buffer.o 
```

This will work if the flags can be appended to the regular set of C flags used for compiling the object.

However, if you need to do something more complicated, like removing or modifying flags, then you can build your own command line by hand. To do this, it is easiest to have 'make' produce the default compilation command (which will be several lines long), then copy, paste and edit it, to run on the command line directly. To see the exact compile commands used to compile a particular object, use the V=1 option with the kernel build system:

```
make V=1 fs/buffer.o 
```

For me, this produced something like this:

mipsel-linux-gcc -Wp,-MD,fs/.buffer.o.d -nostdinc -isystem /home/usr/local/mipsel-linux-glibc/bin/../lib/gcc/mipsisa32el-linux/3.4.3/include -D__KERNEL__ -Iinclude -Iinclude2 -I/home/tbird/work/linux/include -I/home/tbird/work/linux/fs -Ifs -Wall -Wstrict-prototypes -Wno-trigraphs -fno-strict-aliasing -fno-common -ffreestanding -O2 -fomit-frame-pointer -g -I/home/tbird/work/linux/ -I /home/tbird/work/linux/include/asm/gcc -G 0 -mno-abicalls -fno-pic -pipe -finline-limit=100000 -mabi=32 -march=mips32r2 -Wa,-32 -Wa,-march=mips32r2 -Wa,-mips32r2 -Wa,--trap -I/home/tbird/work/linux/include/asm-mips/ati -Iinclude/asm-mips/ati -I/home/tbird/work/linux/include/asm-mips/mach-generic -Iinclude/asm-mips/mach-generic -Wdeclaration-after-statement -DKBUILD_BASENAME=buffer -DKBUILD_MODNAME=buffer -c -o fs/buffer.o /home/tbird/work/linux/fs/buffer.c

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Kgdb

> From: [eLinux.org](http://eLinux.org/Kgdb "http://eLinux.org/Kgdb")

# Kgdb

It is fascinating to think that you have control over running Linux Kernel. You can stop, can single-step, can resume and even can put break-points on running Kernel. In fact, you can debug the kernel as easily as you debug any application.

## Contents

*   1 How to setup kgdb
*   2 Hardware Requirements
*   3 Preparing Kernel to be Debugged
*   4 There are several possible problems that you may face
*   5 Using an IDE

## How to setup kgdb

The steps mentioned here are with reference to 2.6.26 Kernel. The main reason is KGDB code is merged into Linux tree from 2.6.26-RC5 kernel. (As a side note, for kernel \< 2.6.26-RC5, you have to get kgdb patch from [ [`kgdb.linsyssoft.com/kernel.htm`](http://kgdb.linsyssoft.com/kernel.htm)] and apply them to kernel)

## Hardware Requirements

Two x86 machines are required for using KGDB. One of the machines runs a kernel to be debugged called "TEST MACHINE". The other machine runs gdb "DEVELOPMENT MACHINE". A serial line is required between the development and the test machine. And so obviously, machines need one serial port each. Basically, you will be sending "Debugging Commands" from "DEVELOPMENT MACHINE" to "TEST MACHINE".

## Preparing Kernel to be Debugged

1.  Download the source of kernel (for e.g., 2.6.26.2)from kernel.org

2.  Recompile the Kernel on "DEVELOPMENT MACHINE". Go to Kernel Hacking and Enable the following options:

    -*- Magic SysRq key [*] Compile the kernel with debug info [*] KGDB: kernel debugging with remote gdb --->

And in KGDB:

```
--- KGDB: kernel debugging with remote gdb
<*>   KGDB: use kgdb over the serial console
[ ]   KGDB: internal test suite 
```

1.  Build kernel and modules

    ```
     make -j 12 && make modules && make modules_install 
    ```

2.  Transfer the vmlinux and system.map and initrd.img files on "TEST MACHINE".

3.  Now, edit the GRUB entry for that kernel on "TEST MACHINE". Add kernel options/kernel parameters like kgdbwait and kgdboc=ttyS0,115200

    ```
    For e.g., kernel /vmlinux ro root=LABEL=/ rhgb quiet crashkernel=128M@16M kgdbwait kgdboc=ttyS0,115200 
    ```

kgdbwait -- > This will make Kernel to wait on boot time and will expect someone to connect to it and give further commands kgdboc --> This is a KGDB I/O driver and we are supplying two arguments. ttyS0 will tell that communication will happen on Serial Port 0 and 115200 is the baudrate.

1.  Now boot the Kernel with those kernel parameters.

2.  On dev machine, start GDB session.

    ```
    [dev@einfochips]gdb vmlinux 
    ```

The argument vmlinux file is the file that is created with Debug symbols. It will be of much larger size and more than likely to be in the directory where you gave "make" command..

1.  Assuming that on "DEVELOPMENT MACHINE" you have set serial interface baudrate as 115200\. Connect to the "TEST MACHINE" with target command.

    ```
    (gdb)target remote /dev/ttyS0 
    ```

2.  This will stop your Kernel booting on "TEST MACHINE" and will give control to your "DEVELOPMENT MACHINE". Now, you can do Single-stepping or put breakpoints and etc.

3.  Once your Kernel is running on "TEST MACHINE" and you want control over your running kernel from "DEVELOPMENT MACHINE", You have to send MANUALLY on TEST machine SysRq command. So, on "TEST MACHINE" press SysRq + g

    (i.e., press "ALT" key then Press "PrintScreen" Key and then Press "g" key)

## There are several possible problems that you may face

1.  Your Kernel is booted and SysRq+g is not working.

    ```
    [r00t@einfochips] echo 1 > /proc/sys/kernel/sysrq 
    ```

This will enable sending SysRq commands.

1.  You may find some time that while stopping execution through SysRq key on "TEST MACHINE", it stops but then it is not able to communicate over serial cable with "DEVELOPMENT MACHINE". The reason can be, your KGDB I/O driver is not passed arguments properly and you may need to reconfigure the driver by following way,

    ```
    [root@einfochips]echo "ttyS0,115200" > /sys/modules/<module name>/parameters 
    ```

2.  The target board has a single serial port that needs to be shared between the console and kgdb.

    ```
    The kgdb Docbook refers to using a proxy to split the serial port data stream
    between gdb and the console terminal emulator.  proxy programs include:

      kgdb demux (kdmx) Media:Kdmx_v140904a.tar.gz
                         (moved to agent-proxy git)
      kgdb demux (kdmx)
                         kdmx at elinux.org
                         https://git.kernel.org/cgit/utils/kernel/kgdb/agent-proxy.git/
                         git clone git://git.kernel.org/pub/scm/utils/kernel/kgdb/agent-proxy.git/
      agent-proxy
                         https://git.kernel.org/cgit/utils/kernel/kgdb/agent-proxy.git/
                         git clone git://git.kernel.org/pub/scm/utils/kernel/kgdb/agent-proxy.git/

    (frowand: I have been using kdmx successfully.  I have not tried using agent-proxy.) 
    ```

## Using an IDE

You can debug Linux Kernel over KGDB with Visual Studio using the VisualKernel plugin. [This tutorial](http://visualkernel.com/tutorials/kgdb) shows how to use it with KGDB.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# KDB

> From: [eLinux.org](http://eLinux.org/KDB "http://eLinux.org/KDB")

# KDB

## Contents

*   1 Introduction and basic resources
    *   1.1 Older Information
*   2 General Information
    *   2.1 Kernel Versions supported
*   3 Kernel configuration
    *   3.1 Config variables
    *   3.2 Steps
*   4 Enabling kdb
    *   4.1 At runtime
    *   4.2 At boot time (via kernel command line)
*   5 Invoking kdb
    *   5.1 Invoking with Magic SysRq 'g'
    *   5.2 Invocation by panic
    *   5.3 Invocation by breakpoint
*   6 Using gdb to see the kernel source listing
*   7 KDB environment variables
*   8 Hints and tips
    *   8.1 Command line completion
    *   8.2 Defining a set of commands
    *   8.3 Executing commands on kdb initialization
    *   8.4 Piping command results through 'grep'
    *   8.5 Command history
*   9 Feature extension notes (by Tim)
    *   9.1 internals

## Introduction and basic resources

Here is some information about KDB - the in-kernel debugger for the Linux kernel.

The KDB and KGDB official wiki: [`kgdb.wiki.kernel.org/`](https://kgdb.wiki.kernel.org/) (this only has 2 pages?)

Jason Wessel is the current KDB maintainer. Here is a presentation from him at LinuxCon 2010 (August 2010): [`kernel.org/pub/linux/kernel/people/jwessel/dbg_webinar/State_Of_kernel_debugging_LinuxCon2010.pdf`](http://kernel.org/pub/linux/kernel/people/jwessel/dbg_webinar/State_Of_kernel_debugging_LinuxCon2010.pdf)

Here are some videos showing use of KDB and KGDB:

*   video 1 of 6: [`www.youtube.com/watch?v=V6Qc8ppJ_jc`](http://www.youtube.com/watch?v=V6Qc8ppJ_jc)
    *   example of a call to panic from a test module (without a debugger)
*   video 2 of 6: [`www.youtube.com/watch?v=LqAhY8K3XzI`](http://www.youtube.com/watch?v=LqAhY8K3XzI)
    *   example of catching the panic with KDB, and looking up the source line with gdb
*   video 3 of 6: [`www.youtube.com/watch?v=bBEh_UduX04`](http://www.youtube.com/watch?v=bBEh_UduX04)
    *   example of a bad access request, and looking up the source line with gdb
*   video 4 of 6: [`www.youtube.com/watch?v=MfJU2E0aJwg`](http://www.youtube.com/watch?v=MfJU2E0aJwg)
    *   example of using a hardware breakpoint with kdb
*   video 5 of 6: [`www.youtube.com/watch?v=sWiHV5mt8_k`](http://www.youtube.com/watch?v=sWiHV5mt8_k)
    *   use an address watch (hardware watchpoint) using kgdb (data access hardware breakpoint on tp_address_ref)
*   video 6 of 6: [`www.youtube.com/watch?v=nnopzcwvLTs`](http://www.youtube.com/watch?v=nnopzcwvLTs)
    *   use of kgdb over serial - Start up the agent-proxy and connect and hit a breakpoint a sys_sync

Documentation, up-to-date as of 2010, for KDB and KGDB is at: [`kernel.org/pub/linux/kernel/people/jwessel/kdb/`](http://kernel.org/pub/linux/kernel/people/jwessel/kdb/)

### Older Information

See [`www.ibm.com/developerworks/linux/library/l-kdbug/`](http://www.ibm.com/developerworks/linux/library/l-kdbug/) for a tutorial for the 2.4.20 kernel (from June 2003)

Here's an article from 2002 on KDB vs. KGDB: [`kerneltrap.org/node/112`](http://kerneltrap.org/node/112) It has a good discussion excerpt between Andrew Morton and Keith Owens about the relative merits of KDB versus KGDB.

## General Information

### Kernel Versions supported

kgdb was added to the mainline Linux kernel in version 2.6.26.

kdb support was added to the mainline Linux kernel in version 2.6.35.

Before those versions, kgdb and kdb were available as patches which could be applied to the Linux kernel source.

## Kernel configuration

The following descriptions are for a 2.6.35 kernel, using KDB over a serial line between host and target:

All these options on are the "Kernel Hacking" menu.

In order to support KDB, "KGDB" support must be turned on first (even if you aren't using kgdb/gdb)

### Config variables

*   CONFIG_DEBUG_KERNEL=Y - includes debug information in the kernel compilation - required for basic kernel debugging support
*   CONFIG_KGDB=Y - turn on basic kernel debug agent support
*   CONFIG_KGDB_SERIAL_CONSOLE=Y - to share a serial console with kgdb.
    *   Sysrq-g must be used to break in initially.
    *   Selecting this will automatically set:
        *   CONFIG_CONSOLE_POLL=Y
        *   CONFIG_MAGIC_SYSRQ=Y - turn on MAGIC-SYSRQ key support
*   CONFIG_KGDB_KDB=Y - actually turn on the KDB debugger feature
*   CONFIG_DEBUG_RODATA=N - you must disable this on x86 in order to set breakpoints on code or data

Optional other configuration settings:

*   CONFIG_FRAME_POINTER=Y - this allows for better backtrace support in KDB
*   CONFIG_KALLSYMS=Y - this adds symbolic information to the kernel, useful to see symbols instead of addresses
*   CONFIG_KDB_KEYBOARD - use KDB with an attached keyboard (not for use with serial console)
*   CONFIG_KGDB_TESTS - used to turn on kgdb internal self-tests - see the config help for this for more information

### Steps

To turn on KDB over serial console, do the following:

*   'make menuconfig'
    *   go to "Kernel Hacking" sub-menu
    *   turn on "KGDB: kernel debugger", and choose "\<selectu0005c class="calibre16">" to go to sub-menu</selectu0005c>
    *   verify that "KGDB: use kgdb over the serial console" is set
    *   set "KGDB_KDB: include kdb frontend for kgdb"
*   save and exit

Results:

```
[]$ diffconfig
KGDB n -> y
+CONSOLE_POLL y
+KDB_KEYBOARD n
+KGDB_KDB y
+KGDB_SERIAL_CONSOLE y
+KGDB_TESTS n 
```

## Enabling kdb

### At runtime

Once the kernel is compiled with kdb support and is running on your target board, you need to enable it. This can be done on a running system, binding the kdb/kgdb feature to a serial port, by writing a value into the sys filesystem.

If your machine starts a serial console on ttyS0, you can bind kdb/kgdb to this serial console by writing the string "ttyS0" to /sys/module/kgdboc/parameters/kgdboc. The kernel will respond with a message indicating that that driver is registered.

```
$ echo ttyS0 >/sys/module/kgdboc/parameters/kgdboc
kgdb: Registered I/O driver kgdboc. 
```

### At boot time (via kernel command line)

You can enable kdb support in your kernel at boot time by using the 'kgdboc' option on the kernel command line. Normally, you specify the tty device name, followed by the serial port speed.

```
kgdboc=ttyS0,115200 
```

## Invoking kdb

Once the kernel is running, and the kgdb/kdb is bound to the serial console, you can invoke the debugger in numerous ways.

First, you can enter the debugger using a [Magic SysRq](http://en.wikipedia.org/wiki/Magic_SysRq_key) command.

kdb will also be entered automatically if the kernel panics.

Finally, you can set a breakpoint (either hardware or software), such that the debugger is invoked when the breakpoint condition is met. For a code breakpoint, this means when the instruction is executed at the breakpoint location, and for a data breakpoint, when the particular access is made at the breakpoint address.

### Invoking with Magic SysRq 'g'

To invoke the debugger using the Magic SysRq command, you use the 'g' command, which can be issued any of the ways supported by the Magic SysRq feature. This can be done by 1) typing the key sequence on a connected keyboard, 2) echoing a value to /proc/sysrq-trigger, or 3) sending a break key sequence through the serial console

Examples:

*   sysrq trigger from local shell, via procfs: 'echo g >/proc/sysrq-trigger'
*   sysrq trigger via serial console break sequence:
    *   in minicom, type 'ctrl-a f g' (quickly)
    *   in telnet, through a server that supports sending a break: type

### Invocation by panic

When a kernel panic occurs, then something has gone seriously wrong and the kernel automatically enters kdb. From here you can look at memory, do a traceback, examine registers, and do other operations to find out more about the state of the system and debug the problem.

### Invocation by breakpoint

To enter kdb using a breakpoint, first invoke kdb using the Magic SysRq key (see above), then set a breakpoint. Then type 'go' to continue execution. When the breakpoint is hit, the debugger shell will appear.

In the example that follows, items in italics are commands typed by a user. Items following a '$' are commands entered at a shell command (normal Linux user-space runtime), and items following 'kgdb>' are commands entered at the kdb interactive shell.

```
$ echo g >/proc/sysrq-trigger
SysRq : DEBUG

Entering kdb (current=0xdfdff040, pid 71) due to Keyboard Entry
kdb> bp sys_sync+4
Instruction(i) BP #0 at 0xc00c9f00 (sys_sync+0x4)
   is enabled  addr at 00000000c00c9f00, hardtype=0 installed=0

kdb> go
$ sync

Entering kdb (current=0xdfdaa360, pid 72) due to Breakpoint @ 0xc00c9f00
kdb> bt
Stack traceback for pid 72
0xdfdaa360       72       71  1    0   R  0xdfdaa560 *sync
[<c0028cb4>] (unwind_backtrace+0x0/0xe4) from [<c0026d50>] (show_stack+0x10/0x14)
[<c0026d50>] (show_stack+0x10/0x14) from [<c0079e78>] (kdb_show_stack+0x58/0x80)
[<c0079e78>] (kdb_show_stack+0x58/0x80) from [<c0079f1c>] (kdb_bt1.clone.0+0x7c/0xcc)
[<c0079f1c>] (kdb_bt1.clone.0+0x7c/0xcc) from [<c007a240>] (kdb_bt+0x2d4/0x338)
[<c007a240>] (kdb_bt+0x2d4/0x338) from [<c0078328>] (kdb_parse+0x4d4/0x5f8)
[<c0078328>] (kdb_parse+0x4d4/0x5f8) from [<c0078a8c>] (kdb_main_loop+0x448/0x6b0)
[<c0078a8c>] (kdb_main_loop+0x448/0x6b0) from [<c007acb4>] (kdb_stub+0x210/0x398)
[<c007acb4>] (kdb_stub+0x210/0x398) from [<c0073280>] (kgdb_handle_exception+0x384/0x574)
[<c0073280>] (kgdb_handle_exception+0x384/0x574) from [<c0028518>] (kgdb_brk_fn+0x18/0x20)
[<c0028518>] (kgdb_brk_fn+0x18/0x20) from [<c0022198>] (do_undefinstr+0x10c/0x1a8)
[<c0022198>] (do_undefinstr+0x10c/0x1a8) from [<c0022b84>] (__und_svc+0x44/0x60)
Exception stack(0xdfe09f58 to 0xdfe09fa0)
9f40:                                                       00000000 bec93e74
9f60: 000437ac 00034738 00000001 bec93e74 00000049 00000024 c00230e8 dfe08000
9f80: 00000000 bec93e54 00000000 dfe09fa0 c0022f40 c00c9f00 80000013 ffffffff
[<c0022b84>] (__und_svc+0x44/0x60) from [<c00c9f00>] (sys_sync+0x4/0x28)
[<c00c9f00>] (sys_sync+0x4/0x28) from [<c0022f40>] (ret_fast_syscall+0x0/0x30)
kdb> 
```

This example shows an invocation of kdb, followed by setting a breakpoint, then resuming execution with 'go'. Then, at the Linux user-space shell, the 'sync' command is run to cause the breakpoint to occur. When kdb is entered due to the breakpoint, then 'bt' is run to get a backtrace from the stack of the current process.

## Using gdb to see the kernel source listing

You can use the addresses printed out in kdb, with a host-side gdb session, to see the source code or assembly instructions around a particular address.

The target address can come from a backtrace or register dump (e.g. instruction pointer).

To load the source for a kernel, start gdb (or the appropriate arch-specific gdb) with the vmlinux that matches the image running on target. The kernel should have been compiled with debug symbols (CONFIG_DEBUG_KERNEL=y). gdb will start, and load the symbol information for the kernel.

Use the following commands to see various bits of information:

*   source file and line number for an instruction address
    *   *info line \*0x\<targetu0005c_addru0005c class="calibre16">*</targetu0005c_addru0005c>
*   source lines around an instruction address
    *   *list \*0x\<targetu0005c_addru0005c class="calibre16">*</targetu0005c_addru0005c>
*   assembly instructions at an address
    *   *disas 0x\<targetu0005c_addru0005c class="calibre16"></targetu0005c_addru0005c>*, or
    *   *x/20i 0x\<targetu0005c_addru0005c class="calibre16"></targetu0005c_addru0005c>*

## KDB environment variables

Here are some environment variables supported by kdb (in 2.6.35):

*   LINES - set the number of lines for paging output from KDB (default 24)
*   BTAPROMPT - prompt after each process in bta command (default 0)
*   LOGGING - if 1, echo all KDB output to the kernel log buffer (printk log) (default 0)
*   PROMPT - string to use for kdb interactive shell prompt (default '[%d]kdb>' on SMP or 'kdb>' on UP)
*   MOREPROMPT - string to use for kdb paged output prompt (default '[%d]more>' on SMP or 'more>' on UP)
*   RADIX - base used to print out (default 16)
*   MDCOUNT - number of lines of data for md command if not specified (default 8)
*   BYTESPERWORD - number of bytes per word for md command (default 4)
*   DTABCOUNT - number of symbols to display for tab completion (default 30)
*   NOSECT - avoid displaying section information with md commands (default 1)
*   PS - specify a list of task states (one letter per state) for filtering the 'ps' command (default 'DRSTCZEU')
    *   letters can be used from the list: DRSTCZEUIMA, each letter corresponding to a task state (see the PS man page for task states)
        *   D = uninterruptible sleep
        *   R = running
        *   S = interruptible sleep
        *   T = stopped
        *   C = traced
        *   Z = exited and zombied
        *   E = exited and dead
        *   U = unrunnable
        *   I = idle
        *   M = daemon
        *   A = all

Unused args (in 2.6.35):

*   BTARGS - number of arguments to show in bt command (default 9)
    *   is used for argcount for internal routine kdb_bt1, but argcount is never used in that routine
*   BTSYMARG - ??? [set in kdb_cmds 'dumpall' and 'dumpcpu', but not used in code)
    *   appears to be an integer

## Hints and tips

### Command line completion

kdb supports command line completion using the tab key. While typing at the interactive shell, you can press the tab key to get a list of symbols to use as part of the command. If you type the first few letters of a symbol name, then press the tab key, the shell will complete the name for you. If more than one symbol matches what you have typed, then a list is shown of matching symbols. The number of symbols shown is controlled by the DTABCOUNT variable.

### Defining a set of commands

You can define a set of commands to be run as a group (in sequence), using the 'defcmd' command. This group essentially becomes a new command which you can run, using the indicated name.

The second two strings after the new command name are the Usage string and the Description string. These appear in the online help when the 'help' or '?' commands are run.

Here is an example of the definition of a new command:

```
defcmd dumpcommon "" "Common kdb debugging"
  set BTAPROMPT 0
  set LINES 10000
  -summary
  -cpu
  -ps
  -dmesg 600
  -bt
endefcmd 
```

A leading dash indicates to ignore any errors processing a command.

### Executing commands on kdb initialization

In the kernel source, you can place commands in the file 'kdb_cmds'. These commands will be executed by kdb on it's initialization. By default, a few command sets are declared: 'dumpcommon', 'dumpall', and 'dumpcpu'.

### Piping command results through 'grep'

kdb includes a grep-like capability, which provides the ability to filter command results using a pattern. To use this, type the regular command, and at the end of the line add '| grep \<patternu0005c class="calibre16">', where pattern is the pattern to match.</patternu0005c>

To see online help for this feature, type 'grephelp' at the kdb shell prompt.

The pattern may include a very limited set of metacharacters:

```
 pattern or ^pattern or pattern$ or ^pattern$ 
```

And if there are spaces in the pattern, you may quote it:

```
 "pat tern" or "^pat tern" or "pat tern$" or "^pat tern$" 
```

### Command history

You can access the command history by typing up-arrow and down-arrow, and select a previous command to run.

If you hit \ <enteru0005c class="calibre16">without typing a command, sometimes (when it makes sense) the last command run will be run again. For 'md' commands, the next command will continue at the address where the last command left off.</enteru0005c>

## Feature extension notes (by Tim)

This section just has some random notes on Tim Bird's investigation of KDB (for kernel version 2.6.35)

Regular users of KDB can ignore this...

*   Can break on __do_user_fault, to break into kdb on user process SIGSEGV (on ARM, anyway)
*   See CONFIG_DEBUG_USER (on ARM) for extra information shown and code paths used, on user-space faults

*   I can't set a breakpoint from kdb_cmds (on kdb initialization)

    *   I get a hang (likely a panic before the console starts) on any breakpoint set on kdb initialization
        *   how to debug: 1) printk and logbuf visibility over reset? 2) use of qemu??

### internals

internal functions to retrieve environment variables:

*   kdbgetintenv()
*   kdbgetenv()
*   kdbgetulenv() - get unsigned long env variable

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Kdmx

> From: [eLinux.org](http://eLinux.org/Kdmx "http://eLinux.org/Kdmx")

# Kdmx

## Contents

*   1 overview
*   2 source location
*   3 example 1 (simple)
*   4 example 2 (automate paths of gdb pty and terminal emulator pty)

## overview

If the target board has a single serial port then a proxy on the host is needed to be share the target port between the console and kgdb.

kdmx (kgdb demux) is one of the available proxy programs.

## source location

The source is located in the agent-proxy git

```
 https://git.kernel.org/cgit/utils/kernel/kgdb/agent-proxy.git/
 git clone git://git.kernel.org/pub/scm/utils/kernel/kgdb/agent-proxy.git/ 
```

## example 1 (simple)

This is a simple example, where I am using a USB serial port to connect between my host and target.

I did all of the commands in "host window 1" before I did the commands in "host window 2".

I did all of the commands in "host window 2" before I did the commands in "host window 3".

```
+---------------------  HOST  -------------------------------------+                +--  TARGET  --+
|                                                                  |                |              |
|                                                                  |                |              |
v                                                                  v                v              v

terminal  <---->  /dev/pty/29  <---->  +------+
emulator                               |      |
                                       | kdmx |  <---->  serial port  <== cable ==> target console
gdb  <--------->  /dev/pty/53  <---->  |      |          /dev/ttyUSB0
                                       +------+ 
```

The absolute path of the kernel source directory is in ${LINUX_SOURCE}.

The path of the kernel build directory, relative to the kernel source directory, is in ${KBUILD_OUTPUT}

```
$ echo ${LINUX_SOURCE}
/xxx/linux--3.17

$ echo ${KBUILD_OUTPUT}
../build/dragon_linus_3.17

$ echo ${LINUX_SOURCE}/${KBUILD_OUTPUT}
/xxx/linux--3.17/../build/dragon_linus_3.17

-----  host window 1 - kdmx  -----

$ export PS1='[1]: '
[1]: ./kdmx -n -d -p/dev/ttyUSB0 -b115200
serial port: /dev/ttyUSB0
Initalizing the serial port to 115200 8n1
/dev/pts/29 is slave pty for for terminal emulator
/dev/pts/53 is slave pty for gdb

-----  host window 2 - terminal emulator  -----

# In this example, I am using the sysrq debug command instead of the
# kernel command line 'kgdbwait' option to enter kgdb.
#
# The minicom "-o" option tells it not to initialize, so it does not
# send the "Init string" (defaults to "At S7=45 S0=0...") when you run
# minicom.

$ export PS1='[2]: '
[2]: minicom -o -w -p /dev/pts/29
Welcome to minicom 2.5

OPTIONS: I18n
Compiled on May  2 2011, 10:05:24.
Port /dev/tty8

Press CTRL-A Z for help on special keys

# Now in minicom, connected to the target console.
# CONFIG_PRINTK_TIME=y, so timestamps will be added to
# printk() messages to the console.

$ export PS1='% '
% cat /proc/version
Linux version 3.17.0-dirty (frank@ussvlx) (gcc version 4.6.x-google 20120106 (prerelease) (GCC) ) #1 SMP PREEMPT Mon Oct 6 10:19:37 PDT 2014
% echo g >/proc/sysrq-trigger
[   24.246292] SysRq : DEBUG
[   24.247184] Entering KGDB

-----  host window 3 - gdb  -----

$ export PS1='[3]: '
[3]: echo ${CROSS_COMPILE}
arm-eabi-
[3]: cd ${LINUX_SOURCE}
[3]: strings $KBUILD_OUTPUT/vmlinux | grep "^Linux version"
Linux version 3.17.0-dirty (frank@ussvlx) (gcc version 4.6.x-google 20120106 (prerelease) (GCC) ) #1 SMP PREEMPT Mon Oct 6 10:19:37 PDT 2014
Linux version
[3]: ${CROSS_COMPILE}gdb $KBUILD_OUTPUT/vmlinux
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "--host=x86_64-linux-gnu --target=arm-linux-android".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /xxx/git_linus/build/dragon_linus_3.17/vmlinux...done.
(gdb) target remote  /dev/pts/53
Remote debugging using /dev/pts/53
kgdb_breakpoint ()
    at /xxx/git_linus/linux--3.17/kernel/debug/debug_core.c:1050
1050            arch_kgdb_breakpoint();
(gdb) b sys_sync
Breakpoint 1 at 0xc031d450: file /xxx/git_linus/linux--3.17/fs/sync.c, line 103.
(gdb) c
Continuing. 
```

## example 2 (automate paths of gdb pty and terminal emulator pty)

Keeping track of the paths of the pty slaves created by kdmx can be annoying, so the status file feature has been added in kdmx version 141210a to automate use of the paths by gdb and the terminal emulator.

The status file feature is enabled with the '-s' option, which will create files containing the paths of the gdb slave pty and the terminal emulator pty. Each time a slave pty is opened, the contents of the respective status file is updated. The '-s' option provides the path, including a root for the file name, for the location of the status files. The strings '_gdb' and '_trm' will be appended to the path to form the names of the status files.

The absolute path of the kernel source directory is in ${LINUX_SOURCE}.

The path of the kernel build directory, relative to the kernel source directory, is in ${KBUILD_OUTPUT}

```
$ echo ${LINUX_SOURCE}
/xxx/linux--3.18

$ echo ${KBUILD_OUTPUT}
../build/dragon_linus_3.18

$ echo ${LINUX_SOURCE}/${KBUILD_OUTPUT}
/xxx/linux--3.18/../build/dragon_linus_3.18 
```

In this example, I place the status files in my kernel build directory.

```
-----  host window 1 - kdmx  -----

$ export PS1='[1]: '
[1]: kdmx -n -d -p/dev/ttyUSB0 -b115200 -s${LINUX_SOURCE}/${KBUILD_OUTPUT}/zzz__kdmx_pty
gdb status file: /xxx/linux--3.18/../build/dragon_linus_3.18/zzz__kdmx_pty_gdb
terminal emulator status file: /xxx/linux--3.18/../build/dragon_linus_3.18/zzz__kdmx_pty_trm
serial port: /dev/ttyUSB0
Initalizing the serial port to 115200 8n1
/dev/pts/44 is slave pty for terminal emulator
/dev/pts/45 is slave pty for gdb

Use <ctrl>C to terminate program 
```

Here are the contents of the status files:

```
$ ls -l ${LINUX_SOURCE}/${KBUILD_OUTPUT}/zzz__kdmx_pty*
-rw-r----- 1 frank frank_group 12 Dec 10 22:07 /xxx/linux--3.18/../build/dragon_linus_3.18/zzz__kdmx_pty_gdb
-rw-r----- 1 frank frank_group 12 Dec 10 22:07 /xxx/linux--3.18/../build/dragon_linus_3.18/zzz__kdmx_pty_trm
$ for k in `ls ${LINUX_SOURCE}/${KBUILD_OUTPUT}/zzz__kdmx_pty*`; do echo "$k:" ; cat $k; echo ; done
/xxx/linux--3.18/../build/dragon_linus_3.18/zzz__kdmx_pty_gdb:
/dev/pts/45

/xxx/linux--3.18/../build/dragon_linus_3.18/zzz__kdmx_pty_trm:
/dev/pts/44 
```

The terminal emulator slave pty path is retrieved from the proper status file and provided to minicom:

```
-----  host window 2 - terminal emulator  -----

$ export PS1='[2]: '
[2]: minicom -o -w -p `cat ${LINUX_SOURCE}/${KBUILD_OUTPUT}/zzz__kdmx_pty_trm`
Welcome to minicom 2.5

OPTIONS: I18n
Compiled on May  2 2011, 10:05:24.
Port /dev/tty8

Press CTRL-A Z for help on special keys

$ export PS1='% '
% cat /proc/version
Linux version 3.18.0-dirty (frank@ussvlx) (gcc version 4.6.x-google 20120106 (prerelease) (GCC) ) #1 SMP PREEMPT Mon Dec 8 12:27:26 PST 2014
% echo g > /proc/sysrq-trigger
[210227.044514] SysRq : DEBUG 
```

The task of getting the path of the gdb slave pty into gdb is a little bit more complicated. (Any suggestions of a more straightforward method are welcome.)

The following shell script is used to invoke the proper gdb and create a user defined gdb command to connect to the correct pty path. The ${CROSS_COMPILE} ensures that the gdb built for the target architecture is used.

```
#! /bin/bash

kgdb_set_pty_cmd=`mktemp --tmpdir kgdb_set_pty_cmd.XXXXXXXXXX` || exit 1

# user defined command
#
#   \x5c is '\'
#
echo -e \
   "define tr"                                                                 \
   "\ndont-repeat"                                                             \
   "\necho target remote"                                                      \
   "`cat ${LINUX_SOURCE}/${KBUILD_OUTPUT}/zzz__kdmx_pty_gdb`"                  \
   "\x5cn"                                                                     \
   "\ntarget remote"                                                           \
   `cat ${LINUX_SOURCE}/${KBUILD_OUTPUT}/zzz__kdmx_pty_gdb`                    \
   "\nend"                                                                     \
   "\n"                                                                        \
   "\ndocument tr"                                                             \
   "\ntarget remote to the kdmx gdb pty"                                       \
   "\n"                                                                        \
   "\nThe kdmx gdb pty to be used is: "                                        \
   `cat ${LINUX_SOURCE}/${KBUILD_OUTPUT}/zzz__kdmx_pty_gdb`                    \
   "\nThe kdmx gdb pty was determined at gdb startup.  If the kdmx gdb pty"    \
   "\nhas changed since then, restart gdb"                                     \
   "\n"                                                                        \
   "\nend"                                                                     \
   > ${kgdb_set_pty_cmd}

echo "${CROSS_COMPILE}gdb -x ${kgdb_set_pty_cmd} $KBUILD_OUTPUT/vmlinux"
      ${CROSS_COMPILE}gdb -x ${kgdb_set_pty_cmd} $KBUILD_OUTPUT/vmlinux

rm ${kgdb_set_pty_cmd}

-----  host window 3 - gdb  -----

$ export PS1='[3]: '
[3]: echo ${CROSS_COMPILE}
arm-eabi-
[3]: cd ${LINUX_SOURCE}
[3]: tcgdb
arm-eabi-gdb -x /tmp/kgdb_set_pty_cmd.4xKNf2ah0T ../build/dragon_linus_3.18/vmlinux
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "--host=x86_64-linux-gnu --target=arm-linux-android".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /local/nobackup/src/git_linus/build/dragon_linus_3.18/vmlinux...done.
(gdb) help tr
target remote to the kdmx gdb pty

The kdmx gdb pty to be used is:  /dev/pts/45
The kdmx gdb pty was determined at gdb startup.  If the kdmx gdb pty
has changed since then, restart gdb

(gdb) tr
target remote /dev/pts/45
kgdb_breakpoint () at /local/nobackup/src/git_linus/linux--3.18/kernel/debug/debug_core.c:1050
1050        arch_kgdb_breakpoint();
(gdb) b sys_sync
Breakpoint 1 at 0xc031f7b4: file /local/nobackup/src/git_linus/linux--3.18/fs/sync.c, line 103.
(gdb) c
Continuing. 
```

# Debugging The Linux Kernel Using Gdb

> From: [eLinux.org](http://eLinux.org/Debugging_The_Linux_Kernel_Using_Gdb "http://eLinux.org/Debugging_The_Linux_Kernel_Using_Gdb")

# Debugging The Linux Kernel Using Gdb

## Contents

*   1 Debugging the linux kernel using gdb
    *   1.1 Requirements
    *   1.2 The basics
        *   1.2.1 vmlinuz v.s zImage
        *   1.2.2 Debugging the kernel
        *   1.2.3 Loading a kernel in memory
        *   1.2.4 Getting the kernel log buffer
        *   1.2.5 Debugging a kernel module (.o and -ko )
    *   1.3 Determining the module load address
    *   1.4 Loading Symbols of a loaded module
    *   1.5 Pointers

# Debugging the linux kernel using gdb

The majority of day to day kernel debugging is done by adding print statements to code by using the famous printk function. This technique is well described in Kernel Debugging Tips. Using printk is a relatively simple, effective and cheap way to find problems. But there are also many other Linux-grown techniques that take debugging and profiling to a higher level. On this page, we will discuss using the GNU debugger (GDB) to do kernel debugging. The [GDB](http://eLinux.org/GDB "GDB") page describes some basic gdb command and also gives good links to documentation. Overall, starting using gdb to do kernel debugging is relatively easy.

Most of the examples here will work in two (open source) situations. When using [JTAG](http://eLinux.org/JTAG "JTAG") and when using [QEMU](http://eLinux.org/Qemu "Qemu") system emulation. As the second option does not require any hardware, you can go on and try it right away!

The open source JTAG debugging world is not that big. One project that stands out in terms of debugging capabilities is OpenOCD and this is the tool used in this documentation. [OpenOCD](http://openocd.berlios.de/web/) is pretty usable on the ARM11 and ARM9 targets we tested.

## Requirements

GDB:

You need to get a GDB that is capable of understanding your target architecture. Often, this comes with your cross-compiler, but if you have to, you can compile it yourself, but you need to understand the difference between --target and --host configure options. GDB will be running on the host (e.g. x86) but will be able to understand the target (e.g. armv6). You may also want to have the gdbserver that can serve as a stub for your user land debugging.

OpenOCD:

TODO...

A JTAG Dongle:

TODO...

## The basics

![Kernel gdb debugging component overvierw
small.png](http://eLinux.org/File:Kernel_gdb_debugging_component_overvierw_small.png)

To debug the kernel, you will need to configure it to have debug symbols. Once this is done, you can do your normal kernel development. When needed, you can "hook-up" your debugger. Start debugging a running kernel.

*   start openocd

### vmlinuz v.s zImage

When you want to debug the kernel you need a little understanding of how the kernel is composed. Most important is the difference between your vmlinux and the zImage. What you need to understand at this point is that the zImage is a container. This container gets loaded by a boot loader and that execution is handed over to the zImage. This zImage unpacks the kernel to the same memory location and starts executing the kernel. (explain that vmlinux does not have to be the real kernel as it is possible to debug a "stripped" kernel using a non stripped vmlinux). overall if we look at a compiled kernel we will see that vmlinux is located at the root of the kernel tree whiles the zImage is located under arch/arm/boot

```
vmlinux
arch/arm/boot
`-- zImage 
```

vmlinux is what we will be using during debugging of the Linux kernel.

### Debugging the kernel

The JTAG based debugging method described here is not intrusive. This means that besides debugging symbols you don't need to modify the kernel in any way. This is because we operate on the hardware, CPU core level. Overall this means that you can follow your normal development method. You can let your bootstrap and boot loader do their work and for example start debugging a running kernel. If your gdb-aware debugger is running it can be as simple as loading the vmlinuz and connecting to the remote target:

```
load vmlinuz
target remote :3333 
```

### Loading a kernel in memory

Once you are used to using gdb to debug kernels you will want to use gdb to directly load kernels onto your target. The most practical way of doing this is to set a hardware breakpoint at the start of the kernel and reset your board using the JTAG reset signal. Your boot loader will initialize your board and the execution will stop at the start of the kernel. After that you can load a kernel into memory and run it.

Execute the following:

```
(gdb) file vmlinux
(gdb) target remote :3333
(gdb) break __init_begin
(gdb) cont
(gdb) mon reset #perhaps this needs to be done from the openocd telnet session..
Breakpoint 1, 0xc0008000 in stext ()
(gdb) load vmlinux
Loading section .text.head, size 0x240 lma 0xc0008000
Loading section .init, size 0xe4dc0 lma 0xc0008240
Loading section .text, size 0x219558 lma 0xc00ed000
Loading section .text.init, size 0x7c lma 0xc0306558
Loading section __ksymtab, size 0x4138 lma 0xc0307000
Loading section __ksymtab_gpl, size 0x1150 lma 0xc030b138
Loading section __kcrctab, size 0x209c lma 0xc030c288
Loading section __kcrctab_gpl, size 0x8a8 lma 0xc030e324
Loading section __ksymtab_strings, size 0xc040 lma 0xc030ebcc
Loading section __param, size 0x2e4 lma 0xc031ac0c
Loading section .data, size 0x1e76c lma 0xc031c000
Start address 0xc0008000, load size 3345456
Transfer rate: 64 KB/sec, 15632 bytes/write.
(gdb) cont 
```

This will boot your kernel that was loaded into memory via JTAG.

### Getting the kernel log buffer

Sometimes the kernel will panic before the serial is up and running. In such situations is it **very** handy to be able to dump the kernel log buffer. This can be done by looking at the content of the __log_buf in the kernel. In gdb this can be done by issuing

```
p (char*) &__log_buf[log_start] 
```

There must be a simple way of printing the memory area between log_start and log_end.

The problem is that gdb stops after the first line. Currently we use this routine that copied from wchar.gdb until something "normal" came out. We defined dmesg it like this:

```
define dmesg
        set $__log_buf = $arg0
        set $log_start = $arg1
        set $log_end = $arg2
        set $x = $log_start
        echo "
        while ($x < $log_end)
                set $c = (char)(($__log_buf)[$x++])
                printf "%c" , $c
        end
        echo "\n
end
document dmesg
dmesg __log_buf log_start log_end
Print the content of the kernel message buffer
end 
```

and call it like this:

```
dmesg __log_buf log_start log_end 
```

### Debugging a kernel module (.o and .ko )

Debugging a kernel module is harder.

## Determining the module load address

gdb itself does not have knowledge about kernel modules and when debugging a kernel module. We will need to help gdb a little. One problem with modules is that it is not possible to determine where in the memory a module will be loaded before it is actually loaded so only once it is loaded we need to determine the address in memory it is loaded and tell gdb about it. There are many ways of determining this information. Here are 3 ways:

```
lsmod

cat /sys/module/mydriver/sections/.text

 #gdb implementation of the linux lsmod
 define lsmod
        set $current = modules.next
        set $offset =  ((int)&((struct module *)0).list)
    printf "Module\tAddress\n"

    while($current.next != modules.next)
                printf "%s\t%p\n",  \
                        ((struct module *) (((void *) ($current)) - $offset ) )->name ,\
                        ((struct module *) (((void *) ($current)) - $offset ) )->module_core
                set $current = $current.next
        end
end 
```

## Loading Symbols of a loaded module

```
add-symbol-file drivers/mydrivers/mydriver.o 0xbf098000 
```

Note that we use the .o file and not the .ko one. The address at the end is the address where the kernel decided to load the module

## Pointers

In the Linux Documentation directory under the kdump you will find file called gdbmacros.txt and it looks very promising as among other things it contains the a dmesg implementation

```
head  linux-2.6.22.1/Documentation/kdump/gdbmacros.txt
#
# This file contains a few gdb macros (user defined commands) to extract
# useful information from kernel crashdump (kdump) like stack traces of
# all the processes or a particular process and trapinfo.
# 
```

A "find . -name "*gdb*" in the linux kernel directory also shows up a few interesting .gdbinit files that apparently can perform low level initialization.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# MagicSysRq

> From: [eLinux.org](http://eLinux.org/MagicSysRq "http://eLinux.org/MagicSysRq")

# MagicSysRq

* * *

**Note**: This article is currently only a draft and is a part of a series of articles I'm going to publish the next few months - if you want to contribute to it, please feel free to. However it would be nice if you could coordinate your efforts with [me](http://eLinux.org/User:Peter_Huewe "User:Peter Huewe")

This article is still work in progress and still needs a lot of effort.

* * *

## Contents

*   1 Introduction
*   2 Example
*   3 Troubleshooting
*   4 Important Files
*   5 External links

## Introduction

MagicSysRq is a special key combination which lets the user perform certain low level tasks of the kernel and is especially useful when the whole system seems to be hung. MagicSysRq e.g. makes it possible to, more or less, cleanly shutdown a system, which doesn't even respond to CTRL+ALT+DEL any more.

The MagicSysRq functions are invoked by pressing ALT+SysRq+Function Key where function key describes one of the following.

TODO:Insert table here

## Example

The probably most used combination of MagicSysRq functions is the one RSEIUB, which can be easyly remebered by the mnemonic trick ``Raising Elephants is so utterly boring*\footnote{``You can still find several references the* mnemonic *Raising Skinny Elephants is utterly boring* on the internet - however it is probably better to do the sync after the tasks exited/were killed} - although we're dealing with penguins here ;) According to the table above this performs, in order: R Put Keyboard in Raw Mode E terminate all tasks I kill all tasks S Sync U remount all filesystems read-only B Reboot

## Troubleshooting

You can easily and safely check whether MagicSysRq works on your System by pressing Alt+SysRq+H - on a console you should now see a short help text of the MagicSysRq Feature or at least print this to `dmesg`.

If you don't see the help text, check whether CONFIG\_MAGIC\_SYSRQ=y is set in your Kernel Config. If it is set, please check the output of 'cat /proc/sys/kernel/sysrq' which should report 1 if MagicSysRq is enabled. To enable it just do a simple echo 1 > /proc/sys/kernel/sysrq

## Important Files

/proc/sys/kernel/sysrq - enable/disable MagicSysRq at runtime /proc/sysrq-trigger - trigger MagicSysRq from the console (e.g. useful over ssh)

TODO Content:

*   Explanation of remaining keys
*   using it over serial line -> BREAK+key, in kermit ctrl-\ b KEY
*   Interaction with KDB
*   Interaction with kexec / crashkernel

## External links

*   [Wikipedia Article about Magic_SysRq_key](http://en.wikipedia.org/wiki/Magic_SysRq_key)
*   [Kernel Documentation/sysrq.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux.git;a=blob;f=Documentation/sysrq.txt;hb=HEAD)

[Categories](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")
*   [Tips and Tricks](http://eLinux.org/Category:Tips_and_Tricks "Category:Tips and Tricks")

# External Links

*   [Embedded Linux JTAG debugging (CELF presentation)](http://tree.celinuxforum.org/CelfPubWiki/ELC2009Presentations?action=AttachFile&do=get&target=DebuggingWithJtagCelf2009.pdf)
*   [Debugging the Linux kernel using Eclipse/CDT and QEMU](http://issaris.blogspot.com/2007/12/download-linux-kernel-sourcecode-from.html)

# 内核跟踪与分析

# Kernel Tracing and Profiling

About kernel tracing and profiling tools and their usage.

# System Tap

> From: [eLinux.org](http://eLinux.org/System_Tap "http://eLinux.org/System_Tap")

# System Tap

This page has information about **System Tap**, which is of interest to embedded developers, because tracers are a useful tool for diagnosing problems during product development.

## Contents

*   1 Introduction
*   2 Open Source Projects/Mailing Lists
*   3 Miscellaneous notes
    *   3.1 Probe types
*   4 See Also
    *   4.1 ARM Support
*   5 Some Performance measurements
*   6 Links

## Introduction

SystemTap is a flexible and extensible system for adding trace collection and analysis to a running Linux kernel.

SystemTap is designed to be very flexible (allowing for the insertion of arbitrary C code), yet also easy-to-use (most trace statements are written in a simple scripting language, with useful data collection and aggregation routines available in (essentially) library form).

A key aspect of SystemTap is that it is intended to allow you to create a trace set (a "tapset"), and run it on a running Linux system, with no modification or re-compilation of the system required. To do this, it uses the kernel [KProbes](http://www-users.cs.umn.edu/~boutcher/kprobes/) interface and loadable kernel modules to dynamically add probe points and newly generated code to the running kernel.

## Open Source Projects/Mailing Lists

The main SystemTap site is at: [`sourceware.org/systemtap/`](http://sourceware.org/systemtap/)

The SystemTap mail list archives are at: [`sourceware.org/ml/systemtap/`](http://sourceware.org/ml/systemtap/)

The tutorial, which gives a good overview of the system, is at: [`sourceware.org/systemtap/tutorial/`](http://sourceware.org/systemtap/tutorial/)

## Miscellaneous notes

### Probe types

There are several types of probes:

*   kprobe & kretprobe, for dynamically insterted probes
*   timers
*   static instrumentation markers
*   performance counter events

In the future, there may be:

*   user-space probes,
*   user-space return probes, and
*   watchpoint probes (kernel & user)
*   and more

## See Also

Note that SystemTap is one of the major tracing systems for the Linux kernel.

There is work afoot (as of spring 2006) to try to collaborate on different parts of the tracing problem, between some of the major tracing projects. See the [Tracing Collaboration Project](http://eLinux.org/Tracing_Collaboration_Project "Tracing Collaboration Project") page for more information.

### ARM Support

System Tap works on ARM & OMAP platforms instructions are available [here](http://omappedia.org/wiki/Systemtap)

## Some Performance measurements

Jian Gui writes (in July 2006 on the **System Tap** mailing list):

```
Hi, we've tested the overhead of systemtap/LKET with some benchmarks
on a ppc64 machine.

It shows the overhead of systemtap/LKET is acceptable generally.
But it will also cause significant overhead for some benchmark of
special behavior, e.g. dbench. Dbench calls kill() in a very high
frequency to check whether a task is complete, thus leads to a high
overhead.

We categorized the event hooks into five groups in the testing:
grp1 - syscall.entry, process
grp2 - syscall.return, process
grp3 - iosyscall, ioscheduler, scsi, aio, process
grp4 - tskdispatch, pagefault, netdev, process
grp5 - syscall.entry, syscall.return, process

All the results are
   (score1 - score2)/score2 * 100%,  where:
score1: the benchmark score when probed by systemtap
score2: the benchmark score without probing

dbench (<3% is noise)
--------------------
grp1            -14.4%
grp2            -33.1%
grp3            -7.92%
grp4            -13.6%
grp5            -43.3%

specjbb (<3% is noise)
---------------------
grp 1           -0.87%
grp 2           -0.67%
grp 4           +0.47%
grp 5           +0.05%

tiobench (<3% is noise)
----------------------
grp1       sequential reads      +1.45%
           sequential writes     -6.98%
           random reads          +0.57%
           random writes         -2.11%
grp2       sequential reads      +0.11%
           sequential writes     -5.81%
           random reads          +0.03%
           random writes         -2.11%
grp3       sequential reads      +1.42%
           sequential writes     -6.98%
           random reads          +0.51%
           random writes         -2.11%
grp4       sequential reads      +1.38%
           sequential writes     -5.81%
           random reads          +0.60%
           random writes         -2.11%
grp5       sequential reads      +0.22%
           sequential writes     -8.14%
           random reads          -0.10%
           random writes         -1.05%

Rawiobench (<3% is noise)
------------------------
grp1       sequential aioread()     0%
           sequential aiowrite()    0%
           random aioread()         0%
           random aiowrite()        0%
grp2       sequential aioread()     0%
           sequential aiowrite()    0%
           random aioread()         0%
           random aiowrite()        -0.82%
grp3       sequential aioread()     0%
           sequential aiowrite()    0%
           random aioread()         0%
           random aiowrite()        0%
grp4       sequential aioread()     0%
           sequential aiowrite()    0%
           random aioread()         +0.79%
           random aiowrite()        -0.82%
grp5       sequential aioread()     0%
           sequential aiowrite()    -6.41%
           random aioread()         +0.79%
           random aiowrite()        0%

Test environment:
Machine:  Open Power 720/ 8 cpus/ 2 cores/ 6GB RAM (tiobench use 1G)
Software: RHEL4-U3GA/ 2.6.17.2/ systemtap-20060718/ elfutils-0.122-0.4 
```

## Links

*   [SystemTap Sans Kernel: A Pure Userspace Backend](https://events.linuxfoundation.org/events/collaboration-summit/stone) [Slides](https://events.linuxfoundation.org/images/stories/pdf/lfcs2012_jstone.pdf) [Video](http://video.linux.com/videos/systemtap-sans-kernel-a-pure-userspace-backend)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Kernel Trace Systems

> From: [eLinux.org](http://eLinux.org/Kernel_Trace_Systems "http://eLinux.org/Kernel_Trace_Systems")

# Kernel Trace Systems

Here are some links to information about different kernel tracing systems:

## Contents

*   1 General Purpose tracing systems
*   2 Special Purpose tracing systems
*   3 Trace Infrastructure
*   4 Sampling Systems
*   5 Related facilities
*   6 Other Systems
*   7 Collaboration Efforts

## General Purpose tracing systems

Some major Linux general-purpose tracing systems are:

*   ptrace - ability to trace syscall entry and exit, and signal delivery, to a process (also used for debugging a process)
    *   see "man ptrace" and "man strace"
*   Ftrace
    *   Ftrace Function Graph ARM - presentations and patches by Tim Bird to add function graph and duration tracing to ARM systems
        *   The presentation has good introductory material on ftrace, as well as links to additional resources
    *   tracer for kernel functions
    *   can also be used for debugging or analyzing latencies and performance issues
    *   in mainline since 2.6.27
    *   See [Measuring Function Duration with FTrace](http://eLinux.org/Measuring_Function_Duration_with_FTrace "Measuring Function Duration with FTrace")
        *   outline of presentation by Tim Bird for Linux Symposium 2009
*   System Tap - System Tap is a system for building and executing tracing and sampling systems that can be applied to a running Linux system
*   LTTng - Linux Trace Toolkit, next generation
*   LKST - Linux Kernel State Tracer

## Special Purpose tracing systems

There are some other notable special-purpose kernel tracing systems:

*   KFT - Kernel Function Trace - traces functions to show function durations and call graphs
*   latency trace - RT-preempt tool for measuring interrupt and mutex latency
    *   The latency tracer is embedded in the RT-preempt patch - see Realtime Preemption and [RT-preempt Article](http://lwn.net/Articles/97811/)
*   block tracer (blktrace) - allows you to see exactly what is going on in the block layer for a given queue
    *   Introduction by Jens Axboe: [Introduction](http://lwn.net/Articles/148761/)
    *   Execellent presentation: [blktrace.pdf](http://www.gelato.org/pdf/apr2006/gelato_ICE06apr_blktrace_brunelle_hp.pdf)
    *   Guide to using is at: [blktrace guide](https://secure.engr.oregonstate.edu/wiki/CS411/index.php/Blktrace_Guide)
    *   This appears to have been mainlined as of 2.6.17
    *   Timeline utility (blktrace post-processing tool): [blktrace timeline utility](http://www.nabble.com/NEW%3A-btt---blktrace-timeline-utility%3A-analyze-I-Os-collected-with-blktrace.-tf1644874.html)
*   delay accounting patches - collect statistics about the delays that are experienced by each task on the system
    *   see [delay accounting patches](http://lkml.org/lkml/2006/5/2/30)

## Trace Infrastructure

*   KProbes - grew out of dprobes, with information at: [dprobes](http://dprobes.sourceforge.net/)

    *   see an excellent tutorial at: [kprobes](http://www-users.cs.umn.edu/~boutcher/kprobes/)
    *   The mainline version of the KProbes supports x86,Alpha and PPC64 architectures. A MIPS implementation has been completed on the 2.6.16 kernel and tested on the Toshiba TX49 platform. Patch is available in the [Patch Archive](http://eLinux.org/Patch_Archive "Patch Archive").
*   [would be nice to have some djprobe stuff here]

## Sampling Systems

Note that profile systems (or "sampling systems") are slightly different, in that they involve sampling instead of event tracing. Some major ones for Linux are:

*   top - provides a dynamic real-time view of a running system, including processes
    *   see "man top"
    *   see also ksysguard, [Gnome system Monitor](http://freshmeat.net/projects/gnome-system-monitor/)
*   OProfile - system-wide profiler for Linux systems
    *   see [oprofile](http://oprofile.sourceforge.net/about/)
    *   also: [oprofile at IBM](http://www-128.ibm.com/developerworks/linux/library/l-oprof.html)
*   BootChart - samples bootup and provides visualization of process startup and system utilization
    *   see Bootchart

## Related facilities

*   in-kernel statistics infrastructure - proposal for a generic implementation of statistics facilities inside the kernel
    *   see [inkernel stats](http://lkml.org/lkml/2006/5/19/106)
*   perfmon2 - interfaces to hardware performance monitoring features of the CPU
    *   see [perfmon](http://perfmon2.sourceforge.net/)
*   inotify - [inotify](http://www-128.ibm.com/developerworks/linux/library/l-inotify.html)

## Other Systems

Here are some systems I haven't classified yet:

*   Datastreams - a system for creating and monitoring tracepoints - see [datastreams](http://kusp.ittc.ku.edu/wiki/index.php/Main_Page)

## Collaboration Efforts

Some trace system project leaders are trying to collaborate: see [Tracing Collaboration Project](http://eLinux.org/Tracing_Collaboration_Project "Tracing Collaboration Project")

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Linux tracing](http://eLinux.org/Category:Linux_tracing "Category:Linux tracing")

# Linux Trace Toolkit

> From: [eLinux.org](http://eLinux.org/Linux_Trace_Toolkit "http://eLinux.org/Linux_Trace_Toolkit")

# Linux Trace Toolkit

@) ***Preliminary Draft*** @) under construction

## Contents

*   1 Introduction
    *   1.1 Rationale
*   2 Downloads
    *   2.1 LTTng
    *   2.2 Sample i386 trace for LTTng
    *   2.3 0.9.6 Release (Nov 21, 2004)
    *   2.4 Older stuff
    *   2.5 getting attachments from ltt-dev archive
    *   2.6 Patch
    *   2.7 Utility programs
*   3 How To Use
    *   3.1 Building the software
    *   3.2 Using the software
*   4 References
*   5 Sample Results
*   6 Future Work
*   7 LKML Reaction to tracing

## Introduction

The Linux Trace Toolkit is used to examine the flow of execution (between processes, kernel threads, and interrupts) in a Linux system. This is useful for analyzing where delays occur in the system, and to see how processes interact (especially with regard to scheduling, interrupts, synchronization primitives, etc.)

![Alert.gif](http://eLinux.org/File:Alert.gif) * Note that as of 2.6.14, LTT is being replaced with LTTng. See [LTTng](http://ltt.polymtl.ca/)

See also the announcement here: [Patch: Linux Trace Toolkit Viewer/Next Generation announcement (LTTV/LTTng)](http://lwn.net/Articles/170198/)

### Rationale

Tracing is useful for analyzing stuff.

## Downloads

### LTTng

See the QUICKSTART guide at: [Quickstart](http://ltt.polymtl.ca/svn/ltt/branches/poly/QUICKSTART)

### Sample i386 trace for LTTng

Here is a zip file containing a sample trace on an i386 machine: i386-ltt-trace.zip

### 0.9.6 Release (Nov 21, 2004)

I found it a bit difficult to get the appropriate set of patches for LTT. After a bit of frustration, I built my own release for Nov. 23,

1.  The release is 0.9.6 (no 'pre') and it works with the Linux kernel 2.6.9\. The tar file for the release is in the PatchArchive.

### Older stuff

The latest stable release is 0.9.5a, but this release is over 2 years old.

Here is the patch set I finally used, for kernel 2.6.8.1:

*   [message with ltt-2.6.8.1-relayfs.patch.bz2 by Mathieu Desnoyers](http://www.listserv.shafik.org/pipermail/ltt-dev/2004-August/000638.html)
*   [message with ltt-2.6.8.1-ltt.patch.bz2 by Mathieu Desnoyers](http://www.listserv.shafik.org/pipermail/ltt-dev/2004-August/000637.html)

### getting attachments from ltt-dev archive

I had to use 'munpack' to extract the compressed patches from the mail messages in the archive. 'munpack' is part of the package 'mpack', which is available at: munpack

### Patch

Here are some patches used by Tim Bird at Sony, targeted at MIPS, but known to also work on other platforms. This tarball contains both an all-in-one patch and a series file with discreet sub-patches.

*   *   [media:Ltt-2.6.11-from-TimBird.tgz](http://eLinux.org/images/b/b5/Ltt-2.6.11-from-TimBird.tgz "Ltt-2.6.11-from-TimBird.tgz")
*   Patches for Linux kernel version 2.6.8.1, and !TraceToolkit tarfile are here:

    *   ltt-2.6.8.1.tar.gz
    *   TraceToolkit-0.9.6pre3-plus.tar.gz

These are bundles with multiple sub-patches and sub-tars. To install them, do the following:

*   untar kernel patches:
    *   `tar -xzvf ltt-2.6.8.1.tar.gz`
*   apply patches:
    *   `./tpm -t linux-2.6.8.1.tar.bz2 -f ltt-2.6.8.1.pl -o linux-2.6.8.1-ltt`
    *   untar !TraceToolkit stuff
    *   `tar -xzvf TraceToolkit-0.9.6pre3-plus.tar.gz`
*   unpack and apply patch:
    *   `./tpm -f TraceToolkit-0.9.6pre3-plus.pl -o TraceToolkit`

Now, follow the build and usage instructions for the software in the include docs (!TraceToolkit/Help/index.html). For cross-compiling, use the instructions on this wiki page.

### Utility programs

## How To Use

### Building the software

Apply the ltt and relayfs patches to your kernel:

*   configure kernel with LTT
*   turn on RelayFS and LTT

make menuconfig `File systems ---> Pseudo filesystems --->`

*   Relayfs file system support

(exit, exit) General Setup --->

*   Linux Trace Toolkit support

You can leave klog debugging support turned off.

Compile the user-space tracedaemon program:

I couldn't figure out if the tracevisualizer tools handle cross-compilation correctly (by which I mean that you can natively compile the tracevisualizer but cross-compile the tracedaemon). Instead of blindly trying configure tricks, I instead used the instructions from Karim's book "Building Embedded Linux Systems". In a nutshell, the instructions go something like this:

Overview:

```
* install source for user-space stuff
* configure for native build
* hand-build tracedaemon specifying a cross-compiler
* build rest of user-space suite using native compiler
* install programs as appropriate 
```

Detail (in my case):

```
* install !TraceToolkit-0.9.6pre3, apply "plus" patch
  ** tpm -f user-space.pl -o !TraceToolkit-0.9.6pre3
* cd !TraceToolkit-0.9.6pre3
* configure --prefix=/home/tbird/work/ltt/tools
* make -C !LibUserTrace CC=<cross-compiler-gcc> !UserTrace.o
* make -C !LibUserTrace CC=<cross-compiler-gcc> LDFLAGS="-static"
* make -C Daemon CC=<cross-compiler-gcc> LDFLAGS="-static"
* cp Daemon/tracedaemon Daemon/Scripts/trace Daemon/Scripts/tracecore 
```

Daemon/Scripts/traceu \<target file="" systeu0005c="" class="calibre16">/usr/sbin</target>

```
* make
* make install 
```

### Using the software

In April, 2004, Karim wrote: [lkml thread](http://lkml.org/lkml/2004/4/3/100)) `The documentation is out of date. Basically, the createdev.sh script isn't needed anymore because of relayfs. You need to mount relayfs to use LTT. See the classic dox on filesystem mounting for this kind of thing. It's going to be something like: # mount -t relayfs nodev /mnt/relay`

There's no insmod for LTT. It isn't a device driver module, following LKML recommendations.

## References

*   LTT home page is at: [LTT](http://www.opersys.com/LTT/)
*   Presentation from OLS 2006:
    *   [LTTng Tracer : A low impact performance and behavior monitor for GNU/Linux](http://ltt.polymtl.ca/slides/desnoyers-talk-ols2006.pdf)
*   Online documentation is at: [LTT Documentation](http://www.opersys.com/LTT/dox/ltt-online-help/index.html)
    *   This documentation is a bit old (2002), and has parts that are out of date.

## Sample Results

## Future Work

Here is a list of things that could be worked on for this feature:

*   online reference needs to be updated
    *   e.g. no mention of relayfs
*   project seems fairly quiet, and unmaintained
*   see if LTT patch should be refactored to take into account new kprobe support in the kernel

## LKML Reaction to tracing

In Sep 2002, there was a thread about tracing, where some major kernel developers expressed their concerns about tracing infrastructure in the kernel.

*   [Ingo Molnar](http://seclists.org/lists/linux-kernel/2002/Sep/5094.html)

`my problem with this stuff is conceptual: it introduces a constant drag on the kernel sourcecode, while 99% of development will not want to trace, ever. When i do need tracing occasionally, then i take those 30 minutes to write up a tracer from pre-existing tracing patches, tailored to specific problems.</source> ... so use the power of the GPL-ed kernel and keep your patches separate, releasing them for specific stable kernel ranches (or even development kernels).`

*   [Linus Torvalds](http://seclists.org/lists/linux-kernel/2002/Sep/5140.html)

`> To summarize: You find tracing useful, but software tracing is only of limited value in areas you're working at. What about other developers, which only want to develop a simple driver, without having to understand the whole kernel? Traces still work where printk() or kgdb don't work. I think it's reasonable to ask an user to enable tracing and reproduce the problem, which you can't reproduce yourself.`

That makes adding source bloat ok? I've debugged some drivers with dprintk() style tracing, and it often makes the code harder to follow, even if it ends up being compiled away.

From what I've seen from the LTT thing, it's too heavy-weight to be good for many things (taking SMP-global locks for trace events is _not_ a good idea if the trace is for doing things like doing performance tracing, where a tracer that adds synchronization fundamentally _changes_ what is going on in ways that have nothing to do with timing).

I suspect we'll want to have some form of event tracing eventually, but I'm personally pretty convinced that it needs to be a per-CPU thing, and the core mechanism would need to be very lightweight. It's easier to build up complexity on top of a lightweight interface than it is to make a lightweight interface out of a heavy one.

[Categories](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Boot Time](http://eLinux.org/Category:Boot_Time "Category:Boot Time")
*   [Tracing](http://eLinux.org/index.php?title=Category:Tracing&action=edit&redlink=1 "Category:Tracing (page does not exist)")
*   [Kernel](http://eLinux.org/Category:Kernel "Category:Kernel")

# LTTng

> From: [eLinux.org](http://eLinux.org/LTTng "http://eLinux.org/LTTng")

# LTTng

Linux Trace Toolkit Viewer/Next Generation

LTTV is a modular viewer/analysis tool specifically designed to deal with very large traces generated by a production system.

It comes with a Linux kernel tracer, Linux Trace Toolkit Next Generation (LTTng), which builds on the existing LTT tracepoints and RelayFS delivery mechanism but is a complete rewrite of LTT tracing module and daemon.

The design aims at facilitating contributions from the community. We know that the vast quantity of analysis that can be performed on trace data is practically unlimited, so we want to make it as easy and fun as possible to add those to the project.

You can get it at [`www.lttng.org`](http://www.lttng.org). Follow the Quickstart guide to know how to get it from Debian packages, RPM or sources.

LTTV key features :

*   Support for large traces (it has been tested with 15GB traces). I has been designed from the start to deal with huge traces.
*   Information from several tracefiles can be combined in a single view on the fly.
*   Deals with traces coming from any architecture size or endianness.
*   Text command line interface supporting plugins for trace batch analysis.
*   Graphical interface supporting visualisation plugins.
*   Flexible event filter.
*   Modular architecture :

```
 - dynamically loadable plugins : each specific view/analysis becomes a plugin.
 - module dependencies architecture for maximum functionality reuse and easier testing. 
```

*   Addition of new instrumentations (or any kind of trace point) becomes easier with an event description parser and a tracing code generator (genevent).

You can also get LTTng from [`www.lttng.org`](http://www.lttng.org) . Available in Debian, RPM and sources packages. See the Quickstart guide.

LTTng key features :

*   Easy addition of new instrumentations by supporting the genevent code generator.
*   Very precise timestamps on events by using the processor cycle counter as an unique monotonic time reference.
*   Minimal impact on the traced system : instrumentation does not disable interrupts or take locks. It uses RCU and cmpxchg atomic operations instead.
*   Non maskable interrupts (NMI) reentrancy.
*   Supports many nestable types : structures, unions, arrays, sequences.
*   Supports host dependent types.
*   Makes dynamic alignment of trace data with a low overhead.
*   Integration with LTTV viewer so tracing can be controlled directly from the graphical interface.
*   Can record many (n) independent traces at once.
*   Modular architecture.

[`lwn.net/Articles/166952/`](http://lwn.net/Articles/166952/)

## External Links

*   [lwn.net: LTTng 2.0: Tracing for power users and developers - part 1](http://lwn.net/Articles/491510/)
*   [lwn.net: LTTng 2.0: Tracing for power users and developers - part 2](http://lwn.net/Articles/492296/)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Linux](http://eLinux.org/Category:Linux "Category:Linux")

# Ftrace

> From: [eLinux.org](http://eLinux.org/Ftrace "http://eLinux.org/Ftrace")

# Ftrace

Ftrace is the Linux kernel internal tracer that was included in the Linux kernel in 2.6.27\. Although Ftrace is named after the function tracer it also includes many more functionalities. But the function tracer is the part of Ftrace that makes it unique as you can trace almost any function in the kernel and with dynamic Ftrace, it has no overhead when not enabled.

The interface for Ftrace resides in the debugfs file system in the tracing directory. Documentation for this can be found in the Linux kernel source tree at [Documentation/trace/ftrace.txt](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob;f=Documentation/trace/ftrace.txt;).

## Contents

*   1 trace-cmd
*   2 Tips
    *   2.1 Tracing a specific process with the Ftrace interface
    *   2.2 Tracing a specific process with trace-cmd
    *   2.3 Capturing an oops (from startup) to the serial console
    *   2.4 Find latencies on kernel startup
    *   2.5 Find deepest kernel stack
    *   2.6 additional resources
    *   2.7 pytimechart
    *   2.8 bootchart like traces with ftrace and pytimechart
    *   2.9 output

## trace-cmd

Using the Ftrace debugfs interface can be awkward and time consuming. trace-cmd was created to interface with Ftrace using a binary tool which comes with full documentation in man pages.

Here's some examples of trace-cmd:

```
# trace-cmd record -e sched myprogram 
```

The above will enable all the Ftrace tracepoints that are grouped under the sched system. You can find these tracepoints by looking at the debugfs system:

```
# mount -t debugfs nodev /sys/kernel/debug
# ls /sys/kernel/debug/tracing/events/sched
enable                  sched_process_fork  sched_stat_sleep
filter                  sched_process_free  sched_stat_wait
sched_kthread_stop      sched_process_wait  sched_switch
sched_kthread_stop_ret  sched_signal_send   sched_wait_task
sched_migrate_task      sched_stat_iowait   sched_wakeup
sched_process_exit      sched_stat_runtime  sched_wakeup_new 
```

trace-cmd allows you to see the possible events without needing to look at this directory as well.

```
# trace-cmd list -e | grep sched:
sched:sched_kthread_stop
sched:sched_kthread_stop_ret
sched:sched_wait_task
sched:sched_wakeup
sched:sched_wakeup_new
sched:sched_switch
sched:sched_migrate_task
sched:sched_process_free
sched:sched_process_exit
sched:sched_process_wait
sched:sched_process_fork
sched:sched_signal_send
sched:sched_stat_wait
sched:sched_stat_runtime
sched:sched_stat_sleep
sched:sched_stat_iowait 
```

You can find trace-cmd in its [git](http://git.kernel.org/?p=linux/kernel/git/rostedt/trace-cmd.git;a=summary) repository.

Also within that same repository is KernelShark, which is a graphical user interface to trace-cmd. trace-cmd is built with just "make" and KernelShark is created with "make gui". This allows building trace-cmd on your embedded device and keeping the build from needing the GTK libraries required by KernelShark.

## Tips

### Tracing a specific process with the Ftrace interface

(Adapted from email by Steven Rostedt) To trace just the kernel functions executed in the context of a particular function, set the pseudo-variable 'set-ftrace-pid', to the process id (pid) of the process.

If the process is not already running, you can use a wrapper shell script and the 'exec' command, to execute a command as a known pid.

Like so:

```
#!/bin/sh
echo $$ > /debug/tracing/set_ftrace_pid
# can set other filtering here
echo function > /debug/tracing/current_tracer
exec $* 
```

In this example, '$$' is the pid of the currently executing process (the shell script. This is set into the 'set_ftrace_pid' variable, then the 'function' tracer is enabled. Then this script exec's the command (specified by the first argument to the script).

Example usage (assuming script is called 'trace_command'):

```
trace_command ls 
```

### Tracing a specific process with trace-cmd

```
# trace-cmd record -p function -F ls 
```

### Capturing an oops (from startup) to the serial console

You can capture the function calls leading up to a panic by placing the following on the kernel command line:

```
ftrace=function ftrace_dump_on_oops 
```

*Note: You can also use 'ftrace=function_graph' if you would prefer that instead.*

The ftrace documentation, in Documentation/trace/ftrace.txt mentions how to set ftrace_dump_on_oops in a running system, but I have found it very handy to have it configured to dump the trace from kernel startup, so that any panics that occur during boot (before user-space is started) are also captured.

Note that the output will be VERY long. Please be patient.

The output will look something like the following:

```
 ash-56      0d..2\. 159400967us : _raw_spin_lock <-vprintk
     ash-56      0d..2\. 159400972us : __raw_spin_lock <-_raw_spin_lock
     ash-56      0d..2\. 159400974us : add_preempt_count <-__raw_spin_lock
     ash-56      0d..3\. 159400978us : log_prefix <-vprintk
     ash-56      0d..3\. 159400979us : emit_log_char <-vprintk
     ash-56      0d..3\. 159400981us : console_trylock <-vprintk
     ash-56      0d..3\. 159400983us : down_trylock <-console_trylock
     ash-56      0d..3\. 159400985us : _raw_spin_lock_irqsave <-down_trylock
     ash-56      0d..3\. 159400987us : __raw_spin_lock_irqsave <-_raw_spin_lock_irqsave
     ash-56      0d..3\. 159400989us : add_preempt_count <-__raw_spin_lock_irqsave
     ash-56      0d..4\. 159400991us : _raw_spin_unlock_irqrestore <-down_trylock
     ash-56      0d..4\. 159400993us : sub_preempt_count <-_raw_spin_unlock_irqrestore
     ash-56      0d..3\. 159400994us : _raw_spin_unlock <-vprintk
     ash-56      0d..3\. 159400997us : sub_preempt_count <-_raw_spin_unlock
     ash-56      0d..2\. 159400999us : console_unlock <-vprintk
     ash-56      0d..2\. 159401000us : _raw_spin_lock_irqsave <-console_unlock
     ash-56      0d..2\. 159401002us : __raw_spin_lock_irqsave <-_raw_spin_lock_irqsave
     ash-56      0d..2\. 159401004us : add_preempt_count <-__raw_spin_lock_irqsave
     ash-56      0d..3\. 159401006us : _raw_spin_unlock <-console_unlock
     ash-56      0d..3\. 159401008us : sub_preempt_count <-_raw_spin_unlock
     ash-56      0d..2\. 159401010us : _call_console_drivers <-console_unlock
     ash-56      0d..2\. 159401012us : _call_console_drivers <-console_unlock
     ash-56      0d..2\. 159401014us : _raw_spin_lock_irqsave <-console_unlock
     ash-56      0d..2\. 159401015us : __raw_spin_lock_irqsave <-_raw_spin_lock_irqsave
     ash-56      0d..2\. 159401017us : add_preempt_count <-__raw_spin_lock_irqsave
     ash-56      0d..3\. 159401019us : up <-console_unlock
     ash-56      0d..3\. 159401021us : _raw_spin_lock_irqsave <-up
     ash-56      0d..3\. 159401023us : __raw_spin_lock_irqsave <-_raw_spin_lock_irqsave
     ash-56      0d..3\. 159401024us : add_preempt_count <-__raw_spin_lock_irqsave
     ash-56      0d..4\. 159401027us : _raw_spin_unlock_irqrestore <-up
     ash-56      0d..4\. 159401029us : sub_preempt_count <-_raw_spin_unlock_irqrestore
     ash-56      0d..3\. 159401031us : _raw_spin_unlock_irqrestore <-console_unlock
     ash-56      0d..3\. 159401033us : sub_preempt_count <-_raw_spin_unlock_irqrestore
     ash-56      0d..2\. 159401034us : wake_up_klogd <-console_unlock
     ash-56      0d..2\. 159401037us : sub_preempt_count <-vprintk
     ash-56      0d..1\. 159401039us : die <-__do_kernel_fault
     ash-56      0d..1\. 159401041us : oops_enter <-die
---------------------------------
Modules linked in:
CPU: 0    Not tainted  (3.0.27_nl-kzm-a9-rt46-00022-g5e35327 #2)
PC is at sysrq_handle_crash+0x40/0x50
LR is at _raw_spin_unlock_irqrestore+0x34/0x54
pc : [<801b7dd4>]    lr : [<802f5420>]    psr: 60000093
sp : 9f0cdeb0  ip : 802f7ed4  fp : 9f0cdebc
r10: 9f0cdf68  r9 : 9fbaedc8  r8 : 00000000
r7 : 60000013  r6 : 00000063  r5 : 00000001  r4 : 8044b890
r3 : 00000000  r2 : 00000001  r1 : 20000093  r0 : 00000001
Flags: nZCv  IRQs off  FIQs on  Mode SVC_32  ISA ARM  Segment user
Control: 10c5787d  Table: 5f11c04a  DAC: 00000015
Process ash (pid: 56, stack limit = 0x9f0cc2f0)
Stack: (0x9f0cdeb0 to 0x9f0ce000)
dea0:                                     9f0cdee4 9f0cdec0 801b85d0 801b7da0
dec0: 9f0cdf68 00000002 801b867c 9f07a960 00000002 2ad20000 9f0cdefc 9f0cdee8
dee0: 801b86b0 801b852c 9f0cdf68 9fbaed80 9f0cdf2c 9f0cdf00 80108a84 801b8688
df00: 9f0cdf68 00000002 9f07a960 2ad20000 9f0cdf68 2ad20000 00000001 00000000
df20: 9f0cdf5c 9f0cdf30 800c2f14 80108a00 00000002 00000889 800c4978 00000002
df40: 9f07a960 0005a750 00000002 2ad20000 9f0cdfa4 9f0cdf60 800c3250 800c2e5c
df60: 00020001 0004423c 00000000 00000000 9f0cc000 00000000 9f0cdfa4 00000002
df80: 2ad20000 0005a750 00000004 8000db24 9f0cc000 00000000 00000000 9f0cdfa8
dfa0: 8000d8c0 800c3208 00000002 2ad20000 00000001 2ad20000 00000002 00000889
dfc0: 00000002 2ad20000 0005a750 00000004 00000001 00000000 2ad093b8 7e8766d4
dfe0: 00000000 7e8766c0 2ac20f08 2ac023ec 40000010 00000001 00000000 00000000
Backtrace:
[<801b7d94>] (sysrq_handle_crash+0x0/0x50) from [<801b85d0>] (__handle_sysrq+0xb0/0x15c)
[<801b8520>] (__handle_sysrq+0x0/0x15c) from [<801b86b0>] (write_sysrq_trigger+0x34/0x44)
 r8:2ad20000 r7:00000002 r6:9f07a960 r5:801b867c r4:00000002
r3:9f0cdf68
[<801b867c>] (write_sysrq_trigger+0x0/0x44) from [<80108a84>] (proc_reg_write+0x90/0xa4)
 r4:9fbaed80 r3:9f0cdf68
[<801089f4>] (proc_reg_write+0x0/0xa4) from [<800c2f14>] (vfs_write+0xc4/0x150)
[<800c2e50>] (vfs_write+0x0/0x150) from [<800c3250>] (sys_write+0x54/0x110)
 r8:2ad20000 r7:00000002 r6:0005a750 r5:9f07a960 r4:00000002
[<800c31fc>] (sys_write+0x0/0x110) from [<8000d8c0>] (ret_fast_syscall+0x0/0x30)
Code: 0a000000 e12fff33 e3a03000 e3a02001 (e5c32000)
---[ end trace feb441c6e3b9c3f1 ]---
Kernel panic - not syncing: Fatal exception
Backtrace:
[<80011908>] (dump_backtrace+0x0/0x114) from [<802f2304>] (dump_stack+0x20/0x24)
 r6:9f0cdd1c r5:8039bb64 r4:8045dc40 r3:00000002
[<802f22e4>] (dump_stack+0x0/0x24) from [<802f2404>] (panic+0xfc/0x220)
[<802f2308>] (panic+0x0/0x220) from [<80011dd4>] (die+0x18c/0x1d0)
 r3:00000001 r2:9f0cdd28 r1:20000113 r0:8039bb64
 r7:00000001
[<80011c48>] (die+0x0/0x1d0) from [<80014edc>] (__do_kernel_fault+0x74/0x94)
 r8:00000000 r7:9f0cde68 r6:9f0c1d40 r5:00000817 r4:00000000
[<80014e68>] (__do_kernel_fault+0x0/0x94) from [<802f7ca0>] (do_page_fault+0x254/0x274)
 r8:00000817 r7:9f0c1d40 r6:9f06d5e0 r5:00000000 r4:9f0cde68
r3:9f0cde68
[<802f7a4c>] (do_page_fault+0x0/0x274) from [<802f7db0>] (do_DataAbort+0x40/0xa8)
[<802f7d70>] (do_DataAbort+0x0/0xa8) from [<802f5d98>] (__dabt_svc+0x38/0x60)
 r8:00000000 r7:9f0cde9c r6:ffffffff r5:60000093 r4:801b7dd4
[<801b7d94>] (sysrq_handle_crash+0x0/0x50) from [<801b85d0>] (__handle_sysrq+0xb0/0x15c)
[<801b8520>] (__handle_sysrq+0x0/0x15c) from [<801b86b0>] (write_sysrq_trigger+0x34/0x44)
 r8:2ad20000 r7:00000002 r6:9f07a960 r5:801b867c r4:00000002
r3:9f0cdf68
[<801b867c>] (write_sysrq_trigger+0x0/0x44) from [<80108a84>] (proc_reg_write+0x90/0xa4)
 r4:9fbaed80 r3:9f0cdf68
[<801089f4>] (proc_reg_write+0x0/0xa4) from [<800c2f14>] (vfs_write+0xc4/0x150)
[<800c2e50>] (vfs_write+0x0/0x150) from [<800c3250>] (sys_write+0x54/0x110)
 r8:2ad20000 r7:00000002 r6:0005a750 r5:9f07a960 r4:00000002
[<800c31fc>] (sys_write+0x0/0x110) from [<8000d8c0>] (ret_fast_syscall+0x0/0x30)
CPU1: stopping
Backtrace:
[<80011908>] (dump_backtrace+0x0/0x114) from [<802f2304>] (dump_stack+0x20/0x24)
 r6:00000006 r5:00000001 r4:00000000 r3:00000000
[<802f22e4>] (dump_stack+0x0/0x24) from [<80008308>] (do_IPI+0xd8/0x148)
[<80008230>] (do_IPI+0x0/0x148) from [<802f5df4>] (__irq_svc+0x34/0xd0)
Exception stack(0x9fb47f68 to 0x9fb47fb0)
7f60:                   00000000 00000000 f300a000 00000000 9fb46000 80432444
7f80: 8045d464 802fd754 4000406a 411fc092 00000000 9fb47fbc 8000e660 9fb47fb0
7fa0: 8000e67c 8000e680 60000013 ffffffff
 r7:9fb47f9c r6:f0020000 r5:60000013 r4:8000e680
[<8000e64c>] (default_idle+0x0/0x38) from [<8000e920>] (cpu_idle+0x88/0xcc)
[<8000e898>] (cpu_idle+0x0/0xcc) from [<802f0130>] (secondary_start_kernel+0x140/0x164)
 r7:8045d57c r6:10c0387d r5:8043a2f8 r4:00000001
[<802efff0>] (secondary_start_kernel+0x0/0x164) from [<402efab4>] (0x402efab4)
 r5:00000015 r4:5fb4806a 
```

### Find latencies on kernel startup

It is possible to use ftrace to record functions that exceed a certain amount of time, using the 'tracing_thresh' option. This can be used for finding routines that are taking a long time on kernel startup, to help optimize bootup time:

*   Make sure the following kerne configuration options are set:
    *   CONFIG_FTRACE: "Tracers"
    *   CONFIG_FUNCTION_TRACER: "Kernel Function Tracer"
    *   CONFIG_FUNCTION_GRAPH_TRACER: "Kernel Function Graph Tracer"
*   Use the following on the kernel command line:
    *   tracing_thresh=200 ftrace=function_graph
        *   this traces all functions taking longer than 200 microseconds (.2 ms). You can use any duration threshhold you want.
*   to get the data:
    *   $ mount -t debugfs debugfs /debug
    *   $ cat /debug/tracing/trace

These command should be probably be done programatically (as part of an init script), to avoid data loss

*   scope of operation
    *   the tracer starts sometime during initialization, and you only get timings after it starts

### Find deepest kernel stack

This is useful to find the routine with the deepest kernel stack The system continually monitors the stack depth of all processes, and whenever a low-water mark is hit (deepest stack), it records the list of functions.

(The following instructions work for a v3.0 Linux kernel)

*   Kernel configuration: Set the following kernel configuration options:
    *   CONFIG_FTRACE: "Tracers"
    *   CONFIG_FUNCTION_TRACER: "Kernel Function Tracer"
    *   CONFIG_STACK_TRACER: "Trace max stack"
*   Turning it on: You can turn it on at boot time or at runtime.
    *   At boot time, use the following on the kernel command line:
        *   stacktrace
    *   or, at runtime do:
        *   echo 1 >/proc/sys/kernel/stack_tracer_enabled
*   To get the data:
    *   $ mount -t debugfs debugfs /debug
    *   $ cat /debug/tracing/stack_trace
*   scope of operation
    *   the stack tracer will continue operating until you turn it off, which can be done with:
        *   echo 0 >/proc/sys/kernel/stack_tracer_enabled

### additional resources

See [`lwn.net/Articles/295955/`](http://lwn.net/Articles/295955/)

### pytimechart

You can use pytimechart to explore ftrace traces visually. See [`packages.python.org/pytimechart/userguide.html`](http://packages.python.org/pytimechart/userguide.html)

### bootchart like traces with ftrace and pytimechart

You can use the following kernel command line parameters to generate a trace at boot, which can then be open with pytimechart to have a browsable bootchart.

```
 trace_event=sched:*,timer:*,irq:* trace_buf_size=40M 
```

### output

Here is what the output looks like, on ARM:

```
/debug/tracing # cat stack_trace
        Depth    Size   Location    (42 entries)
        -----    ----   --------
  0)     3328      16   ftrace_test_stop_func+0x28/0x34
  1)     3312      28   __gnu_mcount_nc+0x58/0x60
  2)     3284      52   skb_release_data+0xc0/0xc8
  3)     3232      24   __kfree_skb+0x24/0xc0
  4)     3208      32   consume_skb+0xe4/0xf0
  5)     3176      56   smsc911x_hard_start_xmit+0x188/0x2f4
  6)     3120      72   dev_hard_start_xmit+0x440/0x6a4
  7)     3048      40   sch_direct_xmit+0x8c/0x1f8
  8)     3008      48   dev_queue_xmit+0x2c8/0x570
  9)     2960      56   neigh_resolve_output+0x32c/0x390
 10)     2904      40   ip_finish_output+0x2bc/0x37c
 11)     2864      32   ip_output+0xb0/0xb8
 12)     2832      24   ip_local_out+0x38/0x3c
 13)     2808      32   ip_send_skb+0x18/0xa4
 14)     2776      56   udp_send_skb+0x274/0x394
 15)     2720     240   udp_sendmsg+0x4dc/0x748
 16)     2480      32   inet_sendmsg+0x70/0x7c
 17)     2448     232   sock_sendmsg+0xa8/0x160
 18)     2216      32   kernel_sendmsg+0x40/0x48
 19)     2184      96   xs_send_kvec+0xa8/0xb0
 20)     2088      64   xs_sendpages+0x90/0x1f8
 21)     2024      40   xs_udp_send_request+0x4c/0x13c
 22)     1984      48   xprt_transmit+0x114/0x214
 23)     1936      40   call_transmit+0x208/0x27c
 24)     1896      48   __rpc_execute+0x88/0x334
 25)     1848      24   rpc_execute+0x68/0x70
 26)     1824      24   rpc_run_task+0xa8/0xb4
 27)     1800      64   rpc_call_sync+0x68/0x90
 28)     1736      32   nfs_rpc_wrapper.clone.6+0x3c/0x7c
 29)     1704      48   nfs_proc_getattr+0x70/0xac
 30)     1656      48   __nfs_revalidate_inode+0xe4/0x1f8
 31)     1608      56   nfs_lookup_revalidate+0x1ac/0x40c
 32)     1552      72   do_lookup+0x228/0x2e4
 33)     1480      72   do_last.clone.44+0x10c/0x688
 34)     1408      88   path_openat+0x2fc/0x394
 35)     1320     144   do_filp_open+0x40/0x8c
 36)     1176      40   open_exec+0x2c/0xc0
 37)     1136     136   load_elf_binary+0x1cc/0x12b8
 38)     1000      72   search_binary_handler+0x150/0x3a0
 39)      928      56   do_execve+0x170/0x328
 40)      872      32   sys_execve+0x44/0x64
 41)      840     840   ret_fast_syscall+0x0/0x30 
```

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Linux tracing](http://eLinux.org/Category:Linux_tracing "Category:Linux tracing")

# Using Kernel Function Trace

> From: [eLinux.org](http://eLinux.org/Using_Kernel_Function_Trace "http://eLinux.org/Using_Kernel_Function_Trace")

# Using Kernel Function Trace

Using Kernel Function Trace Ver. 0.1.1 -- 2007-04-26 Most material provided by Sony

* * *

## Contents

*   1 Introduction
*   2 Quick Overview
*   3 Detailed instructions
    *   3.1 Configuring the kernel for using KFT
    *   3.2 Editing the static trace run configuration (optional)
    *   3.3 Compiling the kernel
    *   3.4 Configuring the trace run
        *   3.4.1 Triggers
        *   3.4.2 Filters
        *   3.4.3 Configuration scenarios
        *   3.4.4 Handling a link error
*   4 Initiating a KFT run
*   5 Reading the trace data
*   6 Processing the data
    *   6.1 Analyzing the data with kd
    *   6.2 KFT utilities
*   7 Tips for using KFT
*   8 Online Resources
*   9 Appendices
    *   9.1 Appendix A - KFT configuration language
        *   9.1.1 A note on function names
        *   9.1.2 configuration block
        *   9.1.3 triggers
        *   9.1.4 filters
        *   9.1.5 watches
        *   9.1.6 miscellaneous
        *   9.1.7 Configuration Samples
    *   9.2 Appendix B - Sample results
        *   9.2.1 kft log output (excerpt)
        *   9.2.2 kft log analysis with 'kd'
        *   9.2.3 kft nested call trace with 'kd -c'
    *   9.3 Appendix C - Using KFT for monitoring stack usage
    *   9.4 Appendix D - Some notes on KFT operation
        *   9.4.1 Trace overhead
            *   9.4.1.1 Overhead measurement
        *   9.4.2 Trace buffer exhaustion
        *   9.4.3 Early clock issues
            *   9.4.3.1 Adding platform support for the KFT clock source
*   10 Modification History

## Introduction

This document describes how to use Kernel Function Trace with the Linux kernel. It assumes you have already applied the KFT patch to your kernel, or that it was otherwise previously integrated with your kernel source code.

Kernel Function Trace (KFT) is a kernel function tracing system, which examines every function entry and exit in the Linux kernel. The KFT system provides for capturing a subset of these events along with timing and other details. KFT is different from other kernel tracing systems in that it is designed to be able to filter the events by the duration of the function calls. Thus, KFT is good for finding out where time is spent in functions and sub-routines in the kernel. When used in unfiltered mode, KFT is very useful to collect information about the flow of control in the kernel, which can help with debugging or optimizing kernel code.

The main mode of operation with KFT is by running a "dynamic" trace. That is, you start the kernel as usual, then, using the `/proc/kft` interface, configure a trace, start it, and retrieve the trace data immediately.

However, another special mode of operation is available for performing bootup time tracing. In this mode, the configuration for a trace is compiled statically into the kernel. This is sometimes referred to as "static" mode. This mode is useful for getting a trace of the kernel during system bootup, before user space is running and before any services are available to configure and start a trace. This mode is particularly helpful to find problems with kernel bootup time.

In either case, you specify a KFT configuration for the trace run. The configuration tells how to automatically start and stop the trace, whether to include interrupts as part of the trace, and whether to filter the event data by various criteria (for minimum function duration, only certain listed functions, etc.)

When a trace is complete, the event data collected during the trace is retrieved by reading from `/proc/kft_data`.

Finally, KFT provides tools to process and analyze the data in a KFT trace.

## Quick Overview

Quick overview for using KFT in dynamic mode:

*   Configure your kernel with support for KFT
*   Compile your kernel
*   Boot the kernel
*   Write a configuration to `/proc/kft`
*   Start the trace
*   Read the trace data from `/proc/kft_data`
*   Process the data
    *   Use `addr2sym` to convert addresses to function names
    *   use `kd` to analyze trace data

* * *

Quick overview for using KFT during bootup:

*   Configure your kernel with support for KFT and KFT_STATIC_RUN
*   Edit the configuration in `<linux_src>/kernel/kftstatic.conf`
*   Compile your kernel
*   Boot the kernel
    *   The run should be triggered during bootup
*   Read the trace data from `/proc/kft_data`
*   Process the data
    *   Use `addr2sym` to convert addresses to function names
    *   use `kd` to analyze trace data

## Detailed instructions

### Configuring the kernel for using KFT

Configure your kernel to support KFT by editing the kernel configuration (.config) file.

For example, if you are using 'make menuconfig', set the following option under the "Kernel Hacking" menu.

```
Kernel Hacking --->
[*] Kernel Function Trace 
```

Save this configuration. This will set the option CONFIG_KFT=y in your kernel .config file.

* * *

If you wish to perform a trace during kernel bootup time, also configure for KFT static mode.

For example, if you are using 'make menuconfig', set the following option under the "Kernel Hacking" menu.

```
Kernel Hacking --->
[*] Kernel Function Trace
[*] Static function tracing configuration 
```

Save this configuration. This will set the following options in your kernel .config file:

```
CONFIG_KFT=y
CONFIG_KFT_STATIC_RUN=y 
```

### Editing the static trace run configuration (optional)

If you are performing a "static" trace, edit the file `kernel/kftstatic.conf` to set the configuration for the trace run you wish to perform at system boot. (see the next section "Configuring the trace run" for details on the trace configuration syntax and options.) Note that even if you perform or bootup time trace, you can still perform dynamic traces any time while the system is running.

### Compiling the kernel

Build the kernel, and install it to boot on your target machine.

Make sure to save the `System.map` file from this build, since it will be used later when processing the trace data.

If you get an error compiling the kernel, see the next section on trouble-shooting configuration problems.

### Configuring the trace run

To configure your trace, you write a trace configuration file. This file specifies when to start and stop the trace, and what events to save as part of the trace data.

Here is a sample configuration file, commonly used during bootup:

```
begin
  trigger start entry start_kernel
  trigger stop entry to_userspace
  filter mintime 500
end 
```

This trace says to:

*   start tracing when the function "start_kernel" is entered
*   stop tracing when the function "to_userspace" is entered
*   don't save the events for any function that takes less than 500 microseconds

The function "start_kernel" is the first C function executed by the kernel on startup. The function "to_userspace" is a function called immediately before execution is transferred to the first user space program (usually `/sbin/init`). This trace configuration says to start tracing immediately when the kernel starts executing, and stop tracing right before the first user space program runs. It will only save in the trace buffer a record of functions that took longer than 500 microseconds to execute.

#### Triggers

Triggers, in the configuration, are used to start and stop the data collection of the trace system. Triggers can be based on a function entry or exit event, or on the passage of time. The stop trigger is used to control the amount of data collected. The trace will automatically stop if the buffer runs out of space for trace data.

Time values are expressed in decimal microseconds. The start time is relative to booting, or to the initialization of the clock used for tracing (usually, whatever clock is being used by the internal kernel function sched_clock(). A stop time is relative to the start time.)

Here are some examples:

*   statement: `trigger start entry start_kernel`

    *   meaning: start tracing when the kernel enters the function "start_kernel"
*   statement: `trigger stop exit do_fork`

    *   meaning: stop tracing when the kernel exits the function "do_fork"
*   statement: `trigger start time 10000000`

    *   meaning: start tracing 10,000,000 microseconds (10 seconds) after booting
*   statement: `trigger stop time 5000`

    *   meaning: stop tracing 5,000 microseconds (5 milliseconds) after the trace starts

#### Filters

Filters control what data is collected during the trace. Since every kernel function entry and exit is a possible candidate for trace event recording, KFT can potentially generate a LOT of data. To control how much data is recorded, it is customary to set filters used during the trace.

You can filter by function duration, by interrupt context, or limit the trace to specific functions. Times in a filter statement are expressed in microseconds. Functions in a filter function list can be expressed by name in a static configuration, but must be expressed by addressed in a dynamic configuration.

Here are some examples:

*   statement: `filter mintime 100`

    *   meaning: only keep functions in the trace which last at least 100 microseconds
*   statement: `filter maxtime 5000000`

    *   meaning: discard functions in the trace which last more than 5,000,000 microseconds (5 seconds)
*   statement: `filter noints`

    *   meaning: discard functions in the trace which are executed when the processor is in interrupt context
*   statement: `filter onlyints`

    *   meaning: retain only the functions in the trace which are executed when the processor is in interrupt context
*   statement: `filter funclist do_fork sys_read fend`

    *   meaning: retain only events for the functions do_fork and sys_read.

#### Configuration scenarios

For other commands you can include in the trace configuration, see Appendix A

#### Handling a link error

You may get an error linking the kernel if you reference certain functions in the `kftstatic.conf` that are not visible globally. If you see a linker error like the following: "undefined reference to `foo_func'", then you can resolve this by making `'foo_func'` visible. Usually, this means finding the declaration of `'foo_func'`, and removing the `'static'` keyword from its declaration.

## Initiating a KFT run

If you are running in static mode, upon booting the kernel, the trace should be initiated and run automatically, depending on the trigger and filter settings in `kernel/kftstatic.conf`).

If you are running in dynamic mode, then you initiate a run by writing a KFT configuration to `/proc/kft`, then "priming" the run.

Traces go through a state machine (a series of event transitions) in order to actually start collecting data. This is to allow trace collection to be separated from trace setup and preparation. The trace configuration specifies a start trigger, which will initiate the collection of data. When the configuration is written to `/proc/kft`, it is not ready to run yet. Making the trace ready to run is called "priming" it.

Therefore, the normal sequence of events for a trace run is:

1.  . The user writes the configuration file, usually using an editor and creating the file in the local filesystem. Helper scripts can be used to auto-generate simple configurations for common tasks.

    1.  . There is a helper script scripts/sym2addr, which converts function names in the configuration file to addresses. This can be copied to the target, along with the current System.map file, to make preparing the configuration file easier.
2.  The user writes the configuration to KFT (via /proc/kft)

    1.  e.g. `cat /tmp/trace.config >/proc/kft`
3.  If needed, the user prepares for trace by setting up programs to run.

4.  The user primes the trace

    1.  e.g. `echo "prime" >/proc/kft`
5.  A kernel event occurs which starts the trace (the start trigger fires)

6.  Trace data is collected
7.  A kernel event or buffer exhaustion stops the trace (that is, the stop trigger fires, or the buffer runs out)

It is possible to force the start or end of a trace using the `/proc/kft` interface. This overrides steps 5 or 7, which are normally performed by triggers in the trace configuration.

*   To manually start a trace: `echo "start" >/proc/kft`
*   To manually stop a trace: `echo "stop" >/proc/kft`

You can get the status of the current trace by reading `/proc/kft`.

To see the status of the currently configured trace:

*   `cat /proc/kft`

## Reading the trace data

When the trace is running, the trace data is accumulated in a buffer inside the kernel. Once the trace data is collected, you can retrieve if from the kernel by copying the data from `/proc/kft_data`. Usually, you will want to save the data to a file for later analysis.

Here is an example:

*   `cat /proc/kft_data >/tmp/kft.log`

## Processing the data

Copy the `kft.log` file from the target to your host development system (on which the kernel source resides), for example, into the `/tmp` directory on your host machine.

The raw `kft.log` file will only have numeric function addresses. To translate these addresses to symbols, use the `addr2sym` program, along with the `System.map` file which was produced when you built the kernel.

Change directory to your kernel source top-level directory and run `scripts/addr2sym` to translate addresses to symbols:

Example:

```
$ scripts/addr2sym /tmp/kft.log -m System.map > /tmp/kft.lst 
```

Here is an example fragment of output from `addr2sym` on a TI OMAP Innovator. Entry and Delta value are times in microseconds. The Entry time is the time time since machine boot, and the Delta time is the time between the function entry and exit.

```
 Entry      Delta      PID            Function                    Called At
--------   --------   -----   -------------------------   --------------------------
   23662       1333       0                    con_init   console_init+0x78
   25375     209045       0             calibrate_delay   start_kernel+0xf0
  234425     106067       0                    mem_init   start_kernel+0x130
  234432     105278       0       free_all_bootmem_node   mem_init+0xc8
  234435     105270       0       free_all_bootmem_core   free_all_bootmem_node+0x28
  340498       4005       0       kmem_cache_sizes_init   start_kernel+0x134 
```

In the above, `calibrate_delay` took about 209 milliseconds.

`mem_init` took 106 msecs, the majority of which (105 msecs) was in `free_all_bootmem_core` (which is called by `free_all_bootmem_node`, which is called by `mem_init`).

If you just look at the function duration, it may appear that lots of time is being spent in certain functions, when in reality those functions are "thin", and the real time-consuming function is one of its children. Thus, rather than look just at the function Delta (or duration), you should look at the function entry times. If there is a big leap in the function entry times, that means a lot of time was consumed in the function right before the leap.

In the example above, there is a leap from 234435 to 340498 (about 100 milliseconds) between the Entry times for `free_all_bootmem_core` and `kmem_cache_sizes_init`. No other functions Entries (lasting more than 500 microseconds, based on the KFT configuration used) were recorded during this time, so this means that this time was spent in `free_all_bootmem_core`.

CPU-yielding functions like schedule_timeout, switch_to, kernel_thread, etc. can have large Delta values due intervening scheduling activity, but these can often be quickly filtered out by following the "leaps in the entry times in the Entry column" above.

### Analyzing the data with kd

You can use the program `"kd"` to further process the data. It is very helpful at this point to have resolved the names of the functions in the log file, but it is not strictly necessary. The `kd` program function reads a KFT log file and determines the time spent locally in a function versus the time spent in sub-routines. It sorts the functions by the total time spent in the function, and can display various extra pieces of information about each function (number of times called, average call time, etc.)

Also `kd` can be used to re-generate a function call trace from the trace log. This can be very helpful to see the sequence of execution (including interrupts, context switches and returns) of the code that was traced.

Use "./kd -h" for more usage help.

As of this writing, KFT and kd do not correctly account for scheduling jumps. The time reported by KFT for function duration is just wall time from entry to exit.

For examples of what kd can show, try the following commands on the sample kft output file:

[show all functions sorted by time]

```
$ ./kd kftsample.lst | less 
```

[show only 10 top time-consuming functions]

```
$ ./kd -n 10 kftsample.lst 
```

[show only functions lasting longer than 100 milliseconds]

```
$ ./kd -t 100000 kftsample.lst 
```

[show each function's most time-consuming child, and the number of times it was called. (You may want to make your terminal wider for this output.)]

```
$ ./kd -f Fcatlmn kftsample.lst 
```

[show call traces]

```
$ ./kd -c kftsample.lst 
```

[show call traces with timing data, and functions interlaced]

```
$ ./kd -c -l -i kftsample.lst 
```

Note that the call trace mode may not produce accurate results if weird filtering was used in the trace config (routines that are part of the call tree may be missing, which will confuse kd).

### KFT utilities

KFT includes the following helper scripts which are located in the kernel `scripts` directory:

*   mkkftrun.pl - used during building the kernel to convert a configuration file into a C file to be compiled into the kernel. This is run automatically by the kernel make system. Users of KFT should not need to worry about this.
*   sym2addr - convert function names to addresses in a KFT configuration file (for a dynamic trace). This is only used if a dynamic configuration has function names.
*   addr2sym - convert function addresses to symbols in the trace data
*   kd - KFT dump - does filtering, sorting, analysis and trace formatting of KFT trace logs

The use of most these are described elsewhere in this document. But this list is here for the sake of completeness.

[ should provide usage for each command? ]

## Tips for using KFT

*   How to look for long-duration functions?

```
 (searching child functions for local time) 
```

*   *   how to avoid being fooled by bogus local times
    *   How to see a detailed function trace (don't use a min-filter)
    *   How to interpret trace results including context switches

## Online Resources

Here's a presentation about KFT usage: (Actually, the presentation covers KFT's predecessor KFI, but all the information is basically the same.)

*   [Learning the Kernel and Finding Performance Problems with KFI](http://eLinux.org/images/a/a6/KFI-presentation.ppt "KFI-presentation.ppt") Presentation by Tim Bird at CELF [International Technical Jamboree](http://eLinux.org/International_Technical_Jamboree "International Technical Jamboree") in 2005
    *   [Media:omap-serial_init.trace.txt](http://eLinux.org/images/1/1b/Omap-serial_init.trace.txt "Omap-serial init.trace.txt")
        *   Sample trace used with presentation

## Appendices

### Appendix A - KFT configuration language

This appendix describes the language for specifying a KFT trace run. Is it used for both static mode (`kftstatic.conf`), and dynamic mode (written to `/proc/kft`).

#### A note on function names

NOTE that for parameters referencing functions, you can use the function name in `kftstatic.conf` (that is, when you using a static configuration). However, you have to use the function address when setting the configuration via the `/proc/kft` interface. The reason for this is that kernel symbols are always available at compile-time, but may not be available in the kernel at runtime, depending on your kernel configuration.

To convert a function name to an address, you can look up the address for the symbol in the System.map file for the current kernel. There is a helper program provided called `sym2addr` which you can use to convert the function names in a configuration file into addresses. To do this manually, use:

e.g. grep do_fork System.map

```
c001d804 T do_fork 
```

In this case, you would put 0xc001d804 in place of the function name in the configuration file. (Note the leading '0x'.)

To use the helper function `sym2addr`, do the following:

```
sym2addr trace_do_fork.conf System.map >trace_do_fork.conf2
cat trace_do_fork.conf2 >/proc/kft 
```

#### configuration block

The configuration for a single run is inside a block that starts with 'begin' and ends with 'end'. Inside the block are triggers, filters, and miscellaneous entries. By convention, each configuration entry is placed on its own line. When writing the configuration to /proc/kft, then the keyword "new" should appear before the block 'begin' keyword.

#### triggers

```
trigger:
         either "start" or "stop", and then one of:
        entry <funcname>
        exit <funcname>
        time <time-in-usecs>
syntax:
trigger start|stop entry|exit|time <arg> 
```

Start time is relative to booting. Stop time is relative to trace start time.

#### filters

```
filters
    maxtime <max-time>
    mintime <min-time>
    noints
    onlyints
    funclist <func1> <func2> fend

syntax:
filter noints|onlyints|maxtime|mintime|funclist <args> fend 
```

The funclist specifies a list of functions which will be traced. When a funclist is specified, only those functions are traced, and all other functions are ignored.

When specifying a configuration via /proc/kft, the 'fend' keyword must be used to indicated the end of the function list. When the configuration is specified via kftstatic.conf, no 'fend' keyword should be used.

#### watches

```
watches
    stack <low-water-threshold>
    worst-stack <starting-low-water-threshold>

syntax:
watch stack|worst-stack <threshold> 
```

A watch is used to have KFT monitor the trace for a particular condition, and act on the condition (usually preserve extra data to help debug that condition). The only supported watches currently are for monitoring the stack depth.

For a "stack" watch, while the trace is running the current position of the stack pointer is checked upon entry to every function. If the stack position is lower than the specified threshold, the current call stack of functions is preserved in the log (no matter whether the functions match other KFT filtering criteria or not), and the function durations are marked with a -2 value, to highlight them in the log. This operation (saving the call stack) is performed every time the stack position underflows the threshold. In this mode, an arbitrary number of call stacks can be recorded in the log (up to the limit of the log size).

For a "worst-stack" watch, the same monitoring is performed as with a "stack" watch. However, every time the condition is met, the threshold (worst stack left) is set to the new low stack value. In this mode, a call stack is preserved for each new low-water condition. The last such set of marked functions in the log will record the most stack-consuming call stack seen during the trace. Note also that the lowest recorded stack position is available in the KFT status information (from /proc/kft).

#### miscellaneous

```
logentries <num-entries> 
```

specify the maximum number entries for the log for this run

```
autorepeat 
```

Repeat trace indefinitely. That is, on trace trigger stop, prime the trace to run again, but leave the data in the buffer. The trace will start again when the start trigger is matched, and stop again when the stop trigger is matched. The trace will stop autorepeating when the buffer becomes full.

```
# Other options that may be supported in the future:
# overwrite
# Overwrite old data in the trace buffer.  This converts the trace buffer to
# a circular buffer, and does not stop the trace when the buffer becomes full.
# In overwrite mode, the end of the trace is available if the buffer is
# not large enough to hold the entire trace.  In NOT overwrite mode (regular
# mode) the beginning of the trace is available if the buffer is not large
# enough to hold the entire trace.

# untimed
# Do not time function duration.  Normally, the log contains only function
# entry events, with the start time and duration of the function.  In
# untimed mode, the log contains entry AND exit events, with the start
# time for each event.  Calculation of function duration must be done by
# a log post-processing tool.

# prime
# Immediately prime the trace for execution.  "Priming" a trace means making
# it ready to run.  A trace loaded without the "prime" command will not be
# enabled until the user issues a separate "prime" command through the
# /proc interface.

# prime entry ??
# primt exit ??
# prime time ?? 
```

#### Configuration Samples

Here are some configuration samples:

Record all functions longer that 500 microseconds, during bootup. Don't include functions executed inside interrupts.

```
new
begin
  trigger start entry start_kernel
  trigger stop exit to_userspace
  filter mintime 500
  filter maxtime 0
  filter noints
end 
```

* * *

Record all functions longer that 500 microseconds, for 5 seconds after the next fork don't worry about interrupts

Assuming 'do_fork' is at address 0xc001d804

```
new
begin
  trigger start entry 0xc001d804
  trigger stop time 5000000
  filter mintime 500
  filter maxtime 0
  filter noints
end 
```

*   record short routines called by do_fork
*   use a small log

```
new
begin
  trigger start entry do_fork
  trigger stop exit do_fork
  filter mintime 10
  filter maxtime 400
  filter noints
  logentries 500
end 
```

*   record interrupts for 5 milliseconds, starting 5 seconds after booting

```
new
begin
  trigger start time 5000000
  trigger stop time 5000
  filter onlyints
end 
```

*   record all calls to schedule after 10 seconds
*   Assuming schedule is at address

kftstatic.conf version:

```
begin
  trigger start time 10000000
  filter funclist schedule fend
end 
```

/proc/kft version, assuming schedule is at c02cb754

```
new
begin
  trigger start time 10000000
  filter funclist 0xc02cb754 fend
end 
```

### Appendix B - Sample results

Here is an excerpt from a KFI log trace (processed with addr2sym). It shows all functions which lasted longer than 500 microseconds, from when the kernel entered start_kernel() to when it entered to_userspace().

#### kft log output (excerpt)

```
Kernel Instrumentation Run ID 0

Logging started at 6785045 usec by entry to function start_kernel
Logging stopped at 8423650 usec by entry to function to_userspace

Filters: 
```

500 usecs minimum execution time

```
Filter Counters:

Execution time filter count = 896348
Total entries filtered = 896348
Entries not found = 24

Number of entries after filters = 1757

 Entry      Delta      PID            Function                    Called At
--------   --------   -----   -------------------------   -------------------------
      1          0       0                start_kernel   L6+0x0
     14       8687       0                  setup_arch   start_kernel+0x35
     39        891       0                setup_memory   setup_arch+0x2a8
     53        872       0   register_bootmem_low_pages   setup_memory+0x8f
     54        871       0                free_bootmem   register_bootmem_low_pages+0x95
     54        871       0           free_bootmem_core   free_bootmem+0x34
    930       7432       0                 paging_init   setup_arch+0x2af
    935       7427       0             zone_sizes_init   paging_init+0x4e
    935       7427       0              free_area_init   zone_sizes_init+0x83
    935       7427       0         free_area_init_node   free_area_init+0x4b
    935       3759       0        __alloc_bootmem_node   free_area_init_node+0xc5
    935       3759       0        __alloc_bootmem_core   __alloc_bootmem_node+0x43
   4694       3668       0         free_area_init_core   free_area_init_node+0x75
   4817       3535       0            memmap_init_zone   free_area_init_core+0x2bd
   8807     266911       0                   time_init   start_kernel+0xb6
   8807     261404       0               get_cmos_time   time_init+0x1c
 270211       5507       0                select_timer   time_init+0x41
 270211       5507       0                    init_tsc   select_timer+0x45
 270211       5507       0               calibrate_tsc   init_tsc+0x6c
 275718       1638       0                console_init   start_kernel+0xbb
 275718       1638       0                    con_init   console_init+0x59
 275954        733       0          vgacon_save_screen   con_init+0x288
 277376       6730       0                    mem_init   start_kernel+0xf8
 277376       1691       0            free_all_bootmem   mem_init+0x52
 277376       1691       0       free_all_bootmem_core   free_all_bootmem+0x24
 284118      25027       0             calibrate_delay   start_kernel+0x10f
 293860        770       0                     __delay   calibrate_delay+0x62
 293860        770       0                   delay_tsc   __delay+0x26
 294951       1534       0                     __delay   calibrate_delay+0x62
 294951       1534       0                   delay_tsc   __delay+0x26
 297134       1149       0                     __delay   calibrate_delay+0xbe
 297134       1149       0                   delay_tsc   __delay+0x26
 .
 .
 .
1638605          0     145              filemap_nopage   do_no_page+0xef
1638605          0     145                 __lock_page   filemap_nopage+0x286
1638605          0     145                 io_schedule   __lock_page+0x95
1638605          0     145                    schedule   io_schedule+0x24
1638605          0       5                    schedule   worker_thread+0x217
1638605          0       1                to_userspace   init+0xa6 
```

The log is attached here: [Media:Kfiboot-9.lst](http://eLinux.org/images/6/68/Kfiboot-9.lst "Kfiboot-9.lst") A Delta value of 0 usually means the exit from the routine was not seen.

#### kft log analysis with 'kd'

Below is a `kd` dump of the data from the above log.

For the purpose of finding areas of big time in the kernel, the functions with high "Local" time are important. For example, `delay_tsc()` is called 156 times, resulting in 619 milliseconds of duration. Other time-consuming routines were: `isapnp_isolate()`, `get_cmos_time()`, `default_idle()`.

The top line showing schedule() called 192 times and lasting over 5 seconds, is accounted wrong due to the switch in execution control inside the schedule routine. (The count of 192 calls is correct, but the duration is wrong.)

```
$ ~/work/kft/kft/kd -n 30 kftboot-9.lst
Function                  Count Time     Average  Local
------------------------- ----- -------- -------- --------
schedule                    192  5173790    26946  5173790
do_basic_setup                1  1159270  1159270       14
do_initcalls                  1  1159256  1159256      627
__delay                     156   619322     3970        0
delay_tsc                   156   619322     3970   619322
__const_udelay              146   608427     4167        0
probe_hwif                    8   553972    69246      126
do_probe                     31   553025    17839       68
ide_delay_50ms              103   552588     5364        0
isapnp_init                   1   383138   383138       18
isapnp_isolate                1   383120   383120   311629
ide_init                      1   339778   339778       22
probe_for_hwifs               1   339756   339756      103
ide_scan_pcibus               1   339653   339653       13
init_setup_piix               2   339640   169820        0
ide_scan_pcidev               2   339640   169820        0
piix_init_one                 2   339640   169820        0
ide_setup_pci_device          2   339640   169820      242
probe_hwif_init               4   339398    84849       40
time_init                     1   266911   266911        0
get_cmos_time                 1   261404   261404   261404
ide_generic_init              1   214614   214614        0
ideprobe_init                 1   214614   214614        0
wait_for_completion           6   194573    32428        0
default_idle                183   192589     1052   192589
io_schedule                  18   171313     9517        0
__wait_on_buffer             14   150369    10740      141
i8042_init                    1   137210   137210      295
i8042_port_register           2   135318    67659      301
__serio_register_port         2   135017    67508        0 
```

#### kft nested call trace with 'kd -c'

Below is a `kd -c` trace of the data from a log taken from a PPC440g platform, from a (dynamic) trace of the function do_fork().

Here is the configuration file that was used:

```
new
begin
  trigger start entry do_fork
  trigger stop exit do_fork
end 
```

Here is the first part of the trace in nested call format: Times (Entry, Duration and Local) are in micro-seconds. Note the timer interrupt during the routine.

```
 Entry      Duration   Local       Pid    Trace
---------- ---------- ---------- ------- ---------------------------------
        4      20428        209      33 do_fork
        7          6          6      33 |  alloc_pidmap
       18       2643         84      33 |  copy_process
       21        114         19      33 |  |  dup_task_struct
       24          8          6      33 |  |  |  prepare_to_copy
       27          2          2      33 |  |  |  |  sub_preempt_count
       35         22          9      33 |  |  |  kmem_cache_alloc
       38          2          2      33 |  |  |  |  __might_sleep
       43         11          9      33 |  |  |  |  cache_alloc_refill
       49          2          2      33 |  |  |  |  |  sub_preempt_count
       60         65          6      33 |  |  |  __get_free_pages
       63         59         14      33 |  |  |  |  __alloc_pages
       65          3          3      33 |  |  |  |  |  __might_sleep
       71          3          3      33 |  |  |  |  |  zone_watermark_ok
       77         37         17      33 |  |  |  |  |  buffered_rmqueue
       80          4          4      33 |  |  |  |  |  |  __rmqueue
       86          3          3      33 |  |  |  |  |  |  sub_preempt_count
       92          3          3      33 |  |  |  |  |  |  bad_range
       98          2          2      33 |  |  |  |  |  |  __mod_page_state
      103          8          5      33 |  |  |  |  |  |  prep_new_page
      106          3          3      33 |  |  |  |  |  |  |  set_page_refs
      117          2          2      33 |  |  |  |  |  zone_statistics
      141         25          4      33 |  |  do_posix_clock_monotonic_gettime
      143         21          6      33 |  |  |  do_posix_clock_monotonic_get
      146         15          6      33 |  |  |  |  do_posix_clock_monotonic_gettime_parts
      149          9          6      33 |  |  |  |  |  getnstimeofday
      152          3          3      33 |  |  |  |  |  |  do_gettimeofday
      169          3          3      33 |  |  copy_semundo
      174         41         17      33 |  |  copy_files
      177         19          9      33 |  |  |  kmem_cache_alloc
      180          2          2      33 |  |  |  |  __might_sleep
      185          8          5      33 |  |  |  |  cache_alloc_refill
      188          3          3      33 |  |  |  |  |  sub_preempt_count
      200          3          3      33 |  |  |  count_open_files
      209          2          2      33 |  |  |  sub_preempt_count
      218         19          8      33 |  |  kmem_cache_alloc
      220          2          2      33 |  |  |  __might_sleep
      225          9          6      33 |  |  |  cache_alloc_refill
      229          3          3      33 |  |  |  |  sub_preempt_count
      241          2          2      33 |  |  sub_preempt_count
      246        216          9      33 |  |  kmem_cache_alloc
      249        199        199      33 |  |  |  __might_sleep
----------- !!!! start --------------
      253        151         63      33 timer_interrupt
      256          8          6      -1 !  profile_tick
      259          2          2      -1 !  !  profile_hit
      267         61         15      -1 !  update_process_times
      270          8          5      -1 !  !  account_system_time
      273          3          3      -1 !  !  !  update_mem_hiwater
      281          8          5      -1 !  !  run_local_timers
      284          3          3      -1 !  !  !  raise_softirq
      293         27         16      -1 !  !  scheduler_tick
.
.
. 
```

### Appendix C - Using KFT for monitoring stack usage

```
* configure CONFIG_KFI_SAVE_SP (if saving the stack pointer as part of trace data)
* 
```

### Appendix D - Some notes on KFT operation

#### Trace overhead

KFT uses the "-finstrument-functions" capability of the gcc compiler to add instrumentation callouts to every kernel function entry and exit. This generates a large amount of overhead during kernel execution, even if a trace is not active. For this reason, KFT is turned off in the default configuration for your target board.

This high overhead means that using KFT may interfere with time-sensitive operations on your device. You should be careful when interpreting performance results on you device when KFT is configured on in your kernel, whether the results are obtained from KFT or from some other performance measurement tool. KFT is great at providing data for relative performance comparisons, but not for absolute performance timings.

* * *

Performance: KFT adds a fair amount of overhead to kernel execution. The reason for this is that the compiler adds instrumentation hooks to the start and end of every function. These hooks take additional time to execute. When a trace is active, even more time is used as events are compared against triggers and filters, and as events are logged to the trace buffer. It would be inappropriate to use an instrumented kernel for production use.

Local-time: Be careful when using the 'local time' numbers provided by 'kd'. These are calculated using the entry and exit times for the functions, and then subtracting the duration of other functions called during the top function's lifetime. However, due to filtering, interrupt handling, or context-switching, these numbers can be way off.

##### Overhead measurement

Mitsubishi measured the overhead of KFI (the predecessor to KFT) The period is from start_kernel() to smp_init().

Platform was: SH7751R 240MHz (Memory Clock 80MHz)

```
With KFI    : 922.419 msec
Without KFI : 666.982 msec
Overhead    : 27.69% 
```

#### Trace buffer exhaustion

Because every function in the kernel is traced, with certain trace configuration settings it is possible to VERY rapidly fill up the trace buffer. Kernel functions are executed several thousand times a second, even when the machine appears to be doing nothing.

The trace buffer is not circular. As soon as the buffer fills up with data, the trace capture automatically stops. For this reason, it is common to have the trace buffer exhausted during a trace.

How to fix:

```
* increase the trace buffer size
* use filters
  * filter only by certain functions
  * increase the minimum function duration to save in the trace 
```

* * *

Q. Is there a way to adjust the trigger or filters to reduce the memory usage?

A. The memory usage is determined by the size of the log, which is specified by `logentries` in the KFT configuration. If `logentries` is not specified, it defaults to a rather large number (20,000 in the current code). To use a smaller trace log, specify a smaller number of logentries in the KFT configuration.

The use of triggers and filters can help you fit more data (or more pertinent data) into the log, so you can more readily see the information you are interested in.

By setting start and stop triggers with a narrower "range" of operation, then the amount of data put into the log will be more limited. For example, the default configuration for a static trace uses

```
trigger start entry start_kernel
trigger stop entry to_userspace 
```

This will trace EVERYTHING that the kernel does between those two routines. However, you can limit tracing to a much smaller time area of kernel initialization using better triggers. Here is an example showing a triggers for just watching mem_init():

```
trigger start entry mem_init
trigger stop exit mem_init 
```

Filters are also vital to reduce the number of entries the trace log. With no time filters in place, KFT will log every single function executed by the kernel. This will quickly overrun the log (no matter what size you have reserved with `logentries`.

When using KFT to find long-duration functions in the kernel, we usually are not interested in routines that execute quickly, and instead use something like "filter mintime 500" to filter out routines taking less than 500 microseconds.

#### Early clock issues

On many platforms, the clock used for performing trace timings is not available immediately when the kernel begins execution. Often, the clock is initialized sometime during the time_init() function of kernel startup. In this case, the function entry times and durations may be incorrect, for functions which begin before the clock is set up. Also time-related filters will not operate correctly on these functions. Usually, this is not a problem, since the times come back as zero, and any minimum time filters in place will remove the events from the trace buffer.

The result of all this is that, on machines where the clock is not immediately available at kernel start, there will be a "blind spot" during initialization, which is effectively not traceable by KFT. You can get event data for this "blind" period, by turning off the time filter for events, but this will result in a very large set of events (all without valid timing information) at the beginning of the event log.

##### Adding platform support for the KFT clock source

By default, KFT uses sched_clock() as the clock source for event timings. This is called from the routine kft_readclock().

sched_clock() is new in the 2.6 kernel, and returns a 64-bit value containing nanoseconds (not necessarily relative to any particular time base, but assumed to be monotonically increasing, and relatively frequency-stable.)

If your platform has good support for sched_clock(), then KFT should work for you unmodified. If not, you may wish to do one of two things:

*   improve support for sched_clock() in your board port, or
*   write a custom kft_readclock() routine.

A "good" sched_clock() routine will provide at least microsecond resolution on return values. Some architectures have sched_clock() returning values based on the `jiffy` variable, which on many embedded platforms only has resolution to 10 milliseconds.

There are some sample custom kft_readclock() routines in the current code for different architectures. These alternate routines are not active, via pre-processor conditionals. However, you can use them for samples of how to write your own custom KFT clock routine.

## Modification History

| **Date** | **Version** | **Description** |  |
| --- | --- | --- | --- |
| 2006-10-13 | 0.1.0 | First draft of document |  |
| 2007-04-26 | 0.1.1 | Fixed "run run" typo, and added some material on filters and triggers |  |

[Categories](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")
*   [Tips and Tricks](http://eLinux.org/Category:Tips_and_Tricks "Category:Tips and Tricks")

# Linux Kernel State Tracer

> From: [eLinux.org](http://eLinux.org/Linux_Kernel_State_Tracer "http://eLinux.org/Linux_Kernel_State_Tracer")

# Linux Kernel State Tracer

Table Of Contents:

## Contents

*   1 Description
    *   1.1 Rationale
*   2 Resources
    *   2.1 Projects
    *   2.2 Documents and presentations
    *   2.3 Specifications
*   3 Downloads
    *   3.1 Patch
    *   3.2 Utility programs
*   4 How To Use
*   5 How to validate
*   6 Sample Results
    *   6.1 Case Study 1
    *   6.2 Case Study 2
*   7 Status
*   8 Future Work/Action Items

## Description

[This section describes the technology ....]

LKST is Kernel State Tracer for Linux in order to keep track of Kernel Event such as:

```
 - Process Management
 - Interrupt
 - Exceptions
 - System Calls
 - Memory Managements
 - Networking: sending packets, receiving packets
 - Sys V IPC
 - Locks
 - Timer
 - Oops 
```

Originally LKST is developed for Linux Enterprise Systems and now we have port it to Reference Boards for Embedded Systems and currently SH4 port(RTS7751R2D), MIPS/TX49 port (RBHMA4400CE) and ARM/OMAP port (TI OMAP INNOVATOR/OSK) are available.

### Rationale

LKST is one of a number of tracing systems available for the Linux kernel. Such event tracing systems are very useful for analyzing kernel behaviour, and learning how interrupts, kernel threads and user-space applications interact on the system.

## Resources

### Projects

Here is some information about LKST:

*   project home page: [`lkst.sourceforge.net/`](http://lkst.sourceforge.net/)

### Documents and presentations

*   [Media:CELF_LKST_SH_Presen-2005-1.pdf](http://eLinux.org/images/a/a4/CELF_LKST_SH_Presen-2005-1.pdf "CELF LKST SH Presen-2005-1.pdf")
    *   presentation given by Hitachi at CELF Jan 2005 technical conference.
*   [Media:CELF_LKST_SH_Lineo-2005-2.pdf](http://eLinux.org/images/b/b2/CELF_LKST_SH_Lineo-2005-2.pdf "CELF LKST SH Lineo-2005-2.pdf")
    *   presentation given by Lineo at CELF Jan 2005 technical conference.
*   [Media:HITACHI-LKST-CELF-200601.pdf](http://eLinux.org/images/4/46/HITACHI-LKST-CELF-200601.pdf "HITACHI-LKST-CELF-200601.pdf")
    *   presentation given at International Technical Conference, June 2006
*   [Media:CELFTokyoJam6_LkstUpdate_Lineo.pdf](http://eLinux.org/images/1/13/CELFTokyoJam6_LkstUpdate_Lineo.pdf "CELFTokyoJam6 LkstUpdate Lineo.pdf")
    *   presentation 'Features of lkslogtools' given at CELF Jan 2006 technical jamboree (6)

### Specifications

## Downloads

### Patch

*   You can acquire patches at: [`sourceforge.net/projects/lkst/`](http://sourceforge.net/projects/lkst/)
*   and click the link of |Patches|: [`sourceforge.net/tracker/?group_id=41854&atid=431465`](http://sourceforge.net/tracker/?group_id=41854&atid=431465)

### Utility programs

[other programs, user-space, test, etc. related to this technology]

## How To Use

## How to validate

[put references to test plans, scripts, methods, etc. here]

## Sample Results

[Examples of use with measurement of the effects.]

### Case Study 1

### Case Study 2

## Status

*   Status: [implemented]

```
 (one of: not started, researched, implemented, measured, documented, accepted) 
```

*   Architecture Support:

```
 (for each arch, one of: unknown, patches apply, compiles, runs, works, accepted) 
```

*   *   i386: works
    *   x86_64: works
    *   ia64: works
    *   ARM: runs
    *   PPC: unknown
    *   MIPS: runs
    *   SH: runs

## Future Work/Action Items

Here is a list of things that could be worked on for this feature:

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")