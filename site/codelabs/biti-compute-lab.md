summary: BITI IPM Lab 1 - How to monitor CPU Usage
id: biti-ipm-compute-lab
categories: linux
tags: ipm, compute, cpu, BITI, introduction
status: Published
authors: Roland Pellegrini

# BITI IPM Lab 1 - How to monitor CPU Usage
<!-- ------------------------ -->
## Before You Begin 

### What You’ll Learn
Every computer system benefits from proper administration and monitoring. A computer operates more quickly if few CPU-intensive processes are running at the same time.
Consequently, it makes sense that administrators check the CPU usage on a regular basis to identify programs that unnecessarly burden the system.

There are plenty of command line utilities created for this purpose. This codelab will introduce you to some of the most helpful applications, how to install and use them.

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

#### Host operating system (Host OS)

This is the OS of the physical computer on which Oracle VM VirtualBox is installed.

This can be Microsoft Windows, Linux, or Mac OS host. Check the Documentation on the [official web site](https://www.virtualbox.org/) for more details.

#### Guest operation system (Guest OS)

This is the OS of the virtual machine. This will be Debian 11 (Bullseye).

#### Administators privileges

By default, administrator privileges are required on the Host OS to install additional software. Make sure that you have the required permissions.

For the Guest OS, you will create and manage your own users. These users will therefore be different from the Host's user administration. 

## Get set up

For this codelab, it is recommanded to use VirtualBox and Debian 11 (Bullseye).

### VirtualBox.org

- Download VirtalBox (packaged for your Host-OS) from the [official web site](https://www.virtualbox.org/).
- Install it on your host. For Windows: make sure to disable all Hyper-V features first.
- Download the VirtualBox Extension Pack from the [official web site](https://www.virtualbox.org/).
- Install the VirtualBox Extension Pack via File --> Preferences --> Extensions.
![VirtualBox Extension](./img/biti-ipm-compute-vbox-extensions.png).
- Setup a Virtual Machine for Debian installation.
- Ensure that the virtual network is attached to a Bridge Adapter (Open Settings -> Network -> Adapter 1 -> Attached to Bridge Adapter).
![VirtualBox Extension](./img/biti-ipm-compute-vbox-settings-network.png)

### Debian 11 Bullseye
- Download Debian 11 Bullseye from from the [official web site](https://www.debian.org/).
- Ask your instructur for non-privileged user and password to ensure smooth administration.
- Start the virtual Machine and install Debian.
- Keep the installation simple. 
- Set up the given user and password.
- After installation has finished, reboot the VM, log in with user and password, open a shell and fire a ping to a server of your choice. If you get a response, then you have done a good job. Congratulations! 
- Good luck for the next steps!


### Root privileges for Debian

Installing additional software from Debian repositories requires root privileges. There are three ways how to get root access.

1. Log in as root:
```
Debian GNU/Linux 11 icinga tty1
icinga login: _
```
During the installation process, you were promted to set the root password. However, most modern Linux distributions disable the login for the root user due to security reason. If the login does not work, continue with the next step.

2. When logged in as non-privileged user, open the shell and run the following command:
```
su - 
```
Typically, you will be prompted for the root password. If unknown, ask your instructor for the password and run the above command again.

3. When you are logged in as non-privileged user and if you are a member of the **sudo** group, then open the shell and run the following command:
```
sudo -s
```
Normally, you will be asked for the user (not root) password. If you don't know it, ask your instructor for the password and run the above command again. If you have set up a different password, as recommended, and can no longer log into the system, then you will probably need to restart the installation.

## Software installation

In this codelab, you will need to install software packages.

#### Debian

1. Log in as root
2. Run the following command to update the local package lists.
```
apt update
```
3. Install the software with the following command:
```
apt install <package name>
```
If prompted, confirm the installation of software packages with Y(es)

#### Windows

Follow the instruction of the downloaded software.

## Display CPU Information

### nproc
The **nproc** command displays the number of available processing units. The command is a built-in command in a Linux system.

Open a shell and run **nproc** with the following option:
```
nproc --all
```

The output should look like this:
```
4
```

<aside class="positive">
Without the --all flag, nproc will display the number of processing units available to the current process.<br>
Use the --help flag for more information.
</aside>


### lscpu

On most Linux distributions, you can also use the **lscpu** command. It displays not only the number of processors but also the architecture, model name, speed, much more.

Open a shell and run the following command:
```
lscpu
```

The output will probably be very different from yours:
```
Architecture:        x86_64
CPU op-mode(s):      32-bit, 64-bit
Byte Order:          Little Endian
Address sizes:       39 bits physical, 48 bits virtual
CPU(s):              4
On-line CPU(s) list: 0-3
Thread(s) per core:  2
Core(s) per socket:  2
Socket(s):           1
NUMA node(s):        1
Vendor ID:           GenuineIntel
CPU family:          6
Model:               60
Model name:          Intel(R) Core(TM) i3-4160T CPU @ 3.10GHz
Stepping:            3
CPU MHz:             1696.113
CPU max MHz:         3100.0000
CPU min MHz:         800.0000
BogoMIPS:            6185.65
Virtualization:      VT-x
L1d cache:           32K
L1i cache:           32K
L2 cache:            256K
L3 cache:            3072K
NUMA node0 CPU(s):   0-3
Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm cpuid_fault epb invpcid_single pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid xsaveopt dtherm arat pln pts md_clear flush_l1d
```

### cpuinfo

All Linux distributions allows you to run **cat /proc/cpuinfo**. This file contains details about the processors installed. Note that /proc is a pseudo-filesystem, it is used as an interface to kernel data structures.

Open a shell and run the following command:
```
cat /proc/cpuinfo
```

Information is grouped per logical processor. That means that every processor is listed separately and various details about the CPU are included in the description. 

```
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 60
model name	: Intel(R) Core(TM) i3-4160T CPU @ 3.10GHz
stepping	: 3
microcode	: 0x28
cpu MHz		: 948.796
cache size	: 3072 KB
physical id	: 0
siblings	: 4
core id		: 0
cpu cores	: 2
apicid		: 0
initial apicid	: 0
fpu		: yes
fpu_exception	: yes
cpuid level	: 13
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm cpuid_fault epb invpcid_single pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid xsaveopt dtherm arat pln pts md_clear flush_l1d
bugs		: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs itlb_multihit srbds
bogomips	: 6185.65
clflush size	: 64
cache_alignment	: 64
address sizes	: 39 bits physical, 48 bits virtual
power management:

processor	: 1
vendor_id	: GenuineIntel
cpu family	: 6
model		: 60
model name	: Intel(R) Core(TM) i3-4160T CPU @ 3.10GHz
stepping	: 3
microcode	: 0x28
cpu MHz		: 1104.746
cache size	: 3072 KB
physical id	: 0
siblings	: 4
core id		: 1
cpu cores	: 2
apicid		: 2
initial apicid	: 2
fpu		: yes
fpu_exception	: yes
cpuid level	: 13
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm cpuid_fault epb invpcid_single pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid xsaveopt dtherm arat pln pts md_clear flush_l1d
bugs		: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs itlb_multihit srbds
bogomips	: 6185.65
clflush size	: 64
cache_alignment	: 64
address sizes	: 39 bits physical, 48 bits virtual
power management:

processor	: 2
vendor_id	: GenuineIntel
cpu family	: 6
model		: 60
model name	: Intel(R) Core(TM) i3-4160T CPU @ 3.10GHz
stepping	: 3
microcode	: 0x28
cpu MHz		: 1171.670
cache size	: 3072 KB
physical id	: 0
siblings	: 4
core id		: 0
cpu cores	: 2
apicid		: 1
initial apicid	: 1
fpu		: yes
fpu_exception	: yes
cpuid level	: 13
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm cpuid_fault epb invpcid_single pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid xsaveopt dtherm arat pln pts md_clear flush_l1d
bugs		: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs itlb_multihit srbds
bogomips	: 6185.65
clflush size	: 64
cache_alignment	: 64
address sizes	: 39 bits physical, 48 bits virtual
power management:

processor	: 3
vendor_id	: GenuineIntel
cpu family	: 6
model		: 60
model name	: Intel(R) Core(TM) i3-4160T CPU @ 3.10GHz
stepping	: 3
microcode	: 0x28
cpu MHz		: 1125.178
cache size	: 3072 KB
physical id	: 0
siblings	: 4
core id		: 1
cpu cores	: 2
apicid		: 3
initial apicid	: 3
fpu		: yes
fpu_exception	: yes
cpuid level	: 13
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm cpuid_fault epb invpcid_single pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid xsaveopt dtherm arat pln pts md_clear flush_l1d
bugs		: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs itlb_multihit srbds
bogomips	: 6185.65
clflush size	: 64
cache_alignment	: 64
address sizes	: 39 bits physical, 48 bits virtual
power management:
```

<aside class="positive">
Consult the documentation and man-pages for more details. Try to identify keywords and details of the CPU. 
</aside>

To count the number of processing units use grep command.
```
cat /proc/cpuinfo | grep processor
```

```
processor	: 0
processor	: 1
processor	: 2
processor	: 3
```

<aside class="positive">
The number of processors shown by /proc/cpuinfo might be different from the actual number of cores on the processor. For example a processor with 4 cores and hyperthreading would be reported as a processor with 8 cores.
</aside>

### Windows Registry

- Open the Windows Registry Editor
- Browse to the Windows Registry Key named **Computer\HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\CentralProcessor**
- Click on one of the subkeys to get information about the CPU (here 0).

![Windows Registry](./img/biti-ipm-compute-windows-registry.png)


## Monitor the CPU on Linux

### uptime 
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

<aside class="positive">
Consult your documentation about the CPU load average, its meaning and how to interpret the numbers.
</aside>


### top

One of the most common Linux tools for checking the CPU utilization is **top**. <br><br>
Like **uptime**, the command **top** displays the CPU load averages. In addition, the command provides real-time CPU usage information as well as other performance metrics. Furthermore, top stays in the foreground and refreshes at regular intervals.

Run the **top** command as follows:
```
top
```

The output will probably be very different from yours:
![top](./img/biti-ipm-compute-top.png)

The first five lines (also called the Header Block) provide a summary about the processes on the server. The Header Block is followed by a table  (the Process Table) with information about each individual process.

<aside class="positive">
Consult the man-pages for more details about top, about the Header Block and the Process Table. Run your own research about the output and how to interpret it.
</aside>

### htop 

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
![htop](./img/biti-ipm-compute-htop.png)

The top section is more easier to read and the bottom section is better organized.


<aside class="positive">
Consult the man-pages for more details about htop. Run your own research about the output and how to interpret it.
</aside>


### mpstat 

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

## Monitor the CPU on Windows

### Windows Task Manager

The **Windows Task Manager (Taskmon)** is a system tool found in all versions of Microsoft Windows platform's. It provides information about running applications, processes, and services, as well as computer performance, network activity, and memory information. There are two views for the Task Manager: Simplified and Advanced.

To use Taskmon, open Start, do a search for **taskman**, and confirm the result.

![Windows Task Manager (advanced view)](./img/biti-ipm-compute-windows-taskman.png)


### Windows Resource Monitor


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


### Windows Performance Monitor (Perfmon)

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
