# Colorful_CVN_B760I_FROZEN_WIFI_V20_hackintosh
hackintosh EFI for CVN B760I FROZEN WIFI V20 motherboard

[中文](README-ZH.md)

## Hardware Info

- CPU: 12490F

- Motherboard: Colorful CVN B760I FROZEN WIFI V20

- BIOS Version: 1004 (2023/01/05)

- Memory: JINGBAIDA Silver Knight DDR4 3200Mhz ChangXin Adie 16GB (8x2)

- SSD: Samsung 980pro 1TB

- Graphics Card: PowerColor Gaming Edition 6600xt 8GB (Mining Card)

- Wireless Card: Intel AX201

- Ethernet Card: RTL 8125

- Case: Shiny Snake S400ITX

- Power: Silverstone SX500-LG 500W Gold

## EFI Info

- OpenCore Version: 0.9.0
- macOS Version: macOS Ventura 13.2.1

## BIOS Settings

### Disable

- Resize BAR Support (ResizableBar support can also be disabled by setting Booter->Quirks->ResizeAppleGpuBars to 0 in OpenCore config.)
- CSM
- Secure Boot
- cfg lock: **Must be disabled! Otherwise, you will get stuck in EB!** BIOS does not provide a setting to disable cfg lock, so EFI tools are needed. The specific steps will be introduced separately below.
- ME Write Protection: Required to disable cfg lock.

### Enable

> Settings below can use default BIOS settings.

- vt-d
- vt-x
- above 4G Decoding

## CFG Unlock

Thanks to the 'powerful' features of the Colorful motherboard BIOS, obscure settings such as cfg-lock cannot be directly modified in the BIOS.

Of course, hardcore methods like directly flashing BIOS is possible, but obviouly with high risk.

Therefore, we need EFI tools to perform the cfg-unlock operation:

TBD: screenshot

Enable the EFI tool shown in the above image. Then, when entering the OpenCore boot interface, select the tool to execute.

## Installation Process
1. Set up BIOS.
2. Use this EFI to create a USB installation image (please search for tutorials on your own. OpenCore official documentation is recommended);
3. Insert the USB drive into the USB 2.0 interface on the motherboard IO baffle, and restart by pressing F11. Select the first USB drive boot, enter the OpenCore boot interface;
4. Use the EFI tool to unlock cfg-lock, then restart;
5. Enter the OpenCore boot again and select "Install macOS Ventura" to start installation;
6. Wait, then enter the recovery system.
7. Select "Disk Utility" to format the target disk/partition as APFS;
8. Then exit Disk Utility, select Install System, and click Next all the way through.
9. If you have Windows dual boot, copy `OC` folder in USB drive EFI into the ESP Partition's `EFI` folder. Then add `EFI/OC/OpenCore.efi` as a UEFI boot item using tools like EasyUEFI.

## Important

Customized USB before installation: Through trial and error, I found that `USBInjectAll.kext` cannot recognize USB ports of this motherboard very well. Therefore, during the installation process using an EFI without customized USB, an error message `Still waiting for root device` will be encountered, and a prohibition icon will be displayed in the center of the screen. This error message indicates that during the preparation for installation, the USB port where the installation USB drive is plugged cannot be correctly recognized, resulting failure of installation process.

Therefore, the best solution is to use USBToolBox to customize USB ports of motherboard and case before installation, and then use the customized EFI to install.

Please refer to the USBToolBox github: [USBToolBox](https://github.com/USBToolBox/tool/)

In addition, if you are using the same motherboard and case as me, you can directly use the `USBPorts.kext` in my EFI.

## Benchmark

TBD

## Current Issues (Continuously Updated)

- [ ] Incomplete support for Intel wireless cards, a lot native functions like Airdrop cannot be used.

- [x] ACPI not customized: customized through SSDTTime.

- [x] Imperfect CPU scheduling, force Turbo Boost through AppleXcpmForceBoost: thanks to the [previous work](https://github.com/LimeVista/Hackintosh-H610-12490F-AX201), currently the scheduling is quite good.

- [x] 980 Pro SSD TRIM: forced enable with `sudo trimforce enable`.

- [x] Lagging & stutter during use: the most complicated issue, detailed explanation below.

### Lagging & stutter during use

Symptoms: Randomly freezes, possibly accompanied by a rainbow cursor. Frequently occurs during input method switching, application window opening/closing, and network video playback. The frequency of occurrence is much higher when connected to the internet than when not. Besides, the longer the computer is on, the more frequently this issue occurs.

Hypothesis: Based on the severity increase when connected to the internet, this issue may be closely related to network drivers. However, as both LucyRTL8125 and AirportItlwm cause similar issues when enabled, it is difficult to determine the exact cause.

Additional information: In the kernel log information, there are many `kernel connect() - failed necp_set_socket domain attributes` errors, but no related information was found on this error log.

Update: This issue was mostly resolved by reinstalling on a partition of the main hard drive, which is located in the front of the motherboard. Although it still occasionally lags, it is mostly usable now. I suspect that the cause of this issue may be multifaceted and may be related to the following factors:

1. Southbridge bandwidth and latency: the most likely cause. The DMI bus connecting the b760 chipset and the CPU will be used by network IO, disk IO, and possibly even GPU communication, and the DMI (PCIe) bus channel is usually exclusive. Therefore, frequent background IO operations may cause a bottleneck in the DMI bus, resulting in user operation delays and frequent lagging during use. (Of course, these are just my speculations, my technical ability is not enough to verify this hypothesis. Experienced experts may try to verify it.)

2. Unstable memory access: this has already been verified through several little experiments. In simple memory read/write and copy tests, the delay overhead of the same operation fluctuates greatly. For example, the time overhead of copying a 512MB memory block may fluctuate by up to 0.5 seconds. Clearly, this phenomenon is the direct cause of frequent lagging. However, the underlying cause of this issue is hard to determine.

3. Overheating of the SSD causing speed drops: one of the additional hypothesis. I did not install a case fan. Combined with the heat generated by the 980 Pro and the exposed drive on the backplate of motherboard, the temperature of the 980 Pro might rise quickly during use, causing the system to become more laggy.

4. Other factors: there are too many possibilities causing a hackintosh to lag: hard drive, memory, motherboard compatibility, EFI's incorrect configuration, flaws of forced TRIM enablement, imperfect CPU scheduling, imperfect network card driver/hardware adaptation, and other reasons. There are too many challenges to achieve a perfect Hackintosh, especially when hardwares are not perfectly compatible.

In any case, this issue is now partially resolved. It can also be a lesson for everyone: **do not install on a secondary drive if you're using this motherboard**. For those interested in this issue, you can also try to dig deeper.