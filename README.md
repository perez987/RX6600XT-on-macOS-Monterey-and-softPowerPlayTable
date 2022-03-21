# XFX RX 6600 XT graphics card in macOS Monterey 12.2.1

**XFX Speedster QICK 308 AMD Radeon RX 6600 XT Black 8GB GDDR6**

![RX 6600 XT](xfx-6600xt.png?raw=true)

### Preface

Although graphics cards assembled by XFX have negative comments in Hackintosh forums by having custom BIOS that can be more problematic for macOS that of other brands, I have installed a XFX QICK 308 AMD Radeon RX 6600 XT 8GB in Monterey 12.2.1 and the result has been excellent, installation was very simple and performance is much higher than that of the previous card, RX580 8GB.

This card is one of the 6600 XT cheapest even though it can still be considered expensive compared to what would be logical in other circumstances (shortage of components and mined cryptocurrencies).

The main components of my computer are Z390 Aorus Elite board and Intel i9-9900K CPU.

Current macOS status of AMD 6000 series graphics cards is:

Working on macOS
- Family Navi 21: 6800, 6800 XT and 6900 XT (since Big Sur 11.4)
- Family Navi 23: 6600 and 6600 XT (since Monterey 12.1).

NOT working on macOS
- Family Navi 22: 6700 XT
- Family Navi 24: 6400, 6500 and 6500 XT.

Of course 6800 and 6900 series are clearly more powerful than the 6600 but their current market price is very high. The 6600 XT has higher performance than the 6600 and this model of XFX can now be found for a price of around 500-550 €.

The card is long size but no longer than the XFX RX 580 it will replace. Surprisingly, it's even slightly lighter, probably because of the metal housing that the RX580 incorporates. Requires 8 pin power connector and recommended power supply is at least 600-650W. It has 4 DisplayPort ports and 1 HDMI port.

### Installation

Physical placement of the card does not deserve comment, it's like any other PCI-e slot card.

Installation on macOS is very simple. The same EFI with OpenCore 0.7.8 (Lilu and WhateverGreen included) that worked with the RX580 works for the 6600XT with a single change: add `agdpmod=pikera` in boot-args to prevent the screen from going black on the desktop.

The card is well recognized as seen in System profile.

### Working on macOS

Overall performance is very good, smooth, with 2560x1440 resolution at 60Hz on a 4K monitor. The score in the GeekBench 5 test is around 60% higher than the RX580 (around **80000** vs **50000**).

### Working on Windows

Many Hackintosh users have double booting with Windows. Here the impressions are also very good, system has kept the same AMD drivers without requiring update.

The score in the Geeks3D FurMark test is double with the RX 6600 XT than with the RX580 (approx. **6100** vs **3000**).

### Temperature sensor

Starting with the Radeon VII model, it is necessary to use kexts to read the temperature of AMD graphics cards since macOS stopped exposing that data directly. This also applies to the 6000 series. To know the temperature of the card you can use *Aluveitie* RadeonSensor. It consists of 3 elements:

- Radeon sensor.kext: Lilu plugin to read card temperature
- SMCRadeonGPU.kext: to export data via VirtualSMC to monitoring tools such as iStat Menus
- RadeonGadget.app: to display the temperature in the menu bar, it requires RadeonSensor.kext only.

Note: SMCRadeonGPU.kext has to go after RadeonSensor.kext in the config file.plist of OpenCore and of course both after Lilu and VirtualSMC.

I have tested these 2 extensions together and they seem to work well, iStat Menus adds the temperature of the 6600 XT as one more sensor to display in the menu bar.

### Resizable BAR (ReBAR)

AMD Radeon  6600 cards support ReBAR. To activate this feature you must:

- Enable it in BIOS menu (usually next to Above 4G Decoding option, ReBAR is displayed when enabling this other option)
- Set config file.plist in order for OpenCore to boot with ReBAR enabled, you have to set the value of Boot >> Quirks >> ResizeAppleGpuBars=0 (instead of -1, default value).

**Note**: UEFI >> Quirks >> ResizeGpuBars must always be -1.
 
I have tested the card with ReBAR on and off and I have not noticed any difference. GeekBench 5 test scores on macOS and FurMark on Windows have been virtually identical.
It is likely that with a CPU of 10th generation or newer and games of big graphic demand the performance will improve with ReBAR enabled but, at least in my system, there is no gain in it.

## Zero RPM and Monterey 12.3

### Zero RPM

By default, fans of the RX 6600 XT card (like other AMD models) are stopped below 60º, it is what is known as Zero RPM. This has as main advantage the absence of noise except when there is a high graphic requirement.

Users who have this card in a Hackintosh with dual boot have observed that temperature, with idle system, is usually 10-15º lower in Windows than in macOS (35-40º vs 50º). In both systems Zero RPM keeps fans stopped until 60º is reached.

On Windows it is easy to enable/disable Zero RPM from the Radeon software that has this option in custom settings. But on macOS there is no such possibility. So far, existing option to disable Zero RPM in macOS is the creation on Windows, from the AMD card ROM, of a SoftPowerPlayTable (sPPT) (contains the graphics card settings in the form of a hexadecimal value) that OpenCore can load into DeviceProperties. If the sPPT is saved in Windows after disabling Zero RPM, macOS when loading the sPPT also works with Zero RPM disabled. But it is a complex task that requires specific programs and is not within the reach of the inexperienced user.

### AMD 5000 and 6000 in Monterey 12.3

The release of macOS Monterey 12.3 has broken the operation of Radeon 5000 and 6000 families, not in all cases but in quite a few of them judging by comments posted on the forums. This problem has also happened on real Macs but it seems to be more much more frequent on Hackintosh. 5500, 5700, 6800 and 6900 models (XT and non XT) have been most affected. 6600 models (XT and non XT) seem to be free of the issue that manifests itself in a very evident drop in graphic performance after updating to 12.3, in some cases the system becomes unusable and in other cases a big part of the graphic power is simply lost.

My GPU is RX 6600 XT so it has not been affected by this issue.

Solutions have been proposed to fix this. The simplest is to add in DeviceProperties of config.plist some properties that set  Henbury framebuffer for each of the 4 ports of this GPU. By default Radeon framebuffer (ATY,Radeon) is loaded. But on AMDRadeonX6000Framebuffer.kext (in its Info.plist file) AMDRadeonNavi23Controller has "ATY,Henbury" and 6600 series are Navi 23. This is why this framebuffer is specifically proposed.
The patch is added in this way:

On Windows it is easy to enable/disable Zero RPM from the Radeon software that has this option in custom settings. But on macOS there is no such possibility. So far, existing option to disable Zero RPM in macOS is the creation on Windows, from the AMD card ROM, of a SoftPowerPlayTable (sPPT) (contains the graphics card settings in the form of a hexadecimal value) that OpenCore can load into DeviceProperties. If the sPPT is saved in Windows after disabling Zero RPM, macOS when loading the sPPT also works with Zero RPM disabled. But it is a complex task that requires specific programs and is not within the reach of the inexperienced user.
```
<key>DeviceProperties</key>
    <dict>
        <key>Add</key>
        <dict>
            <key>PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x0)/Pci(0x0,0x0)/Pci(0x0,0x0)</key>
            <dict>
                <key>@0,name</key>
                <string>ATY,Henbury</string>
                <key>@1,name</key>
                <string>ATY,Henbury</string>
                <key>@2,name</key>
                <string>ATY,Henbury</string>
                <key>@3,name</key>
                <string>ATY,Henbury</string>
            </dict>
        </dict>
        <key>Delete</key>
        <dict/>
    </dict>
```
