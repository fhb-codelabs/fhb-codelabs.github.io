summary: BITI IPM Lab - Compute
id: biti-ipm-compute-windows-lab
categories: windows
tags: ipm, compute, cpu, BITI, introduction
status: Published
authors: Roland Pellegrini

# BITI IPM Lab - Compute
<!-- ------------------------ -->
## Before You Begin 

### What You’ll Learn

In this codelab you will learn

* how to get information about the CPU
* how to monitor CPU with Windows Tools

### What You'll need

#### Guest operation system (Guest OS)

This is the OS of the virtual machine. This will be Microsoft Windows.

#### Administators privileges

By default, administrator privileges are required on the Host OS to install additional software. Make sure that you have the required permissions.

For the Guest OS, you will create and manage your own users. These users will therefore be different from the Host's user administration. 

## Display CPU information on Microsoft Windows

### What You will learn:

You can use one of the following commands to find some information about the physical CPUs (pCPU) including all cores on Windows:

* msinfo32 application
* systeminfo command
* wmic command
* Windows Registry

## MSINFO32

### Description

The tool **msinfo32** is a built-in system profiler for Microsoft Windows which collects and displays system information about the the Operating systems, hard- and software.

### Sample code

To launch **msinfo32**, simple press the **Win+R** keys, type **msinfo32** and click the **OK**-button.

![Windows Registry](./img/biti-ipm-compute-windows-msinfo32-cpu.png)

Details about the CPU can be found in the System Summary section at the Processor value in the right pane.

## Systeminfo

### Description

The command-line tools **systeminfo** displays detailed configuration information about a computer and its operating system, including system configuration, security information, product details, and hardware properties.

### Sample code

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

## WMIC

### Description
WMI (Windows Management Instrumentation) is a programming interface that can be used to capture many aspects of Windows operating systems. This ranges from hardware, operating system settings, performance data to installed applications. WMI allows to read and changed values. It also allows the execution of methods and functions.

WMIC stands for WMI Command.

### Sample code

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

## Windows Registry

### Description
The **Windows registry** is a database of information, settings, options and other values for software and hardware. The registry is installed on all versions of Microsoft Windows operating systems. The Windows Registry helps the operating system to manage the computer, it helps programs to use the resources, and it provides a location for keeping custom settings you make in both Windows and your programs.

### Sample code

* Open the Windows Registry Editor
* Browse to the Windows Registry Key named **Computer\HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\CentralProcessor**
* Click on one of the subkeys to get information about the CPU (here 0).
![Windows Registry](./img/biti-ipm-compute-windows-registry.png)


## Monitor the CPU on Windows

### What You will learn:
You can use one of the following application to monitor the CPU on your Windows Host.

* Windows Task Manager (TaskMan)
* Windors Resource Monitor (ResMon)
* Windows Performance Monitor (PerfMon)

## Windows Task Manager

### Description

The **Windows Task Manager** is a utility program that reports the status of running programs. IT Administrators can use the Task Manager to monitor the performance of the computer, and to close a program that is not responding. Additionally,  Administrators can view network status, and see which users are connected to the computer.

### Sample code

The **Windows Task Manager (Taskmon)** is a system tool found in all versions of Microsoft Windows platform's. It provides information about running applications, processes, and services, as well as computer performance, network activity, and memory information. There are two views for the Task Manager: Simplified and Advanced.

To use Taskmon, open Start, do a search for **taskman**, and confirm the result.

![Windows Task Manager (advanced view)](./img/biti-ipm-compute-windows-taskman.png)


## Windows Resource Monitor

### Description

The **Windows Resource Monitor (Resmon)** is a system application included in Windows Vista and later versions of Windows that allows users to look at the presence and allocation of resources on a computer.

The Windows Task Manager can best be described as a tool that runs **on** the surface. It lists processes and services, and general resource usage. In contrast, the Resource Monitor gives you the option to look **under** the surface to get more information that the Task Manager does not provide.

The Windows Resource Monitor can be used to determine extensive and detailed information about the current performance and resource consumption in real time. The program is therefore also suitable for error analysis. The view is divided into the following sections:

- Overview (CPU, disk, network, memory)
- CPU 
- Memory 
- Disk 
- Network 

### Sample code

To use Resmon, open Start, do a search for **resmon**, and confirm the result.

![htop](./img/biti-ipm-compute-windows-resmon.png)


## Windows Performance Monitor (Perfmon)

### Description

On Windows 10, IT Administrators can use **Windows Performance Monitor** to analyze data, such as processor, hard drive, memory, and network usage.

### Sample code

To use Perfmon, open Start, do a search for **perfmon**, and confirm the result.

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
