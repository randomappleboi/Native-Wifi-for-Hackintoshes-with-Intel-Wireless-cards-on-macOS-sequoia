# Enabling AirportItlwm native wifi on intel wireless cards on macOS Sequoia
A tutorial on using AirportItlwm for enabling native macOS wifi on hackintoshes with Intel Wireless cards running macOS 15 Sequoia

---

For this guide, you'll need good knowledge of the config.plist structure, although this tutorial is fairly simple. You should be able to do this within 45 mins to 2 hrs. This guide is seperated in 3 different parts: in part 1, we will spoof the intel wireless card as a broadcom card. In part 2, we will use OCLP to patch the card and get wifi working. In part 3, we will fix bluetooth, as it breaks sometimes during the patching. Additionally, this guide assumes that you have a fully functional hackintosh already.

---

## Prerequisets: 
[Hackintool]
[OpenCore Legacy Patcher]
[ProperTree] (or any other .plist editor)
[IO80211FamilyLegacy.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Wifi/IO80211FamilyLegacy-v1.0.0.zip)
[IOSkywalkFamily.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Wifi/IOSkywalkFamily-v1.2.0.zip)
[AMFIPass.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Acidanthera/AMFIPass-v1.4.1-RELEASE.zip)
[AirportItlwm.kext](https://github.com/openintelwireless/itlwm/releases) **Get the AirportItlwm_v2.3.0_stable_Ventura.kext.zip!!!**
[]()
[]()

Step 1.: Spoofing:
First, open Hackintool and navigate to the PCIe section. There, you will find your Intel Wireless card. Mine is listed as "Intel Cooperation | Wireless 8260 | Network Controller". Look at it´s Device Path and right click it. Select "Copy device path. ![Hackintool page](hackintool.png)

Then, open your config.plist in your .plist editor of choice and find the "DeviceProperties" tab. Under "Add", add a new dictionary with the name of *your* network card´s Device Path:
| Key | Type | Value |
| ----------- | ----------- | ----------- |
| IOName | Title | e |
| Paragraph | Text |
