summary: BITI IPM Lab - Disk
id: biti-ipm-disk-lab
categories: linux
tags: ipm, disk, BITI, introduction
status: Published
authors: Roland Pellegrini

# BITI IPM Lab - Disk
<!-- ------------------------ -->
## Before You Begin 

### What You’ll Learn

A hard disk is the storage medium in your computer. A hard disk drive (HDD) is a traditional storage device that uses mechanical platters and a moving read/write head to access data. A solid state drive (SSD) is a newer, faster type of device that stores data on instantly-accessible memory chips.

In this codelab you will learn how to identify HDDs and SDDS in your computer and how to measure their performance. 


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

This is the OS of the virtual machine. This will be Debian 11 (Bullseye) or Microsoft Windows.

#### Administators privileges

By default, administrator privileges are required on the Host OS to install additional software. Make sure that you have the required permissions.

For the Guest OS, you will create and manage your own users. These users will therefore be different from the Host's user administration. 

## Display Disk information on Linux

### What You will learn:

You can use one of the following commands to find detailed information about the physical disks on Linux:

* lshw 
* inxi
* smartctl 

### #1. The lshw command 

The **lshw** (Hardware Lister) is a simple, yet full-featured tool that provides detailed information on the hardware configuration. It can report memory configuration, mainboard configuration, CPU version and speed, Hard disk drive details, cache configuration, bus speed and a lot more. **lshw** requires root privileges. 

Open a shell and run the following command:
```
sudo lshw -class disk
```

Sample output:
```
root@server:~# lshw -class disk
  *-cdrom                   
       description: DVD reader
       product: CD-ROM
       vendor: VBOX
       physical id: 0.0.0
       bus info: scsi@2:0.0.0
       logical name: /dev/cdrom
       logical name: /dev/dvd
       logical name: /dev/sr0
       version: 1.0
       capabilities: removable audio dvd
       configuration: ansiversion=5 status=ready
     *-medium
          physical id: 0
          logical name: /dev/cdrom
  *-disk
       description: ATA Disk
       product: VBOX HARDDISK
       vendor: VirtualBox
       physical id: 0.0.0
       bus info: scsi@1:0.0.0
       logical name: /dev/sda
       version: 1.0
       serial: VB7b3622bd-ddc6a90c
       size: 24GiB (25GB)
       capabilities: partitioned partitioned:dos
       configuration: ansiversion=5 logicalsectorsize=512 sectorsize=512 signature=826474aa
```

<aside class="positive">
The lshw tool provides also information about Memory, CPU, and more in a very user-friendly format. Consult the documentation and man-pages for more details. Try to identify keywords and details of the disk and other components. 
</aside>

To  display only the name of the disks, run:
Sample output:
```
root@server:~# lshw -short -class disk
```

The `-short` option is useful when you need the name of the drives only (here: /dev/cdrom or /dev/sda).

Sample output:
```
root@server:~# lshw -short -class disk
H/W path            Device      Class       Description
=======================================================
/0/100/1.1/0.0.0    /dev/cdrom  disk        CD-ROM
/0/100/1.1/0.0.0/0  /dev/cdrom  disk        
/0/100/d/0.0.0      /dev/sda    disk        25GB VBOX HARDDISK
```

### #2. The inxi command

Inxi is yet another full-featured command line system information tool. It shows system hardware, CPU, drivers, Xorg, Desktop, Kernel, GCC version(s), Processes, RAM usage, and a wide variety of other useful information.

To get the details of the installed hard disk drives in your Linux system, run the following command:
```
inxi -D
```

Information is grouped per memory device. That means that every memory device is listed separately and various details about the memory are included in the description. 

Sample output:
```
root@server:~# inxi -D
Drives:    Local Storage: total: 24 GiB used: 13.57 GiB (56.5%) 
           ID-1: /dev/sda vendor: VirtualBox model: VBOX HARDDISK size: 24 GiB 
```

<aside class="positive">
Consult the documentation and man-pages for more details. Try to identify keywords and details of the disk. 
</aside>

You can also display more disk details like disk controller speed, serial no and temperature using the following command:
```
inxi -Dxx
```

Sample output:
```
root@server:~# inxi -Dxx
Drives:    Local Storage: total: 24 GiB used: 13.57 GiB (56.5%) 
           ID-1: /dev/sda vendor: VirtualBox model: VBOX HARDDISK size: 24 GiB speed: 3.0 Gb/s serial: VB7b3622bd-ddc6a90c 
```

<aside class="positive">
Be aware that all commands are executed within a virtual machine. Running the tool on physical hardware provides more information. 
</aside>

### #3. The smartctl command

**Smartclt** is a command line, control and monitor utility for SMART disks. It controls the **S**elf-**M**onitoring, **A**nalysis and **R**eporting **T**echnology (SMART) system built into most ATA/SATA and SCSI/SAS hard drives and solid-state drives. 

**Smartclt** command is part of the **smartmontools** package, which comes pre-installed in most Linux versions. 
**Smartclt** requires root privileges.


To get all details about the first hard disk drive (dev/sda) in your Linux box, run **Smartclt** the following options:
```
sudo smartctl -d ata -a -i /dev/sda
```

Sample output:
```
smartctl 6.6 2017-11-05 r4594 [x86_64-linux-4.19.0-17-amd64] (local build)
Copyright (C) 2002-17, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Device Model:     JAJS600M512C
Serial Number:    30013687674
Firmware Version: S0222A0
User Capacity:    512,110,190,592 bytes [512 GB]
Sector Size:      512 bytes logical/physical
Rotation Rate:    Solid State Device
Form Factor:      2.5 inches
Device is:        Not in smartctl database [for details use: -P showall]
ATA Version is:   ACS-2 T13/2015-D revision 3
SATA Version is:  SATA 3.2, 6.0 Gb/s (current: 6.0 Gb/s)
Local Time is:    Tue Oct 12 21:42:16 2021 CEST
SMART support is: Available - device has SMART capability.
SMART support is: Enabled

=== START OF READ SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED

General SMART Values:
Offline data collection status:  (0x00)	Offline data collection activity
					was never started.
					Auto Offline Data Collection: Disabled.
Self-test execution status:      (   0)	The previous self-test routine completed
					without error or no self-test has ever 
					been run.
Total time to complete Offline 
data collection: 		(  120) seconds.
Offline data collection
capabilities: 			 (0x11) SMART execute Offline immediate.
					No Auto Offline data collection support.
					Suspend Offline collection upon new
					command.
					No Offline surface scan supported.
					Self-test supported.
					No Conveyance Self-test supported.
					No Selective Self-test supported.
SMART capabilities:            (0x0002)	Does not save SMART data before
					entering power-saving mode.
					Supports SMART auto save timer.
Error logging capability:        (0x01)	Error logging supported.
					General Purpose Logging supported.
Short self-test routine 
recommended polling time: 	 (   2) minutes.
Extended self-test routine
recommended polling time: 	 (  10) minutes.

SMART Attributes Data Structure revision number: 1
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  1 Raw_Read_Error_Rate     0x0032   100   100   050    Old_age   Always       -       0
  5 Reallocated_Sector_Ct   0x0032   100   100   050    Old_age   Always       -       0
  9 Power_On_Hours          0x0032   100   100   050    Old_age   Always       -       7576
 12 Power_Cycle_Count       0x0032   100   100   050    Old_age   Always       -       385
160 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       0
161 Unknown_Attribute       0x0033   100   100   050    Pre-fail  Always       -       100
163 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       9
164 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       42090
165 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       128
166 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       10
167 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       85
168 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       7000
169 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       99
175 Program_Fail_Count_Chip 0x0032   100   100   050    Old_age   Always       -       0
176 Erase_Fail_Count_Chip   0x0032   100   100   050    Old_age   Always       -       0
177 Wear_Leveling_Count     0x0032   100   100   050    Old_age   Always       -       0
178 Used_Rsvd_Blk_Cnt_Chip  0x0032   100   100   050    Old_age   Always       -       0
181 Program_Fail_Cnt_Total  0x0032   100   100   050    Old_age   Always       -       0
182 Erase_Fail_Count_Total  0x0032   100   100   050    Old_age   Always       -       0
192 Power-Off_Retract_Count 0x0032   100   100   050    Old_age   Always       -       20
194 Temperature_Celsius     0x0022   100   100   050    Old_age   Always       -       40
195 Hardware_ECC_Recovered  0x0032   100   100   050    Old_age   Always       -       824
196 Reallocated_Event_Count 0x0032   100   100   050    Old_age   Always       -       0
197 Current_Pending_Sector  0x0032   100   100   050    Old_age   Always       -       0
198 Offline_Uncorrectable   0x0032   100   100   050    Old_age   Always       -       0
199 UDMA_CRC_Error_Count    0x0032   100   100   050    Old_age   Always       -       0
232 Available_Reservd_Space 0x0032   100   100   050    Old_age   Always       -       100
241 Total_LBAs_Written      0x0030   100   100   050    Old_age   Offline      -       232859
242 Total_LBAs_Read         0x0030   100   100   050    Old_age   Offline      -       490349
245 Unknown_Attribute       0x0032   100   100   050    Old_age   Always       -       686052

SMART Error Log Version: 1
No Errors Logged

SMART Self-test log structure revision number 1
No self-tests have been logged.  [To run self-tests, use: smartctl -t]

Selective Self-tests/Logging not supported
```

Here is a short list of SMART attributes:
* Read Error Rate - Non-correctable errors when reading from the hard disk, leads to re-reading.
* Throughput Performance - data throughput or efficiency of the hard disk drive
* Power On Hours - Uptime in hours or seconds (including standby)
* Temperature - Temperature of the drive in °C
* Power Cycle Count - Indicates how many times the drive has been turned on and off
* Hardware ECC Recovered - corrected bit errors during reading
* Total LBAs Written - The total number of sectors written by the host system


## Disk monitoring tools on Linux

### What You will learn:

You can use one of the following command to monitor disks on Linux:

* iotop
* dstat

### #1. The iotop command

**iotop** is a top-like utility for displaying real-time disk activity. By using **iotop** command, you can monitor the disk utilization by individual processes.

Here is a quick example:
```
iotop -o
```
The `-o` or  `--only` option presents only processes or threads actually doing I/O.

Sample output:
![Exercise](./img/biti-ipm-disk-linux-iotop.png) 

The **iotop** command displays columns for the I/O bandwidth read and written by each process/thread during the sampling period. It also displays the percentage of time the thread/process spent while swapping in and while waiting on I/O. For each process, its I/O priority (class/level) is shown. In addition, the total I/O bandwidth read and written during the sampling period is displayed at the top of the interface.

<aside class="positive">
Consult the documentation and man-pages for more details. 
</aside>

### #2. The dstat command

The **dstat** tool is used to retrieve information or statistics form components of the system such as network connections, IO devices, or CPU. By using this tool a system administrator can even see the throughput for block devices that make up a single filesystem or storage system. 

Open a shell and run **dtat** without any option to see major OS components:
```
dstat
```

Sample output:
```
root@server:~# dstat
You did not select any stats, using -cdngy by default.
--total-cpu-usage-- -dsk/total- -net/total- ---paging-- ---system--
usr sys idl wai stl| read  writ| recv  send|  in   out | int   csw 
  1   0  99   0   0|  54k  265k|   0     0 |   0     0 | 189    80 
  4   0  96   0   0|   0     0 |   0     0 |   0     0 | 254   228 
  5   1  94   0   0|   0   124k|   0     0 |   0     0 | 523   589 
  4   1  95   0   0|   0     0 |   0     0 |   0     0 | 317   252 
  3   1  96   0   0|   0     0 |   0     0 |   0     0 | 331   334 
  4   1  96   0   0|   0     0 |   0     0 |   0     0 | 303   282 
  5   1  95   0   0|   0     0 |   0     0 |   0     0 | 284   269 
  4   1  95   0   0|   0     0 |   0     0 |   0     0 | 267   238 
  4   0  96   0   0|   0     0 |   0     0 |   0     0 | 283   246 
  3   1  97   0   0|   0     0 |   0     0 |   0     0 | 247   181 
  5   0  95   0   0|   0     0 |   0     0 |   0     0 | 515   646 
  4   0  96   0   0|   0     0 |   0     0 |   0     0 | 312   242 
  4   1  96   0   0|   0   280k|   0     0 |   0     0 | 355   276 
  4   1  96   0   0|   0     0 |   0     0 |   0     0 | 316   241 
```

This command will display CPU, Disk, Network, Paging and System stats. 
The output above indicates:

* CPU stats: cpu usage by a user (usr) processes, system (sys) processes, as well as the number of idle (idl) and waiting (wai) processes, hard interrupt (hiq) and soft interrupt (siq).
* Disk stats: total number of read (read) and write (writ) operations on disks.
* Network stats: total amount of bytes received (recv) and sent (send) on network interfaces.
* Paging stats: number of times information is copied into (in) and moved out (out) of memory.
* System stats: number of interrupts (int) and context switches (csw). A CSW is a process that involves switching of the CPU from one process or task to another.

<aside class="positive">
Consult the documentation and man pages for more details. Try to identify keywords and details not only of the memory, but also the details about processes, I/O and more. 
</aside>


Additionally, you can also store the output of dstat in a CSV file for analysis at a latter time by enabling the `--output` option.

By running **dstat** with the following options, we are displaying the time, cpu, mem, disk, and system load statistics with a one second delay between 5 updates (counts).

```
dstat --time --cpu --mem disk --load --output report.csv 1 5
```

Sample output:
```
----system---- --total-cpu-usage-- ------memory-usage----- -dsk/total- ---load-avg---
     time     |usr sys idl wai stl| used  free  buff  cach| read  writ| 1m   5m  15m 
12-10 23:17:49|  1   1  99   0   0| 704M 2957M 5680k  254M| 115k  413k|1.91 0.57 0.20
12-10 23:17:50| 19   5  68   9   0| 718M 2931M 6012k  266M|  12M   56k|1.91 0.57 0.20
12-10 23:17:51|  6   2  90   2   0| 718M 2928M 6012k  269M|2448k    0 |1.91 0.57 0.20
12-10 23:17:52|  3   1  96   0   0| 718M 2928M 6012k  269M|   0     0 |1.91 0.57 0.20
12-10 23:17:53|  7  42  50   0   0| 721M 3030M 6016k  169M| 176k    0 |1.91 0.57 0.20
12-10 23:17:54| 10  52  39   0   0| 721M 2964M 6016k  233M|   0   300k|1.84 0.58 0.21
12-10 23:17:55|  5  47  34  14   0| 719M 2896M 6104k  300M|1140k  308k|1.84 0.58 0.21
12-10 23:17:56|  3  48  49   0   0| 720M 2825M 6104k  370M|   0     0 |1.84 0.58 0.21
12-10 23:17:57|  3  44  38  15   0| 720M 2754M 6104k  438M|   0  4820k|1.84 0.58 0.21
12-10 23:17:58|  7  47   0  46   0| 720M 2687M 6104k  504M|   0  7428k|1.84 0.58 0.21
12-10 23:17:59|  4  48   0  49   0| 720M 2615M 6104k  574M|   0  9320k|1.85 0.60 0.22
12-10 23:18:00| 12  43   1  44   0| 719M 2560M 6120k  628M|  92k  113M|1.85 0.60 0.22
```

## Hands-on: Disk Monitoring on Linux

### What you will learn:

In this codelab, you will learn 

* how to use the dstat tool
* how to generate workload
* how to create a report file

### What you will need:

In this codelab, you will need the following tools:

* dstat
* Stress

Details of the **Stress** tool can be found in the corresponding Codelab named `Stress the computer`.

### Scenario

In this codelab, the GuestOS is a Virtual Machine with 2 CPU Cores and 4 GB RAM. The GuestOS is based on Debian 11.0 (Bullseye) with Linux kernel version 5.10.0-8-amd64. The VM is installed and running on the Linux-based Hypervisor VirtualBox, Version 6.1.16 r140961 (QT 5.11.3). THe HostOS is based on Debian 10 (Buster) with Linux Kernel version 4.19.0-17-amd64. The Host hardware is HP Prodesk 400 G1 DN with a Intel Core i3-4160T CPU@3.10GHz, 16GB RAM, and an Intenso SATA III Top 512GB.

### Test Run

* Open a shell terminal and execute the following command:
```
dstat --time --cpu --mem disk --load --output report.csv 1 5
```

* Open the second shell terminal and start the workload generator with the following option.
```
stress --hdd 2 --io 4 --vm 6 --cpu 8 --timeout 30s
```

Where, 
* --hdd 2 -  This will start a 2-thread test, which will write data to the storage and delete it.
* --io 4 -  This will start a 4-thread test, which will stress the system' storage read and write buffers
* --vm 6 -  This will start a 6-thread test, running malloc() and free() functions
* --cpu 4 - This will start a 8-thread test, running sqrt()) functions

Notice that the workload generator runs with a time limit of 30 seconds. Afterwards, open the report.csv file and analyse the results within your team.

Sample output:
```
----system---- --total-cpu-usage-- ------memory-usage----- -dsk/total- ---load-avg---
     time     |usr sys idl wai stl| used  free  buff  cach| read  writ| 1m   5m  15m 
12-10 23:23:28|  1   1  98   0   0|1709M  126M 7208k 2031M| 117k  579k|1.37 0.55 0.28
12-10 23:23:29| 24  76   0   0   0|1449M  497M 7172k 1922M|  16k  323M|1.37 0.55 0.28
12-10 23:23:30| 23  77   0   0   0|1512M 1527M 7188k  860M|4096B  183M|2.54 0.80 0.36
12-10 23:23:31| 11  40  24  24   0| 720M 2227M 7188k  949M| 444k   11M|2.54 0.80 0.36
12-10 23:23:32|  2   1  49  49   0| 720M 2227M 7188k  949M| 248k 7340k|2.54 0.80 0.36
12-10 23:23:33|  1   0  49  50   0| 720M 2227M 7188k  949M|  72k 8156k|2.54 0.80 0.36
12-10 23:23:34|  3   1  49  48   0| 720M 2227M 7188k  949M|4096B 6520k|2.54 0.80 0.36
12-10 23:23:35|  4   1  47  49   0| 720M 2227M 7188k  949M|   0  9812k|2.82 0.89 0.39
12-10 23:23:36|  3   1  48  49   0| 720M 2227M 7188k  949M|   0    11M|2.82 0.89 0.39
12-10 23:23:37|  4   1   4  91   0| 720M 2227M 7200k  949M|   0  7336k|2.82 0.89 0.39
12-10 23:23:38|  5   2  43  51   0| 718M 2555M 7212k  631M|8192B 4944k|2.82 0.89 0.39
12-10 23:23:39| 10   2  84   4   0| 718M 2550M 7356k  636M|5692k    0 |2.82 0.89 0.39
12-10 23:23:40|  5   1  95   0   0| 718M 2550M 7356k  636M|   0     0 |2.75 0.91 0.40
12-10 23:23:41|  8   2  90   0   0| 718M 2550M 7356k  636M|   0     0 |2.75 0.91 0.40
12-10 23:23:42|  3   0  97   0   0| 718M 2550M 7356k  636M|   0     0 |2.75 0.91 0.40
12-10 23:23:43| 21  65  14   0   0|1774M 1166M 7364k  956M|8192B   13M|2.75 0.91 0.40
12-10 23:23:44| 23  77   0   0   0|1230M 1542M 6976k 1120M|   0    18M|2.75 0.91 0.40
12-10 23:23:45| 24  76   0   0   0|1599M 1133M 6976k 1159M|   0    15M|3.98 1.19 0.50
12-10 23:23:46| 24  76   0   0   0|1495M 1225M 6976k 1171M|   0    18M|3.98 1.19 0.50
12-10 23:23:47| 23  77   0   0   0|1425M 1287M 6976k 1178M|   0    19M|3.98 1.19 0.50
12-10 23:23:48| 24  76   0   0   0|1494M 1208M 6988k 1188M|   0    21M|3.98 1.19 0.50
12-10 23:23:49| 26  74   0   0   0|1544M 1147M 6988k 1199M|   0    18M|3.98 1.19 0.50
12-10 23:23:50| 23  77   0   0   0|1470M 1209M 6988k 1211M|   0    12M|5.02 1.46 0.59
12-10 23:23:51| 25  75   0   0   0|1538M 1094M 6988k 1256M|   0   177M|5.02 1.46 0.59
12-10 23:23:52| 22  78   0   0   0|1647M  746M 7004k 1489M|8192B  298M|5.02 1.46 0.59
12-10 23:23:53|  8  20  71   0   0| 719M 2560M 7020k  626M|   0    73M|5.02 1.46 0.59
12-10 23:23:54|  5   1  95   0   0| 719M 2560M 7020k  626M|   0     0 |5.02 1.46 0.59
12-10 23:23:55|  4   0  96   0   0| 719M 2560M 7020k  626M|   0     0 |4.70 1.45 0.59
12-10 23:23:56|  4   0  96   0   0| 719M 2560M 7020k  626M|   0     0 |4.70 1.45 0.59
12-10 23:23:57|  5   0  95   0   0| 719M 2560M 7020k  626M|   0     0 |4.70 1.45 0.59
12-10 23:23:58|  4   0  87   9   0| 719M 2560M 7032k  626M|   0    64k|4.70 1.45 0.59
12-10 23:23:59|  4   0  96   0   0| 719M 2560M 7032k  626M|   0     0 |4.70 1.45 0.59
12-10 23:24:00|  4   1  95   0   0| 719M 2560M 7032k  626M|   0     0 |4.32 1.42 0.59
```

This is the end of the hands-on.



## Display CPU information on Microsoft Windows

### What You will learn:

You can use one of the following commands to find some information about the physical CPUs (pCPU) including all cores on Windows:

* msinfo32 application

### #1. The msinfo32 application
The tool **msinfo32** is a built-in system profiler for Microsoft Windows which collects and displays system information about the the Operating systems, hard- and software.

To launch **msinfo32**, simple press the **Win+R** keys, type **msinfo32** and click the **OK**-button.

![Windows Registry](./img/biti-ipm-compute-windows-msinfo32-disk.png)

Details about the Disks can be found in the **Components -> Storage** Section at the **Disks** value in the right pane.



## Hands-on: Disk Monitoring on Windows

Application performance often depends on how well and quickly storage systems can be provisioned to end users. Storage performance is usually an important factor in the deployment of an application. For this reason, the appropriate storage system must be evaluated, designed and selected in terms of IOPS as well as throughput, speed and bandwidth.

The following section describes the Microsoft tool Diskspd.exe for evaluating the performance of disk and storage systems.
### What you will learn:

In this codelab, you will learn 

* how to generate workload with the diskspd.exe tool

### What you will need:

In this codelab, you will need the following tools:

* diskspd.exe


### The diskspd.exe command

The free Microsoft tool called [Diskspd.exe](https://github.com/microsoft/diskspd/releases) measures the performance of a storage system for Windows, Windows Server and Cloud Server infrastructure at Microsoft Azure and simulates various application loads. Values are given in IOPS, or Input/Output Operations per Second, and provide a benchmark for performance. Please visit the following [page](https://github.com/Microsoft/diskspd/wiki) for updated documentation.

For applications such as Microsoft Exchange or SQL Server, it may be necessary to determine the performance of the storage system in advance, The results help Administrators to specify the required Read/Write performance including latency.

The program can be downloaded [here](https://github.com/microsoft/diskspd/releases)) and copied to any directory on the target server.  The zipfile contains several versions, including a 64-bit variant in the amd64 folder. If you need a graphical user interface instead of the console window, you can get the Diskspd GUI from the [DiskSpeed](https://gallery.technet.microsoft.com/DiskSpeed-90d37d55) project.

To use the tool on the console, open a command prompt with **administrator rights** and then navigate to the directory where you copied and unpacked the zipfile. Finally go to the subdirectory corresponding to your target architecture (e.g. amd64).

### Scenario

In this codelab, the target is a virtual Machine with 2 CPU Cores and 8 GB RAM. The operating system is based on Windows 10 Educatuinm Version 10,0,19042 Build 19042. The VM is installed and running on the Linux-based Hypervisor VirtualBox, Version 6,1.16 r140961 (QT 5.11.3). THe HostOS is based on Debian 10 with Linux Kernel version 4.19.0-17-amd64. The Host hardware is HP Prodesk 400 G1 DN with a Intel Core i3-4160T CPU@3.10GHz, 16GB RAM, and an Intenso SATA III Top 512GB.

### Test Run

This Run is based on the Online Transaction Processing (OLTP) workload, which involves high levels of write activity. 
#### SQL Server OLTP

Open a Powershell terminal as Administrator and execute disksdo.exe with the following options:

```
diskspd.exe -b8K -d180 -h -L -o32 -t3 -r -w75 -c5G sqldata.dat > result.txt
```

Where, 
* -b - Block size of the input/output, specified as (K/M/G). For example, -b8K means a block size of 8 KB, which is relevant for SQL Server.
* -d - Test duration in seconds. Tests of 30-60 seconds are usually long enough to get valid results. Here, test duration is defined with 180 seconds.
* -h - Disables operating system-level software caching and hardware write caching. Here it is disabled becauseSQL Server works this way.
* -L - Captures latency information during the test
* -o - Pending I/Os (i.e. queue depth) per target, per worker thread.
* -t - worker threads per test file target. Here it is defined with 3 workers.
* -r - If the -r option is used, random tests are performed, otherwise sequential tests are performed. Here, SQL Server accesses data files randomly.
* -w - Specifies the percentage. For example, -w75 means that 75% write and 25% read operations are performed. Here, value is set to 75 because OLTP workloads involves more writung activities than reading.
* sqldata.at - the sample sql file name that will be used in the test. 
* -c -Creates workload file(s) of the specified size, specified as (K/M/G).
* - result.txt - the output file of the test results

After the execution of the test, the results are written to the file named **result.txt**. The results of the test are divided into 5 sections. The first section of the test result shows us the input parameters and all other settings.

```
Command Line: C:\Users\icinga\Downloads\DiskSpd\amd64\diskspd.exe -b8K -d180 -h -L -o32 -t3 -r -w75 -c5G sqldata.dat

Input parameters:

	timespan:   1
	-------------
	duration: 180s
	warm up time: 5s
	cool down time: 0s
	measuring latency
	random seed: 0
	path: 'sqldata.dat'
		think time: 0ms
		burst size: 0
		software cache disabled
		hardware write cache disabled, writethrough on
		performing mix test (read/write ratio: 25/75)
		block size: 8KiB
		using random I/O (alignment: 8KiB)
		number of outstanding I/O operations per thread: 32
		threads per file: 3
		using I/O Completion Ports
		IO priority: normal

System information:

	computer name: DESKTOP-12345678
	start time: 2021/10/13 17:19:51 UTC
```

In the second section we can read the details of the average CPU load.
```
Results for timespan 1:
*******************************************************************************

actual test time:	180.01s
thread count:		3
proc count:		2

CPU |  Usage |  User  |  Kernel |  Idle
-------------------------------------------
   0|   8.53%|   0.72%|    7.81%|  91.47%
   1|   8.35%|   0.68%|    7.67%|  91.65%
-------------------------------------------
avg.|   8.44%|   0.70%|    7.74%|  91.56%
```

The third section **Total IO** gives us all the performance details about the Storage performance. The total number of IOPS generated for this test is **274106**. The test duration is 180 seconds, therefore the IOPS per second is rounded up to **1523** (=result of 274106/180, note the variance to 1522.76). The AvgLat column indicates the average latency, and the result is **63.514**. The throughput per second is **11.90** MB/s.
```
Total IO
thread |       bytes     |     I/Os     |    MiB/s   |  I/O per s |  AvgLat  | LatStdDev |  file
-----------------------------------------------------------------------------------------------------
     0 |       653434880 |        79765 |       3.46 |     443.12 |   72.773 |   787.590 | sqldata.dat (5GiB)
     1 |       922140672 |       112566 |       4.89 |     625.35 |   51.558 |   658.417 | sqldata.dat (5GiB)
     2 |       669900800 |        81775 |       3.55 |     454.29 |   70.941 |   777.118 | sqldata.dat (5GiB)
-----------------------------------------------------------------------------------------------------
total:        2245476352 |       274106 |      11.90 |    1522.76 |   63.514 |   734.032
```


The fourth section is split in **Read IO** and **Write IO** operations. 
The **Read IO** section contains details about the read operations. 
The **Write IO** section contains details about the write operations.

```
Read IO
thread |       bytes     |     I/Os     |    MiB/s   |  I/O per s |  AvgLat  | LatStdDev |  file
-----------------------------------------------------------------------------------------------------
     0 |       163168256 |        19918 |       0.86 |     110.65 |   90.790 |   787.774 | sqldata.dat (5GiB)
     1 |       231997440 |        28320 |       1.23 |     157.33 |   65.074 |   681.015 | sqldata.dat (5GiB)
     2 |       166477824 |        20322 |       0.88 |     112.90 |   82.196 |   717.981 | sqldata.dat (5GiB)
-----------------------------------------------------------------------------------------------------
total:         561643520 |        68560 |       2.98 |     380.88 |   77.620 |   724.422

Write IO
thread |       bytes     |     I/Os     |    MiB/s   |  I/O per s |  AvgLat  | LatStdDev |  file
-----------------------------------------------------------------------------------------------------
     0 |       490266624 |        59847 |       2.60 |     332.47 |   66.776 |   787.437 | sqldata.dat (5GiB)
     1 |       690143232 |        84246 |       3.66 |     468.02 |   47.015 |   650.582 | sqldata.dat (5GiB)
     2 |       503422976 |        61453 |       2.67 |     341.39 |   67.219 |   795.673 | sqldata.dat (5GiB)
-----------------------------------------------------------------------------------------------------
total:        1683832832 |       205546 |       8.92 |    1141.88 |   58.809 |   737.150
```

The last section of the test results shows us the latency percentile analysis of the storage performance from the minimum to the maximum value. This result helps us to find out how the storage or the disks behave under different work loads.

```
total:
  %-ile |  Read (ms) | Write (ms) | Total (ms)
----------------------------------------------
    min |      0.244 |      0.149 |      0.149
   25th |      3.845 |      2.094 |      2.335
   50th |      6.232 |      3.223 |      3.896
   75th |     12.455 |      7.059 |      8.611
   90th |    145.098 |     48.302 |     63.730
   95th |    282.831 |    109.626 |    148.808
   99th |    557.531 |    451.447 |    501.254
3-nines |  13625.524 |  13619.105 |  13619.118
4-nines |  23442.732 |  23443.295 |  23443.262
5-nines |  23444.205 |  23819.563 |  23819.563
6-nines |  23444.205 |  23829.449 |  23829.449
7-nines |  23444.205 |  23829.449 |  23829.449
8-nines |  23444.205 |  23829.449 |  23829.449
9-nines |  23444.205 |  23829.449 |  23829.449
    max |  23444.205 |  23829.449 |  23829.449
```

This is the end of the hands-on.

