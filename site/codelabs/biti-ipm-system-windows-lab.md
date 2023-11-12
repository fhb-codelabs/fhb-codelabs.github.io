summary: BITI IPM Lab - System Information
id: biti-ipm-system-windows-lab
categories: windows
tags: ipm, system, BITI, introduction
status: Draft
authors: Roland Pellegrini

# BITI IPM Lab - System Information
<!-- ------------------------ -->
## Before You Begin 

### What You’ll Learn

In this codelab you will learn

* how to get system information with systeminfo

###  Where You Can Look Up

### What You'll need

#### Guest operation system (Guest OS)

This is the OS of the virtual machine. This will be Microsoft Windows.

<aside class="negative">
Important note: All codelabs for Microsoft Windows are tested on Windows 10 but should also work on Windows 11. 
</aside>

#### Administators privileges

By default, administrator privileges are required on the Host OS to install additional software. Make sure that you have the required permissions.

For the Guest OS, you will create and manage your own users. These users will therefore be different from the Host's user administration. 

<!-- ------------------------ -->

## Systeminfo

### Description

The command-line tool **systeminfo** displays detailed configuration information about a computer and its operating system, including system configuration, security information, product details, and hardware properties.

### Sample code

* Launch Windows PowerShell as an administrator, enter the command:

```
systeminfo
```

* The Windows PowerShell window displays some information about the processor on this computer.
```
Host Name:                 DESKTOP-315IDDD
OS Name:                   Microsoft Windows 10 Education
OS Version:                10.0.19042 N/A Build 19042
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          Windows User
Registered Organization:
Product ID:                00328-104C1-1810A-AA697
Original Install Date:     03/11/2020, 08:01:50
System Boot Time:          28/10/2021, 17:15:39
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
Available Physical Memory: 4.297 MB
Virtual Memory: Max Size:  9.472 MB
Virtual Memory: Available: 5.744 MB
Virtual Memory: In Use:    3.728 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\DESKTOP-315IDDD
Hotfix(s):                 9 Hotfix(s) Installed.
                           [01]: KB5005539
                           [02]: KB4537759
                           [03]: KB4557968
                           [04]: KB4562830
                           [05]: KB4577266
                           [06]: KB4580325
                           [07]: KB4598481
                           [08]: KB5006670
                           [09]: KB5005699
Network Card(s):           1 NIC(s) Installed.
                           [01]: Intel(R) PRO/1000 MT Desktop Adapter
                                 Connection Name: Ethernet
                                 DHCP Enabled:    Yes
                                 DHCP Server:     10.0.2.2
                                 IP address(es)
                                 [01]: 10.0.2.15
                                 [02]: fe80::dae:427b:46e6:9f5b
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```
