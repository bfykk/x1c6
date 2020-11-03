﻿# macOS on Thinkpad X1 Carbon 6th Generation, Model 20KH\*

[![macOS](https://img.shields.io/badge/macOS-Catalina-yellow.svg)](https://www.apple.com/macos/catalina/)
[![version](https://img.shields.io/badge/10.15.7-yellow)](https://support.apple.com/en-us/HT210642)
[![BIOS](https://img.shields.io/badge/BIOS-1.45-blue)](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-x-series-laptops/thinkpad-x1-carbon-6th-gen-type-20kh-20kg/downloads/driver-list/component?name=BIOS%2FUEFI)
[![MODEL](https://img.shields.io/badge/Model-20KH*-blue)](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/references/x1c6-Platform_Specifications.pdf)
[![OpenCore](https://img.shields.io/badge/OpenCore-0.6.2-green)](https://github.com/acidanthera/OpenCorePkg)
[![LICENSE](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/996icu/996.ICU/blob/master/LICENSE)

<img align="right" src="https://i.imgur.com/I3yUS4Q.png" alt="Critter" width="300">

### Check out my blog [tylerspaper.com](https://tylerspaper.com/)

#### READ THE ENTIRE README.MD BEFORE YOU START.

#### I am not responsible for any damages you may cause.

### Should you find an error, or improve anything, be it in the config itself or in the my documentation, please consider opening an issue or a pull request to contribute.

`I AM A ONE MAN TEAM, AND A FULL TIME STUDENT. SO, I MIGHT NOT BE ABLE TO RESPOND OR HELP YOU IN A TIMELY MANNER. BUT, I PROMISE I WILL GET TO YOU EVENTUALLY. PLEASE UNDERSTAND.`

`Lastly, if my work here helped you. Please consider donating, it would mean a lot to me.`

> ## Update

##### Recent | [Changelog Archive](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/CHANGELOG.md)

> ### 2020-10-6

#### Changed

- Updated `config.plsit`:
  - Removed depricated ACPI renames in accordance with new ACPI patches.
  - Added `Arch` value to each kext entry in accordance with new OpenCore doc.
  - Added Thunderbolt 3 Device Properties.
  - Added `ExtendBTFeatureFlags` value to replace `BT4LEContinuityFixup`
- Reorganized subdirectories within `/patches/` to make things easier to find and understand.
- Renamed `3_README-POSTinstallation.md` to `SUMMARY.md` since it's not really a step but more of an overview of what patches what.
- More readble and better writing of `SSDT-Keyboard`
- New `SSDT-PNLF` to accomodate `AppleBacklightSmoother.kext`
- New battery patch `SSDT-Battery` that fixes accesses to 16byte-EC-field HWAC (Issue #82).
- `SSDT-Sleep` is an all-in-one sleep patch over `SSDT-PTSWAK`, `SSDT-GPRW`, `SSDT-EXT*`
  - It is no longer necessary to set sleep mode to `Linux` in BIOS as it is now indepently set by `SSDT-Sleep`
- `If (_OSI ("Darwin"))` and `SSDT-DTPG` are now replaced in favor of `SSDT-Darwin` and `OSDW`, just like in genuine Macs.
- Removed `USBPorts.kext` in favor of patching/mapping via ACPI with `SSDT-XHC1`, `SSDT-XHC2`, and `SSDT-USBX`
- `README.md`:
  - Turned different sections into menus for better readability.
  - Merged `3_README-POSTinstallation.md` into the `SUMMARY` section.

#### Added

- `update.sh` script to automatically build and replace all ACPI patches
- `SSDT-HWAC` to patch access to 16byte-EC-field HWAC
- `SSDT-EC` to patch embedded controller for use with `YogaSMC`
- `SSDT-Debug`, `SSDT-HOOKS`, and `Debug.plist` for debugging if needed
- `SSDT-INIT` to configure system values: `HPET`, `DYTC`, and `DPTF`
- `YogaSMC.kext` to interface with the device's EC. Make sure to also install the [app and pref pane](https://github.com/zhen-zen/YogaSMC/releases).
- `AppleBacklightSmoother.kext` is just as its name implies.
- `BrightnessKeys.kext` to handle Fn keys with ACPI renames.
- Documentation of modding the Thunderbolt 3 controller.

#### Removed

- `SSDT-HPET`, similar to genuine Macs, HPET is now disabled within `SSDT-INIT`

#### Remark
- A large of these changes are due to the hardwork of [@benbender](https://github.com/benbender), who debugged and authored many of the new ACPI patches. Thank you for your hard work!

<details>
<summary><strong> SUMMARY </strong></summary>
<br>

> ### Non-Fuctional:
| Feature                              | Status | Dependency          | Remarks                      |
| :----------------------------------- | ------ | ------------------- | ---------------------------- |
| Fingerprint Reader   | ❌ | `DISABLED` in BIOS to save power if not used in other OSes.   | Linux support was only recently added    |
| Wireless WAN   | ❌ | `DISABLED` in BIOS to save power if not used in other OSes.   | Unable to investigate as I have no need and my model did not come with WWAN. |

> ### Video and Audio
| Feature                              | Status | Dependency          | Remarks                      |
| :----------------------------------- | ------ | ------------------- | ---------------------------- |
| Full Graphics Accleration (QE/CI)    | ✅   | `WhateverGreen.kext`                   | -   |
| Audio Recording                      | ✅   | `AppleALC.kext` with Layout ID = 21    | -   |
| Audio Playback                       | ✅   | `AppleALC.kext` with Layout ID = 21    | -   |
| Automatic Headphone Output Switching | ✅   | `AppleALC.kext` with Layout ID = 21    | -   |

> ### Power, Charge, Sleep and Hibernation
| Feature                              | Status | Dependency          | Remarks                      |
| :----------------------------------- | ------ | ------------------- | ---------------------------- |
| Battery Percentage Indication | ✅    | `SSDT-Battery.aml` and `/patches/OpenCore Patches/Battery.plist`             | 
| CPU Power Management (SpeedShift)    | ✅      | `XCPM` and `CPUFriend.kext`, generate your own `CPUFriendDataProvider` with [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend_) or [one-key-cpufriend](https://github.com/stevezhengshiqi/one-key-cpufriend). |
| iGPU Power Management        | ✅ | `XCPM`, enabled by `SSDT-PLUG.aml`                   | 
| NVMe Drive Battery Management  | ✅     | `NVMeFix.kext`  | In my experience, NVMe drives will drain more power than SATA drives.           |
| S3 Sleep/ Hibernation Mode 3 | ✅ | `SSDT-Sleep.aml` | |
| Hibernation Mode 25          | ✅ | `RTCMemoryFixup.kext` and `HibernationFixup.kext`      | Supported, macOS uses mode 3 by default. Change to mode 25 via `pmset`.     |   
| Custom Charge Threshold      | ✅ | `SSDT-EC.aml`, [YogaSMC.kext](https://github.com/zhen-zen/YogaSMC), and [YogaSMCPane](https://github.com/zhen-zen/YogaSMC)| Adjust with YogaSMCPane in System Preferences
| Battery Life                 | ✅ | Native, comparable to Windows/Linux. Biggest impact is TB3, see [docs/1_README-HARDWAREandBIOS.md](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/1_README-HARDWAREandBIOS.md)   |

> ### Input/ Output
| Feature                              | Status | Dependency          | Remarks                      |
| :----------------------------------- | ------ | ------------------- | ---------------------------- |
| WiFi                                       | ✅ | Native with BCM94360CS2. See `/patches/ Network Patches/` otherwise. | See `/patches/OpenCore Patches/` for specific network card.        |
| Bluetooth                                  | ✅ | Native with BCM94360CS2. See `/patches/ Network Patches/` otherwise. | See `/patches/Network Patches/` for specific network card.         |
| Ethernet                                   | ✅ | `IntelMausi.kext` | Needs Lenovo Ethernet adapter: [Item page](https://www.lenovo.com/us/en/accessories-and-monitors/cables-and-adapters/adapters/CABLE-BO-Ethernet-Extension-Adapter-2/p/4X90Q84427) |
| HDMI hotplug                               | ✅ | Custom EDID Override `/patches/Internal Displays/`                                                                  | Refer to [Issue #60](https://github.com/tylernguyen/x1c6-hackintosh/issues/60) if one does not exist already for your display. |
| 4K UHD output via HDMI/ DisplayPort **(Modded BIOS)**  | ✅ | See `DMVT Pre-Allocated` to `64M`  | See [docs/1_README-HARDWAREandBIOS.md](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/1_README-HARDWAREandBIOS.md) for information about modding the BIOS.           |
| 4K UHD output via HDMI/ DisplayPort **(Vanilla BIOS)** | ✅ | See `/patches/OpenCore Patches/4K-Output-wo-BIOSmod.plist`     | -           |
| USB 2.0, USB 3.0, and Micro SD Card Reader | ✅ | `SSDT-XHC1.aml`    | -     |
| USB 3.1                                    | ⚠️ | `SSDT-XHC2.aml`    | -     |
| USB Power Properties in macOS              | ✅ | `SSDT-USBX.aml`    | -     |
| Thunderbolt 3 **(Cold Boot)**                  | ✅ | `SSDT-TB3.aml`,    | TB3 device must be plugged in before boot.   |
| Thunderbolt 3 Hotplug **(Modded Controller and BIOS)**  | ⚠️ | `SSDT-TB3.aml`    | [More details](https://github.com/tylernguyen/x1c6-hackintosh/issues/24#issuecomment-603183002)   |
| Thunderbolt 3 Hotplug **(Modded Controller and Vanilla BIOS)**  | ⚠️ | `SSDT-TB3.aml` | [More details](https://github.com/tylernguyen/x1c6-hackintosh/issues/24#issuecomment-603183002)   |
| Thunderbolt 3 Hotplug **(Vanilla Controller and Modded BIOS)**  | ⚠️ | `SSDT-TB3.aml`, `ThunderboltReset.kext` | [More details](https://github.com/tylernguyen/x1c6-hackintosh/issues/24#issuecomment-603183002)   |
| Thunderbolt 3 Hotplug **(Vanilla Controller and BIOS)** | ⚠️ | `SSDT-TB3.aml`, `ThunderboltReset.kext`, and `TbtForcePower.efi`    | [More details](https://github.com/tylernguyen/x1c6-hackintosh/issues/24#issuecomment-603183002)   |

> ### Display, TrackPad, TrackPoint, and Keyboard
| Feature                              | Status | Dependency          | Remarks                      |
| :----------------------------------- | ------ | ------------------- | ---------------------------- |
| Brightness Adjustments | ✅  | `WhateverGreen.kext`, `SSDT-PNLF.aml`, `AppleBacklightSmoother.kext`, and `BrightnessKeys.kext`| `AppleBacklightSmoother.kext` is optional for smoother birghtness adjustments |
| HiDPI _(Optional)_     | ✅  | [xzhih/one-key-hidpi](https://github.com/xzhih/one-key-hidpi)   | Scaling issues post-sleep fixed with AAPL, ig-platform `BAAnWQ==`     |
| TrackPoint             | ✅  | `VoodooPS2Controller.kext`                                      | -       |
| TrackPad               | ✅  | `VoodooPS2Controller.kext` or `VoodooRMI.kext`                  | I prefer `VoodooRMI.kext` so that is the repository default. |
| Built-in Keyboard      | ✅  | `VoodooPS2Controller.kext` | Optimizations recommended, see [`docs/4_README-other.md`](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/4_README-other.md) |
| Multimedia Keys        | ✅  | [YogaSMC](https://github.com/zhen-zen/YogaSMC) or [ThinkpadAssistant](https://github.com/MSzturc/ThinkpadAssistant) | -       |

> ### macOS Continuity
| Feature                              | Status | Dependency          | Remarks                      |
| :----------------------------------- | ------ | ------------------- | ---------------------------- |
| iCloud, iMessage, FaceTime | ✅ | Whitelisted Apple ID, Valid SMBIOS   | See [dortania /OpenCore-Install-Guide](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html)  |
| Continuty              | ✅     | Native with `BCM94360CS2`. `ExtendBTFeatureFlags` to `True` otherwise.       | See `/patches/Network Patches/` for specific network card.     |
| AirDrop                | ✅     | Native with `BCM94360CS2`. `ExtendBTFeatureFlags` to `True` otherwise.       | See `/patches/Network Patches/` for specific network card.     |
| Sidecar                | ✅     | Native with `BCM94360CS2`. `ExtendBTFeatureFlags` to `True` otherwise. iPad with >= `iPadOS 13`  | Tested with iPad Mini with iPadOS 13.1.2   |
| FileVault | ✅ | as configured in `config.plsit` per [Dortania's Post-Install](https://dortania.github.io/OpenCore-Post-Install/universal/security/filevault.html)|  |
| Time Machine           | ✅     | Native | TimeMachine only backups your Macintosh partition. Manually backup your EFI partition using another method.  |

</details>

<details>
<summary><strong> REFERENCES </strong></summary>
<br>

* Read these before you start:
- [dortania's Hackintosh guides](https://github.com/dortania)
- [dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- [dortania's OpenCore Post Install Guide](https://dortania.github.io/OpenCore-Post-Install/)
- [dortania/ Getting Started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)
- [dortania/ opencore `multiboot`](https://github.com/dortania/OpenCore-Multiboot)
- [dortania/ `USB map` guide](https://dortania.github.io/OpenCore-Post-Install/usb/)
- [WhateverGreen Intel HD Manual](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md)
- `Configuration.pdf` and `Differences.pdf` in each `OpenCore` releases.
- Additionally, references specific to the x1c6 are located in `docs/references/`

* ### No seriously, please read those.
</details>  

<details>
<summary><strong> NEEDED </strong></summary>
<br>

- A macOS machine(optional): to create the macOS installer.
- Flash drive, 12GB or more, for the above purpose.  
- Xcode works fine for editing plist files on macOS, but I prefer [PlistEdit Pro](https://www.fatcatsoftware.com/plisteditpro/).  
- [ProperTree](https://github.com/corpnewt/ProperTree) if you need to edit plist files on Windows.  
- [MaciASL](https://github.com/acidanthera/MaciASL), for patching ACPI tables and editing ACPI patches.
- [MountEFI](https://github.com/corpnewt/MountEFI) to quickly mount EFI partitions.  
- [IORegistryExplorer](https://developer.apple.com/downloads), for diagnosis.  
- [Hackintool](https://www.insanelymac.com/forum/topic/335018-hackintool-v286/), for diagnostic ONLY, Hackintool should not be used for patching, it is outdated.
- [SPI Programmer CH341a and SOIC8 connector](https://www.amazon.com/Organizer-Socket-Adpter-Programmer-CH341A/dp/B07R5LPTYM) are needed if you are going to mod your BIOS/TB3 controller for optimizations and a better and more native macOS experience.
- Patience and time, especially if this is your first time Hackintosh-ing.

</details> 

<details>
<summary><strong> SPECIFICATIONS </strong></summary>
<br>
- These are relevant components on my machine which may differ from yours, keep these in ind as you will need to adjust accordingly, depending on your machine's configuration.

| Category  | Component                            |
| --------- | ------------------------------------ |
| CPU       | [i7-8650U](https://ark.intel.com/content/www/us/en/ark/products/124968/intel-core-i7-8650u-processor-8m-cache-up-to-4-20-ghz.html)                  |
| GPU       | Intel UHD 620                        |
| SSD       | Seagate Firecuda 520 500GB           |
| Display   | 14.0" (355mm) HDR WQHD (2560x1440)   |
| WiFi & BT | BCM94360CS2                          |
| WWAN      | None |

- Refer to [/docs/references/x1c6-Platform_Specifications](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/references/x1c6-Platform_Specifications.pdf) for possible stock ThinkPad X1 6th Gen configurations.

</details> 

<details>
<summary><strong> GETTING STARTED </strong></summary>
<br>

Before you do anything, please familiarize yourself with basic Hackintosh terminologies and the basic Hackintosh process by throughly reading Dortania guides as linked in `REFERENCES`

- Creating a macOS installer: refer to [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/)
- [**1_README-HARDWAREandBIOS**](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/1_README-HARDWAREandBIOS.md): Requirements before installing.  
- [**2_README-ACPIpatching**](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/3_README-ACPIpatching.md): Notes and explainations for ACPI hotpatches.
- [**3_README-other.md**](https://github.com/tylernguyen/x1c6-hackintosh/blob/master/docs/4_README-other.md): for post installation settings and other remarks.

</details> 

<details>
<summary><strong> OTHER REPOSITORIES </strong></summary>
<br>

- x1c6-hackintosh repositories:
  - [benbender/x1c6-hackintosh](https://github.com/benbender/x1c6-hackintosh)
  - [zhtengw/EFI-for-X1C6-hackintosh](https://github.com/zhtengw/EFI-for-X1C6-hackintosh)   
- t480-hackintosh repositories:
  - [EETagent/T480-OpenCore-Hackintosh](https://github.com/EETagent/T480-OpenCore-Hackintosh)
Create a pull request if you like to be added, final decision at my discreation.
</details> 

> ## CONTACT

https://tylerspaper.com/contact  
Signal: +1 (202)-644-9951 \*This is a Signal ONLY number. You will not get a reply of you text me at this number.

> ## SUPPORT

https://tylerspaper.com/support/

<details>
<summary><strong> CREDITS </strong></summary>
<br>

- [@benbender](https://github.com/benbender) for your hardwork. Much of this repo comes from your research and code. Thank you!
- [@Fewtarius](https://github.com/fewtarius) for your help with patching audio.
- [@Colton-Ko](https://github.com/Colton-Ko/macOS-ThinkPad-X1C6) for the great features template.  
- [@stevezhengshiqi](https://github.com/stevezhengshiqi) for the one-key-cpufriend script.  
- [@corpnewt](https://github.com/corpnewt) for [GibMacOS](https://github.com/corpnewt/gibMacOS) and [EFIMount](https://github.com/corpnewt/MountEFI).
- [@xzhih](https://github.com/xzhih) for one-key-hidpi.  
- [daliansky/OC-little](https://github.com/daliansky/OC-little) for various ACPI hotpatch samples.  
- [@velaar](https://github.com/velaar) for your continual support and contributions.     
- [@Porco-Rosso](https://github.com/Porco-Rosso) putting up with my requests to test repo changes.  
- [@MSzturc](https://github.com/MSzturc) for adding my requested features to ThinkpadAssistant.  
paranoidbashthot and \x for the BIOS mod to unlocked Intel Advance Menu.
- [@zhen-zen](https://github.com/zhen-zen) for YogaSMC
- [CaseySJ](https://www.tonymacx86.com/members/caseysj.2134452/) for the custom Thunderbolt 3 firmware.

The greatest thank you and appreciation to the [Acidanthera](https://github.com/acidanthera) team.

And to everyone else who supports and uses my project.

Please let me know if I missed you.

</details> 