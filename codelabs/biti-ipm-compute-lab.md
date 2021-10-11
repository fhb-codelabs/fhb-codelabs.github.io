summary: BITI IPM Lab - Compute
id: biti-ipm-compute-lab
categories: linux
tags: ipm, compute, cpu, BITI, introduction
status: Published
authors: Roland Pellegrini

# BITI IPM Lab - Compute
<!-- ------------------------ -->
## Before You Begin 

### What You’ll Learn
Every computer system benefits from proper administration and monitoring. A computer operates more quickly if few CPU-intensive processes are running at the same time.

Therefore, it is important to know about the CPUs, how they are used and how fast they are. Additionally, it makes sense that administrators check the CPU usage on a regular basis to identify programs that unnecessarly burden the system.

Luckily, IT administrators don't need special system information programs to find this information.There are plenty of command line utilities created for this purpose. This codelab will introduce you to some of the most helpful applications and how to use them.

###  Where You Can Look Up
The **man** is a short term for manual page and acts as an interface to view the reference manual of a command.

Syntax of man:
```
man [option(s)] keyword(s)
```

For example, if you want top find out more about the command **ps** and how to use it, just open a shell and type:
```
man ps
```

This command will display all the information about **ps**.
```
PS(1)                            User Commands                           PS(1)

NAME
       ps - report a snapshot of the current processes.

SYNOPSIS
       ps [options]

DESCRIPTION
       ps displays information about a selection of the active processes.  If
       you want a repetitive update of the selection and the displayed
       information, use top(1) instead.
...
...
```
### What You'll need

#### Guest operation system (Guest OS)

This is the OS of the virtual machine. This will be Debian 11 (Bullseye).

#### Administators privileges

By default, administrator privileges are required on the Host OS to install additional software. Make sure that you have the required permissions.

For the Guest OS, you will create and manage your own users. These users will therefore be different from the Host's user administration. 

## Display CPU information on Linux


### What You will learn:

You can use one of the following commands to find detailed information about the physical CPUs (pCPU) including all cores on Linux:

* nproc command
* lscpu command
* cat /proc/cpuinfo
* hwinfo command
* dmidecode -t processor command
* getconf _NPROCESSORS_ONLN command


### #1. The nproc command

The **nproc** command displays the number of available processing units. The command is a built-in command in a Linux system.

Open a shell and run **nproc** with the following option:
```
echo "Threads/core: $(nproc --all)"
```

Sample output:
```
Threads/core: 4
```

<aside class="positive">
Without the --all flag, nproc will display the number of processing units available to the current process.<br>
Use the --help flag for more information.
</aside>


### #2. The lscpu command

On most Linux distributions, you can also use the **lscpu** command. It displays not only the number of processors but also the architecture, model name, speed, much more.

Open a shell and run the following command:
```
lscpu
lscpu | egrep 'Model name|Socket|Thread|NUMA|CPU\(s\)'
```

Sample outputs:
```
icinga@server:/# lscpu
Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
Address sizes:                   39 bits physical, 48 bits virtual
CPU(s):                          4
On-line CPU(s) list:             0-3
Thread(s) per core:              2
Core(s) per socket:              2
Socket(s):                       1
NUMA node(s):                    1
Vendor ID:                       GenuineIntel
CPU family:                      6
Model:                           78
Model name:                      Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz
Stepping:                        3
CPU MHz:                         800.076
CPU max MHz:                     3400.0000
CPU min MHz:                     400.0000
BogoMIPS:                        5599.85
Virtualization:                  VT-x
L1d cache:                       64 KiB
L1i cache:                       64 KiB
L2 cache:                        512 KiB
L3 cache:                        4 MiB
NUMA node0 CPU(s):               0-3
Vulnerability Itlb multihit:     KVM: Mitigation: VMX disabled
Vulnerability L1tf:              Mitigation; PTE Inversion; VMX conditional cach
                                 e flushes, SMT vulnerable
Vulnerability Mds:               Mitigation; Clear CPU buffers; SMT vulnerable
Vulnerability Meltdown:          Mitigation; PTI
Vulnerability Spec store bypass: Mitigation; Speculative Store Bypass disabled v
                                 ia prctl and seccomp
Vulnerability Spectre v1:        Mitigation; usercopy/swapgs barriers and __user
                                  pointer sanitization
Vulnerability Spectre v2:        Mitigation; Full generic retpoline, IBPB condit
                                 ional, IBRS_FW, STIBP conditional, RSB filling
Vulnerability Srbds:             Vulnerable: No microcode
Vulnerability Tsx async abort:   Mitigation; Clear CPU buffers; SMT vulnerable
Flags:                           fpu vme de pse tsc msr pae mce cx8 apic sep mtr
                                 r pge mca cmov pat pse36 clflush dts acpi mmx f
                                 xsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rd
                                 tscp lm constant_tsc art arch_perfmon pebs bts 
                                 rep_good nopl xtopology nonstop_tsc cpuid aperf
                                 mperf pni pclmulqdq dtes64 monitor ds_cpl vmx s
                                 mx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid s
                                 se4_1 sse4_2 x2apic movbe popcnt tsc_deadline_t
                                 imer aes xsave avx f16c rdrand lahf_lm abm 3dno
                                 wprefetch cpuid_fault epb invpcid_single pti ss
                                 bd ibrs ibpb stibp tpr_shadow vnmi flexpriority
                                  ept vpid ept_ad fsgsbase tsc_adjust bmi1 hle a
                                 vx2 smep bmi2 erms invpcid rtm mpx rdseed adx s
                                 map clflushopt intel_pt xsaveopt xsavec xgetbv1
                                  xsaves dtherm ida arat pln pts hwp hwp_notify 
                                 hwp_act_window hwp_epp md_clear flush_l1d

icinga@server:$ lscpu | egrep 'Model name|Socket|Thread|NUMA|CPU\(s\)'
CPU(s):                          4
On-line CPU(s) list:             0-3
Thread(s) per core:              2
Socket(s):                       1
NUMA node(s):                    1
Model name:                      Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz
NUMA node0 CPU(s):               0-3
```

The output shows the CPU information:

* CPU model: Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz
* Socket: 1
* CPU Cores: 2
* Thread per core: 2 
* Total threads: 4

### #3. cpuinfo

All Linux distributions allows you to run **cat /proc/cpuinfo**. This file contains details about the processors installed. Note that /proc is a pseudo-filesystem, it is used as an interface to kernel data structures.

Open a shell and run the following command:
```
cat /proc/cpuinfo | more
```

Information is grouped per logical processor. That means that every processor is listed separately and various details about the CPU are included in the description. 

```
icinga@server:/# cat /proc/cpuinfo | more
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 78
model name	: Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz
stepping	: 3
microcode	: 0xcc
cpu MHz		: 2144.824
cache size	: 4096 KB
physical id	: 0
siblings	: 4
core id		: 0
cpu cores	: 2
apicid		: 0
initial apicid	: 0
fpu		: yes
fpu_exception	: yes
cpuid level	: 22
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp l
m constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16
 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb invpcid_single pti ssbd ibrs
 ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm mpx rdseed adx smap clflushopt intel_pt xsaveop
t xsavec xgetbv1 xsaves dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp md_clear flush_l1d
vmx flags	: vnmi preemption_timer invvpid ept_x_only ept_ad ept_1gb flexpriority tsc_offset vtpr mtf vapic ept vpid unrestricted_guest ple shadow_vmcs pml
bugs		: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs taa itlb_multihit srbds
bogomips	: 5599.85
clflush size	: 64
cache_alignment	: 64
address sizes	: 39 bits physical, 48 bits virtual
power management:

processor	: 1
vendor_id	: GenuineIntel
cpu family	: 6
model		: 78
model name	: Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz
stepping	: 3
microcode	: 0xcc
cpu MHz		: 2354.343
cache size	: 4096 KB
physical id	: 0
siblings	: 4
core id		: 1
cpu cores	: 2
apicid		: 2
initial apicid	: 2
fpu		: yes
fpu_exception	: yes
cpuid level	: 22
--More--
```

<aside class="positive">
Consult the documentation and man-pages for more details. Try to identify keywords and details of the CPU. 
</aside>

To count the number of processing units use grep command.
```
cat /proc/cpuinfo | grep processor
```

Sample outputs:
```
icinga@server:/# cat /proc/cpuinfo | grep processor
processor	: 0
processor	: 1
processor	: 2
processor	: 3
```

Let us print cpu thread count:

```
echo "CPU threads: $(grep -c processor /proc/cpuinfo)"
grep 'cpu cores' /proc/cpuinfo | uniq
```

Sample outputs:
```
icinga@server:/# echo "CPU threads: $(grep -c processor /proc/cpuinfo)"
CPU threads: 4
icinga@server:/# grep 'cpu cores' /proc/cpuinfo | uniq
cpu cores	: 2
```

<aside class="positive">
The number of processors shown by /proc/cpuinfo might be different from the actual number of cores on the processor. For example a processor with 4 cores and hyperthreading would be reported as a processor with 8 cores.
</aside>

### #4. The hwinfo command

Hwinfo checks details of the hardware present in a Linux system and displays the extensive details of each hardware device. It reports information about most hardware units including CPU, HDD controllers, network card, graphics cards, and more.

<aside class="positive">
Hwinfo requires root privileges.
</aside>

```
hwinfo | more
```

Sample output

```
root@server:/# hwinfo | more
============ start debug info ============
libhd version 21.72 (x86-64) [7688]
using /var/lib/hardware
kernel version is 5.10
----- /proc/cmdline -----
  BOOT_IMAGE=/vmlinuz-5.10.0-9-amd64 root=/dev/mapper/cat--vg-root ro quiet
----- /proc/cmdline end -----
debug = 0xff7ffff7
probe = 0x15938fcdaa17fcf9fffe (+memory +pci +isapnp +net +floppy +misc +misc.serial +misc.par +misc.floppy +serial +cpu +bios +monitor +mouse +scsi +usb -usb.mods +
modem +modem.usb +parallel +parallel.lp +parallel.zip -isa -isa.isdn +isdn +kbd +prom +sbus +int +braille +braille.alva +braille.fhp +braille.ht -ignx11 +sys -bios.v
be -isapnp.old -isapnp.new -isapnp.mod +braille.baum -manual +fb +pppoe -scan +pcmcia +fork -parallel.imm +s390 +cpuemu -sysfs -s390disks +udev +block +block.cdrom +
block.part +edd +edd.mod -bios.ddc -bios.fb -bios.mode +input +block.mods +bios.vesa -cpuemu.debug -scsi.noserial +wlan -bios.crc -hal +bios.vram +bios.acpi -bios.dd
c.ports=0 +modules.pata -net.eeprom +x86emu=dump -max -lxrc)
shm: attached segment 26 at 0x7f2ea7189000
>> hal.1: read hal data
>> floppy.1: get nvram
----- /proc/nvram -----
  Checksum status: not valid
  # floppies     : 4
  Floppy 0 type  : 12 (unknown)
  Floppy 1 type  : 15 (unknown)
  HD 0 type      : db
  HD 1 type      : 0b
  HD type 48 data: 64495/253/247 C/H/S, precomp 61423, lz 61135
  HD type 49 data: 51147/70/0 C/H/S, precomp 0, lz 0
  DOS base memory: 65478 kB
  Extended memory: 20391 kB (configured), 50941 kB (tested)
  Gfx adapter    : monochrome
  FPU            : installed
----- /proc/nvram end -----
>> floppy.2: nvram info
>> bios.1: cmdline
>> bios.1.1: apm
>> bios.2: ram
  bios: 0 disks
>> bios.2: rom
----- SMBIOS Entry Point (sysfs) 0x00000 - 0x00017 -----
  000  5f 53 4d 33 5f fa 18 03 02 00 01 00 13 04 00 00  "_SM3_..........."
  010  00 d0 e5 8b 00 00 00 00  "........"
----- SMBIOS Entry Point (sysfs) end -----
  Found DMI table at 0x8be5d000 (0x0413 bytes max)
  Got DMI table from sysfs (0x0413 bytes)
----- SMBIOS Structure Table 0x8be5d000 - 0x8be5d412 -----
  8be5d000  00 1a 00 00 01 02 00 00 03 ff 80 18 19 0c 00 00  "................"
  8be5d010  00 00 03 0d ff ff ff ff 00 00 4d 69 63 72 6f 73  "..........Micros"
  8be5d020  6f 66 74 20 43 6f 72 70 6f 72 61 74 69 6f 6e 00  "oft Corporation."
  8be5d030  39 32 2e 33 31 39 32 2e 37 36 38 00 30 33 2e 32  "92.3192.768.03.2"
  8be5d040  34 2e 32 30 32 30 00 00 01 1b 01 00 01 02 03 04  "4.2020.........."
  8be5d050  45 c0 37 1a 5f 88 e5 5e 47 36 4c 06 5b 0d db 39  "E.7._..^G6L.[..9"
  8be5d060  02 05 06 4d 69 63 72 6f 73 6f 66 74 20 43 6f 72  "...Microsoft Cor"
--More--
```

The "--short" option will display brief information about the hardware and not the details.
With the "--cpu" option, hwinfo would display only cpu information.

```
hwinfo --short --cpu
```

Sample output

```
root@server# hwinfo --short --cpu
cpu:                                                            
                       Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz, 2550 MHz
                       Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz, 1075 MHz
                       Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz, 1841 MHz
                       Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz, 2463 MHz
```

Finally, the hwinfo has an option to log all data to a file. The following command will log detailed information about all hardware units to a text file.

```
hwinfo --all --log hardware_info.txt
```


### #5. The dmidecode command

The Linux command **dmidecode** reads the data from DMI (Desktop Management Interface) table which holds information for system’s hardware components like BIOS Revision, Serial Number, CPU, RAM, etc.

The tools is useful when users run into situations where they need to find out maximum RAM supported by the BIOS and motherboard, a serial number or a key piece of hardware information during troubleshooting. 

<aside class="positive">
Hwinfo requires root privileges.
</aside>

To get information about the CPU, run dmidecode with -t option as shown below:

```
dmidecode -t processor | more
```

Sample output:

```
root@server# dmidecode -t processor | more
# dmidecode 3.3
Getting SMBIOS data from sysfs.
SMBIOS 3.2.0 present.

Handle 0x000D, DMI type 4, 48 bytes
Processor Information
	Socket Designation: U3E1
	Type: Central Processor
	Family: Core i7
	Manufacturer: Intel(R) Corporation
	ID: E3 06 04 00 FF FB EB BF
	Signature: Type 0, Family 6, Model 78, Stepping 3
	Flags:
		FPU (Floating-point unit on-chip)
		VME (Virtual mode extension)
		DE (Debugging extension)
		PSE (Page size extension)
		TSC (Time stamp counter)
		MSR (Model specific registers)
		PAE (Physical address extension)
		MCE (Machine check exception)
		CX8 (CMPXCHG8 instruction supported)
		APIC (On-chip APIC hardware supported)
		SEP (Fast system call)
		MTRR (Memory type range registers)
		PGE (Page global enable)
		MCA (Machine check architecture)
		CMOV (Conditional move instruction supported)
		PAT (Page attribute table)
		PSE-36 (36-bit page size extension)
		CLFSH (CLFLUSH instruction supported)
		DS (Debug store)
		ACPI (ACPI supported)
		MMX (MMX technology supported)
		FXSR (FXSAVE and FXSTOR instructions supported)
		SSE (Streaming SIMD extensions)
		SSE2 (Streaming SIMD extensions 2)
		SS (Self-snoop)
		HTT (Multi-threading)
		TM (Thermal monitor supported)
		PBE (Pending break enabled)
	Version: Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz
	Voltage: 0.9 V
	External Clock: 100 MHz
	Max Speed: Unknown
	Current Speed: 2500 MHz
	Status: Populated, Enabled
	Upgrade: Socket BGA1356
	L1 Cache Handle: 0x000A
	L2 Cache Handle: 0x000B
--More--
```

You can further filter details with egrep command to filter only the required details.

```
dmidecode -t processor | egrep "Family|Manufacturer|Version|Max Speed|Core Count|Thread Count" 
```

Sample output:
```
	Family: Core i7
	Manufacturer: Intel(R) Corporation
	Signature: Type 0, Family 6, Model 78, Stepping 3
	Version: Intel(R) Core(TM) i7-6600U CPU @ 2.60GHz
	Max Speed: Unknown
	Core Count: 2
	Thread Count: 4
```

### #6. The getconf command
The Linux command **getconf** displays the value of a specified configuration variable on the standard output. The **getconf** command is most portable solution as it runs on Linux and Mac OS X as well.

The option `-a` displays all configuration variables for the current system and their values, on standard output. To get a list of all configuration variables for the current system, run the following command

```
getconf -a
```

Sample output:
```
getconf -a | more
LINK_MAX                           65000
_POSIX_LINK_MAX                    65000
MAX_CANON                          255
_POSIX_MAX_CANON                   255
MAX_INPUT                          255
--More--
```

To get the number of CPU/Cores, run the following command:
```
echo "Number of CPU/cores online at $HOSTNAME: $(getconf _NPROCESSORS_ONLN)"
```

Sample output:
```
Number of CPU/cores online at server: 4
```

## CPU monitoring tools on Linux

### What you will learn:

You can use one of the following command to monitor the physical CPU cores including all cores on Linux:

* uptime command
* top program
* htop program
* mpstat command


### #1. The uptime command 

The command **uptime** shows the basic information. The Linux tool requires few system resources only and can be helpful when a system is responding slow.


Open a shell  and run the command as follows:

```
uptime
```

The output may look like this:

```
17:58:21 up 1 day, 22:38,  1 user,  load average: 0.54, 0.51, 0.69
```

The command shows :

- the system time at the moment the command was run (here: 5.58pm)
- how long the server had been running (here: 1 day)
- how many connections users had to the machine (here: 1 user)
- the CPU load average for the past one, five, and fifteen minutes (here: 0.54, 0.51, 0.69)

To see load changes on screen, run the `watch` command as follows:

```
watch uptime
```

<aside class="positive">
Consult your documentation about the CPU load average, its meaning and how to interpret the numbers.
</aside>


### #2. top

One of the most common Linux tools for checking the CPU utilization is **top**. <br><br>
Like **uptime**, the command **top** displays the CPU load averages. In addition, the command provides real-time CPU usage information as well as other performance metrics. Furthermore, top stays in the foreground and refreshes at regular intervals.

Run the **top** command as follows:
```
top
```

The output will probably be very different from yours:
![top](./img/biti-ipm-compute-linux-top.png)

The first five lines (also called the Header Block) provide a summary about the processes on the server. The Header Block is followed by a table  (the Process Table) with information about each individual process.

<aside class="positive">
Consult the man-pages for more details about top, about the Header Block and the Process Table. Run your own research about the output and how to interpret it.
</aside>

### #3. htop 

This tool is another interactive real-time process monitoring application for Linux. It has numerous nice user-friendly features which are not availble under the **top** command.

Unfortunately, the program is not installed by default. Run the following command to install **htop** with the privileges of the root account:
```
apt update; apt install htop
```

<aside class="positive">
If errors occur due to lack of root privilegues, check the section Prerequisites for more details. 
</aside>


After successful installation, run the **htop** command as follows:
```
htop
```

The output will probably be very different from yours:
![htop](./img/biti-ipm-compute-linux-htop.png)

The top section is more easier to read and the bottom section is better organized.


<aside class="positive">
Consult the man-pages for more details about htop. Run your own research about the output and how to interpret it.
</aside>


### #4. mpstat 

The tool **mpstat** is a Linux command that is used to report processor related statistics. It displays the statistics of the CPU usage of the system and information about CPU utilization. It initializes the first processor with CPU 0, the second one with CPU 1, and so on.


The tool **mpstat** is part of the software package **sysstat**. Like **htop**, the program is not installed by default. Run the following command to install the **systat** package with the privileges of the root account:
```
apt update; apt install sysstat
```

<aside class="positive">
If errors occur, execute the command **su -** first before running the installation process. You will be prompted for the root password. Ask your instructor for the password and run the above command again. 
</aside>


After successful installation, run the **mpstat** command as follows:
```
mpstat
```

The output will probably be very different from yours:
```
Linux 5.10.0-8-amd64 (debian)   07/21/2021      _x86_64_        (2 CPU)

10:13:59 AM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:13:59 AM  all    7.67    0.06    1.82    0.46    0.00    0.21    0.00    0.00    0.00   89.77
```


Next, to display processor numbers to all CPUs, run the command with the following flags:
```
mpstat -P ALL
```

Again, the output will probably be very different from yours:
```
Linux 5.10.0-8-amd64 (debian)   07/21/2021      _x86_64_        (2 CPU)

10:15:43 AM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:15:43 AM  all    7.14    0.05    1.64    0.40    0.00    0.18    0.00    0.00    0.00   90.59
10:15:43 AM    0    6.96    0.04    1.70    0.38    0.00    0.21    0.00    0.00    0.00   90.70
10:15:43 AM    1    7.33    0.06    1.57    0.41    0.00    0.16    0.00    0.00    0.00   90.47
```


Next, to get all information, run the command with the following flag:
```
mpstat -A
```
This will display each and every detail of CPU usage.:
```
Linux 5.10.0-8-amd64 (debian)   07/21/2021      _x86_64_        (2 CPU)

10:18:41 AM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:18:41 AM  all    6.45    0.04    1.39    0.32    0.00    0.16    0.00    0.00    0.00   91.65
10:18:41 AM    0    6.33    0.04    1.44    0.30    0.00    0.17    0.00    0.00    0.00   91.72
10:18:41 AM    1    6.57    0.05    1.33    0.33    0.00    0.15    0.00    0.00    0.00   91.57

10:18:41 AM NODE    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:18:41 AM  all    6.45    0.04    1.39    0.32    0.00    0.16    0.00    0.00    0.00   91.65
10:18:41 AM    0    6.45    0.04    1.39    0.32    0.00    0.16    0.00    0.00    0.00   91.65

10:18:41 AM  CPU    intr/s
10:18:41 AM  all    684.45
10:18:41 AM    0     85.41
10:18:41 AM    1    532.59

10:18:41 AM  CPU        0/s        1/s        8/s        9/s       12/s       14/s       15/s       18/s       19/s       20/s       21/s       22/s      NMI/s      LOC/s      SPU/s      PMI/s      IWI/s      RTR/s      RES/s      CAL/s      TLB/s      TRM/s      THR/s      DFR/s      MCE/s      MCP/s      ERR/s      MIS/s      PIN/s      NPI/s      PIW/s
10:18:41 PM    0       0.03       0.00       0.00       0.00       0.32       0.00       1.16       3.32       0.00       0.00      26.69       0.00       0.00     123.81       0.00       0.00       0.00       0.00       0.69      64.39       2.54       0.00       0.00       0.00       0.00       0.00       0.00       0.01       0.00       0.00       0.00
10:18:41 PM    1       0.00       0.59       0.00       0.00       0.00       0.00       0.00       0.00       0.91       3.29       0.00       0.03       0.00     378.77       0.00       0.00       0.00       0.00       0.68      79.76       3.14       0.00       0.00       0.00       0.00       0.00       0.00       0.00       0.00       0.00       0.00

10:18:41 AM  CPU       HI/s    TIMER/s   NET_TX/s   NET_RX/s    BLOCK/s IRQ_POLL/s  TASKLET/s    SCHED/s  HRTIMER/s      RCU/s
10:18:41 AM    0       0.00      14.51       0.00       0.06      13.04       0.00       0.04      26.58       0.00      31.18
10:18:41 AM    1       0.00     247.50       0.20       0.93      12.66       0.00       0.60     233.72       0.00      36.96
```

Finally, to display CPU utilization by a specific processor, run the command with the following flags:

Next, to get all information, run the command with the following flag:
```
mpstat -P 1
```

This will display CPU usage of the 1st processor:
```
Linux 5.10.0-8-amd64 (debian)   07/21/2021      _x86_64_        (2 CPU)

10:25:23 AM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:25:23 AM    1    5.89    0.03    1.03    0.24    0.00    0.13    0.00    0.00    0.00   92.68

```

<aside class="positive">
Consult the man-pages for more details about mpstat. Do your own research about the output and how to interpret the results.
</aside>

## Display CPU information on Microsoft Windows

### What You will learn:

You can use one of the following commands to find some information about the physical CPUs (pCPU) including all cores on Windows:

* msinfo32 command
* systeminfo command
* wmic command
* Windows Registry

### #1. msinfo32 / System Information
The tool **msinfo32** is a built-in system profiler for Microsoft Windows which collects and displays system information about the the Operating systems, hard- and software.

To launch **msinfo32**, simple press the **Win+R** keys, type **msinfo32** and click the **OK**-button.

![Windows Registry](./img/biti-ipm-compute-windows-msinfo32.png)

Details about the CPU can be found in the System Summary section at the Processor value in the right pane.

### #2. Systeminfo

Using this method, you can find out system information in Windows PowerShell.

* Launch Windows PowerShell as an administrator, enter the command:

```
systeminfo
```

* The Windows PowerShell window displays some information about the processor on this computer.
```
Host Name:                 DESKTOP-325I5B9
OS Name:                   Microsoft Windows 10 Education
OS Version:                10.0.19042 N/A Build 19042
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          Windows User
Registered Organization:
Product ID:                00328-00000-00000-AA697
Original Install Date:     03/11/2020, 08:01:50
System Boot Time:          10/10/2021, 22:58:45
System Manufacturer:       innotek GmbH
System Model:              VirtualBox
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: Intel64 Family 6 Model 60 Stepping 3 GenuineIntel ~3093 Mhz
BIOS Version:              innotek GmbH VirtualBox, 01/12/2006
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              de;German (Germany)
Time Zone:                 (UTC+01:00) Amsterdam, Berlin, Bern, Rome, Stockholm, Vienna
Total Physical Memory:     8.192 MB
Available Physical Memory: 4.974 MB
Virtual Memory: Max Size:  9.472 MB
Virtual Memory: Available: 6.322 MB
Virtual Memory: In Use:    3.150 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\DESKTOP-325I5B9
Hotfix(s):                 8 Hotfix(s) Installed.
                           [01]: KB4586876
                           [02]: KB4537759
                           [03]: KB4557968
                           [04]: KB4562830
                           [05]: KB4577266
                           [06]: KB4580325
                           [07]: KB4598481
                           [08]: KB4598242
Network Card(s):           1 NIC(s) Installed.
                           [01]: Intel(R) PRO/1000 MT Desktop Adapter
                                 Connection Name: Ethernet
                                 DHCP Enabled:    Yes
                                 DHCP Server:     10.0.2.2
                                 IP address(es)
                                 [01]: 10.0.2.15
                                 [02]: fa80::aae:127a:4ae6:5a5a
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```

Details about the CPU can be found at the Processor value in the right pane, in this case a virtual CPU (vCPU).

### #3. Windows Powershell / Command Prompt
* Open a command prompt
* Type and execute the following command:

```
wmic cpu get caption,deviceid,name,numberofcores,maxclockspeed,status
```

* The command produces the following output:

```
Caption                               DeviceID  MaxClockSpeed  Name                                      NumberOfCores  Status
Intel64 Family 6 Model 60 Stepping 3  CPU0      3093           Intel(R) Core(TM) i3-4160T CPU @ 3.10GHz  2              OK
```

### #4. Windows Registry

* Open the Windows Registry Editor
* Browse to the Windows Registry Key named **Computer\HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\CentralProcessor**
* Click on one of the subkeys to get information about the CPU (here 0).
![Windows Registry](./img/biti-ipm-compute-windows-registry.png)


## Monitor the CPU on Windows

### #1. Windows Task Manager

The **Windows Task Manager (Taskmon)** is a system tool found in all versions of Microsoft Windows platform's. It provides information about running applications, processes, and services, as well as computer performance, network activity, and memory information. There are two views for the Task Manager: Simplified and Advanced.

To use Taskmon, open Start, do a search for **taskman**, and confirm the result.

![Windows Task Manager (advanced view)](./img/biti-ipm-compute-windows-taskman.png)


### #2. Windows Resource Monitor


The **Windows Resource Monitor (Resmon)** is a system application included in Windows Vista and later versions of Windows that allows users to look at the presence and allocation of resources on a computer.

The Windows Task Manager can best be described as a tool that runs **on** the surface. It lists processes and services, and general resource usage. In contrast, the Resource Monitor gives you the option to look **under** the surface to get more information that the Task Manager does not provide.

The Windows Resource Monitor can be used to determine extensive and detailed information about the current performance and resource consumption in real time. The program is therefore also suitable for error analysis. The view is divided into the following sections:

- Overview (CPU, disk, network, memory)
- CPU 
- Memory 
- Disk 
- Network 

To use Resmon, open Start, do a search for **resmon**, and confirm the result.

![htop](./img/biti-ipm-compute-windows-resmon.png)


### #3. Windows Performance Monitor (Perfmon)

On Windows 10, users can use **Windows Performance Monitor** to analyze data, such as processor, hard drive, memory, and network usage.

To use Permon, open Start, do a search for **perfmon**, and confirm the result.

![Windows Performance Monitor](./img/biti-ipm-compute-windows-perfmon-main.png)

When you open the tool, the main page will show up with a brief overview, a system summary with real-time data about Memory, Network Interface, Physical Disk, and Processor Information. On the keft side, you will find the navigation pane with access to Monitoring Tools, Data Collector Sets, and Reports.<br>
<br>
When you switch to the Performance Monitor, you will see a screen with a single counter. This is typically the **Processor Time** counter, which displays the processor load.
<br>

![Windows Performance Monitor](./img/biti-ipm-compute-windows-perfmon-performance.png)

However, you can add a lot of other counters to monitor virtually anything on your computer. To add new counters to monitor applications and hardware performance on your computer, do the following steps:

- Click on the green **Plus** button above the Performance Monitor graph.
- Select Local computer (or the name of your computer) from the drop-down menu.
- Select and expand the category of the item you want to monitor. 
- If applicable, select the instances you want to monitor. Click the **Add** button to add the new counters.
<br>

![Windows Performance Monitor](./img/biti-ipm-compute-windows-perfmon-addcounter.png)

- Finally, click on the **OK** button to confirm. Here you are !

![Windows Performance Monitor](./img/biti-ipm-compute-windows-perfmon-counteradded.png)


The Performance Monitor also includes so-called Data Collectors Sets, which is where you can use existing sets or create custom sets containing performance counters and alerts based on specific criteria.

In this lab, we will use the Data Collectors Set for Hyper-V. You can right-click your Data Collector Set under "User Defined," and click **Start** to run it.

![Windows Performance Monitor](./img/biti-ipm-compute-windows-perfmon-start.png)

Wait 30 seconds, while **Performance Monitor** is collecting data. Then right-click the Data Collector set for Hyper-V and click on **Stop** to shut it down.

![Windows Performance Monitor](./img/biti-ipm-compute-windows-perfmon-stop.png)

The tools generates Reports for each run. To view these reports, click on the lastest Hyper-V report in the Report section and browse through the statistics.

![Windows Performance Monitor](./img/biti-ipm-compute-windows-perfmon-view.png)

<aside class="positive">
Consult the online documents for more details about this tool. Do your own research about the output and how to interpret the results.
</aside>


## Hands-On: CPU Monitoring on Linux

### What you will learn:

In this codelab, you will learn 

* how to use the htop programm
* how to generate workload
* how to limit CPU time that the workload generator may consume

### What you will need:

In this codelab, you will need the following tools:

* Stress
* htop
* uptime
* cpulimit

Details of these two (2) tools can be found in the corresponding Codelab named `Stress the computer`. Create workloads with Yes and/or Stress to see how the CPU responds.

For example, run the following command to create a CPU workload for 2 cores for the next two (2) minutes:
```
stress -c 2 -t 120s
```

This gives you time to run commands like htop or uptime to observe CPU usage. Remember that you can also start multiple workload generators simultaneously or delayed. This allows you to try multiple scenarios.

### Scenario

In this codelab, the target is a Virtual Machine with 2 CPUs and 4 GB RAM. The operating system is based on Debian 11.0 (Bullseye) with Linux kernel version 5.10.0-8-amd64.

* Open a shell terminal and execute the following command:
```
htop
```

* Open another shell terminal and execute the following command:
```
watch uptime
```

* Open another shell terminal and start the workload generator with the following option.
```
stress -c 2
```
Notice that the workload generator runs without a time limit.

* Observe how the CPU utilization changes. 
* Sample output for **uptime**:
![Exercise](./img/biti-ipm-compute-exercise-uptime.png) 
* Sample output for **htop** 
![Exercise](./img/biti-ipm-compute-exercise-htop.png)

As you can see, the CPU Load rises above 3.0, indicating that the CPU is overloaded. At this point, we will try to limit the stress processes to 10% each.

* Get the identifiers of all stress processes. In this case the PID's are: 31210, 31211, and 31212

* Open a shell and execute the following command.
```
cpulimit -p 31210 --limit 10 &
```
Sample output
```
root@server' Process 31210 detected
```
* Repeat the command above for the PID 31211 and 31212.
* Observe how the CPU utilization changes in the two tools **htop** and **uptime**.
* Sample output for **htop**:
![Exercise](./img/biti-ipm-compute-exercise-htop-low.png)
* Sample output for **uptime**:
![Exercise](./img/biti-ipm-compute-exercise-uptime-low.png)
* As assumed, the CPU Load drops below 1.0, which means that the CPU is now able to handle all of the work scheduled in time.

To clean up, stop all running stress processes with the following command:
```
killall stress
```

Finally, you can close all open terminals. 

This is the end of the hands-on.