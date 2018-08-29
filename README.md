This is my working install guide for macOS sierra 10.12 on HP elitebook 840 G1, i5(4300U), Intel HD 4400 (tested on both azerty/querty & 1600x900 / 1366x768 models)

* [Prepare USB installer](#prepare-usb-installer)
* [BIOS settings](#bios-settings)
* [Installation](#installation)
* [Post installation](#post-installation)

# Prepare USB installer
1. Download macOS Sierra from [hackintosh.zone](https://www.hackintosh.zone/file/1008-hackintosh-sierra/)
1. Download [transmac trial](https://www.hackintosh.zone/file/1020-transmac-for-sierra/)
1. Plug your USB drive into your computer, and open TransMac as Administrator
1. Right click on your USB (sidebar at the right) and format disk for mac.
1. After finishing right click again on your USB and click Restore with disk image
1. Choose the file Hackintosh-Sierra-Zone.dmg you downloaded and proceed.
1. Wait until finish then reboot to your bios.

# BIOS settings
1. Reset bios settings to default
1. Go to Advanced
    * Device configutation
        * Check Fn key switch
        * Change Video memory to 512
        * Disable wake on USB
    * Built in device option
        * Disable wake on LAN
1. Verify if VT-D; VT-X are disabled and boot mode on UEFI (native recommended but no windows boot).
1. Save and reboot.

# Installation
1. Boot using your USB drive (F9)
1. Select your USB drive, named as “**Hackintosh Sierra Zone**”
1. Wait until texts and apple logo disappear
1. Click next, next, continue, agree
1. Go to top menu and click utilities->disk utility
1. Click on your hard drive disk (not the partition), right click then erase
1. A popup will show up, choose these:
    * Name : macOS (or what you want)
    * Format : Mac Os Extended (journaled)
    * Scheme : GUID Partition Map
1. Then Erase. After finish close the window.
1. Select your drive “macOS” then customize. Choose these right settings:
    * Boot-loader -> Clover -> check all the three (standard, EFI, UEFI)
    * Graphics -> check Graphics Enabler
    * Drivers -> check all 
        * FakeSMC Plugins -> check all
    * Chipset kexts
        * Check Fixed chipset kexts
        * Chipset driver
    * Check Null*********
    * Laptop drivers
        * Check all (battery percentage & Trackpad ….)
    * Sierra USB Fixes (check these)
        * USB 3.0
        * USB Inject All
    * FakePCIID Kexts (check these)
        * USB 2.0/3.0
        * Intel Ethernet
        * Intel HD graphics
        * Intel HDMI audio
        * Broadcom wifi
    * OS X Fixes
        * Check all
    * Click accept at the bottom
1. Continue
1. The install process will take up to 30 minutes

# Post installation
### 1. Boot from hard drive
1. Download and run [Hackintosh Vietnam Tool](https://drive.google.com/open?id=16ezQQVHtwdbxBkb3B8OtIbNKLLiNizGA) 
1. Click continue, continue, continue, agree.
1. Check Clover -> Install Clover UEFI.
1. Continue, install and done.
1. remove your bootable USB drive. now you're able to boot from hard drive.

### 2.1 Kexts installation
1. Run again **Hackintosh Vietnam Tool** 
1. Choose these right settings:
    * Kexts -> System (check these)
        * FakeSMC v6
        * FakeSMC v6 plugins
        * Patch AppleRTC
    * Kexts -> Graphics (check these)
        * ACPIBacklight
        * FakePCIID HD 4600
    * Kexts -> Drive (check these)
        * AHCIPortInjector
        * Trim Support (only if your drive is SSD)
    * Kexts -> USB 3.0 -> GenericUSBXHCI
    * Fixes
        * Enable NoATime
        * Home/End keys
        * Hibernate -> Disable hibernate
    * Tools
        * IASL
        * Kext Wizard
        * MacIASL
1. Then click continue -> install.

### 2.2 Kexts installation
1. Download these [essential kexts](https://drive.google.com/open?id=1J8kTbd6FmRWq_hR-TRmTE-QFJHpXtDtR)
1. Open Kext Wizard & select **Installation** tab
1. Browse and add downloaded kexts or just drag them to the window.
1. Check **Backup Kexts** then click Install.
1. Wait a minute and it's done.

### 3. Patch DSDT files
You will find the complete guide [here](https://www.tonymacx86.com/threads/guide-patching-laptop-dsdt-ssdts.152573/). there is also a great [video tutorial](https://www.youtube.com/watch?v=dgDaTr1pNaQ) but I'm only noting the main steps here below.
1. Reboot your Hackintosh and go to Clover menu.
1. Press **F4** key. Nothing will be shown at this point but this will extract original ACPI files so we can patch them. you'll find them in **EFI/Clover/ACPI/origin**.
1. copy the **origin** folder to the desktop and rename it to **patched**. A backup is recommended.
1. Delete duplicate SSDT files if there are. they have same sizes and you can determine them by bash command `ls -l SSDT*.aml`.
1. Download [iasl](http://raw.github.com/RehabMan/Laptop-DSDT-Patch/master) to disassemble .aml files to .dsl files. copy the downloaded file to /usr/bin to have ease access in terminal: 
```
  cd ~/Downloads
  unzip iasl.zip
  sudo cp iasl /usr/bin
```
1. Download the file **refs.txt** present in this repo and place it to the **patched** directory.
1. To do so, run this command : `iasl -da -dl -fe refs.txt DSDT.aml SSDT*.aml`
1. Open MaciASL. go to preferences > sources > add :
    * name : RehabMan Laptop
    * url : http://raw.github.com/RehabMan/Laptop-DSDT-Patch/master
4. Go to your **patched** folder and open DSDT.dsl file. it shoud be opened with MaciASL.
4. click Patch icon in the top.
4. now we will apply each patch described below to the DSDT file. you need to select the patch name (the left tree bar) and click patch then compile, it should be compiled without any errors (warnings aren't importants).
    * RehabMan Laptop
        * [audio] audio layout 12
        * [bat] HP ProBook 4x0s G1
        * [igpu] Haswell HD4400/HD4600/HD5000
        * [sys] IRQ fix
        * [sys] SMBUS Fix
4. Once finishing go to File menu->save, then again File->save as-> DSDT.aml (file format: ACPI Machine Language Binary)
4. For SSDT files you need just to compile files without errors. (open dsl file, compile, save, save as .aml) and done.
4. now mount your EFI partition, and go to **EFI->CLOVER->ACPI->patched** and paste all the compiled **.aml** files.
4. close and reboot. everything should work now.

