# Ryzen Hackintosh EFI

![Ryzen Hackintosh](docs/hackintosh_macos_sonoma.png)

## What is this

First of all, this is fork of [this wonderfull work for Ventura](https://github.com/awesometic/hackintosh-gigabyte-x570-aorus-elite/) but adаpted to nearly released **macOS Sonoma 14.0** .
I have a bit different hardware setup, so all parameteres are in table. All drivers were updated to latest versions so as OpenCore to 0.9.5 version.

This repository contains the EFI directory for Ryzen 5600X and Gigabyte X570s Aorus Elite AX combo, with no additional components.

## Specification of my computer

| Component    | Product Name                                     | Note                                           |
|--------------|--------------------------------------------------|------------------------------------------------|
| CPU          | AMD Ryzen 5 5600X                                | PBO enabled                                    |
| Mainboard    | Gigabyte X570s Aorus Elite AX                    | F5 BIOS                                        |
| Memory       | Patriot Viper 4 Blackout DDR4 3600MHz 16GB       | With 18-22-22-42 timings                       |
| Graphics     | AMD Radeon RX 580 4GB                            | Changed its thermal pad and thermal paste      |
| M.2 NVMe     | KINGSTON SKC2500M8250G                           | macOS 14 installed                             |
| M.2 SATA     | Kingston SHPM2280P2H/240G                        | Windows 11 installed                           |
| PCI Ethernet | internal Realtek RTL8125                         | Ethernet device                                |
| BT/WIFI      | internal AMD Wi-Fi 6E RZ608 (MT7921K) & BT 5.2   | not using it and no support in macOS at all    |


## EFI structure

### WARNING

- I recommend you use this as only a reference resource.
  - Obviously, this build may not be the best one.
  - This EFI contains additional kexts in **config.plist** rather than only the essential things for X570s + Zen3 CPU. You should remove them before using this on your PC.

### Check this before you use

In the [config.plist](EFI/OC/config.plist) file, I've replaced the private serial codes into the `EDIT_HERE` words because to keep my personal information safe.

So if you are going to use this, you have to make sure that the `EDIT_HERE` texts are changed to yours. To generate the serial key, please refer to the [Dortania's OpenCore Guide](https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html#platforminfo). When you are about to generate one, you should select `MacPro7,1` to properly use your machine.

> - If you are going to convert SMBIOS from **iMacPro1,1** to **MacPro7,1**, make sure that you must logout Apple ID from your current system and regenerate all the SMBIOS details such as MLB, serial number, UUID for MacPro7,1.
> - If your CPU has less than 8 cores, go to `config.plist` file and find "PlatformInfo->Generic" and change the "ProcessorType" from 0 to 1537.

### You got another things you must check

#### Different options by CPU core counts

From the recent AMD CPU patch, now we have to specify the CPU core counts to the `algrey - Force cpuid_cores_per_package` nodes. Currently, my EFI setup sets that for the **6-core** CPU model because I'm using Ryzen 5600X.

- `algrey - Force cpuid_cores_per_package`
  - 10.13,10.14
    - B8**06**0000 0000
  - 10.15,11.0
    - BA**06**0000 0000
  - 12.0,13.0
    - BA**06**0000 0090

Please refer to [the author's description](https://github.com/AMD-OSX/AMD_Vanilla#read-me-first) to get further information.

#### About the Above 4G Decoding option in BIOS

In my setup, I ENABLED **Above 4G Decoding** in my BIOS and removed `npci=0x3000` from the boot args because it doesn't boot with that option disabled.

If you want to use my EFI setup, you have to check whether this option enabled or not, if you want to disable **Above 4G Decoding** then you should add that `npci=0x3000` arguments to the `boot-args`.

#### About fixing PAT methods

I choose Shaneee's one due to the better GPU performance. If you have trouble with HDMI/DP audio, edit `config.plist` to use Algrey's one.

### OpenCore

- Version: 0.9.5

### ACPI

- SSDT-HPET.aml - Fixes IRQ conflicts
- SSDT-NVME.aml - Makes NVMe drives shown as an internal storage
- SSDT-PLUG.aml - Fixes CPU power management
- SSDT-SBRG.aml - Fixes EC, RTC, IRQ conflicts
- SSDT-SBUS-MCHC.aml - Fixes SMBus support
- SSDT-EC-USBX-AMD.aml - Fixes embedded controller and USB power properties

### Drivers

- OpenCanopy.efi
- OpenHfsPlus.efi
- OpenRuntime.efi

### Kexts

- AMDRyzenCPUPowerManagement.kext
- AppleALC.kext
- AppleMCEReporterDisabler.kext
- CtlnaAHCIPort.kext
- Lilu.kext
- LucyRTL8125Ethernet.kext
- NVMeFix.kext
- RadeonSensor.kext
- RestrictEvents.kext
- SMCAMDProcessor.kext
- SMCRadeonSensor.kext
- VirtualSMC.kext
- WhateverGreen.kext

### Tools

- OpenShell.efi

## What works and what doesn't work

### Works

- Almost everything including Apple continuity (Handoff, iMessage, Airdrop, Facetime, Universal Control, ...).

### Partially works

- 3.5mm audio jacks
  - The speaker output on the front/back panel works.
  - The microphone input on the front/back panel doesn't work.
  - Optical digital output works.
- The common Ryzentosh issues. Please refer to the CPU support part of [Dortania's OpenCore Guide](https://dortania.github.io/OpenCore-Install-Guide/macos-limits.html#cpu-support)
  - Specific professional applications may need to be patched for AMD processors such as Adobe apps, Davinci Resolve, etc.
  - Virtualization (Apple Hypervisor and the apps using this like AVD on Android Studio, Parallels) is not working but VirtualBox works.

### Doesn't work

- Sidecar

## Main References

I've got the SSDTs and configuring hints from the following links.

- <https://dortania.github.io/OpenCore-Install-Guide/>
- <https://forum.amd-osx.com/index.php?threads/ms-x570-aorus-elite-5700-xt-r7-3800x-big-sur-oc-0-6-6.1524/>
- <https://forum.amd-osx.com/index.php?threads/audiogods-gigabyte-aorus-x570-pro-pro-wifi-ultra-master-big-sur-monterey-beta-opencore-0-7-4-efi.1344/>
