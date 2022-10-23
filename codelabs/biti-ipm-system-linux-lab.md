summary: BITI IPM Lab - System Information
id: biti-ipm-system-linux-lab
categories: linux
tags: ipm, system, BITI, introduction
status: Draft
authors: Roland Pellegrini

# BITI IPM Lab - System Information
<!-- ------------------------ -->
## Before You Begin 

### What You’ll Learn

In this codelab you will learn

* how to get system information with hwinfo

###  Where You Can Look Up

### What You'll need

#### Guest operation system (Guest OS)

This is the OS of the virtual machine. This will be Debian 11 (Bullseye).

#### Administators privileges

By default, administrator privileges are required on the Host OS to install additional software. Make sure that you have the required permissions.

For the Guest OS, you will create and manage your own users. These users will therefore be different from the Host's user administration. 

<!-- ------------------------ -->

## hwinfo

### Description

Hwinfo checks details of the hardware present in a Linux system and displays the extensive details of each hardware device. It reports information about most hardware units including CPU, HDD controllers, network card, graphics cards, and more.

<aside class="positive">
Hwinfo requires root privileges.
</aside>

### Sample code

Run the following command:
```
hwinfo | more
```

### Sample output

The output may look like this:

```
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

### Documentation

Need more info? Use this:
```
man hwinfo
```
