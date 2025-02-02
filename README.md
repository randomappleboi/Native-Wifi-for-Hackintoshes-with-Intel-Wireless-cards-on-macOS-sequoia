# Enabling AirportItlwm native wifi on intel wireless cards on macOS Sequoia
A tutorial on using AirportItlwm for enabling native macOS wifi on hackintoshes with Intel Wireless cards running macOS 15 Sequoia

---

For this guide, you'll need good knowledge of the config.plist structure, although this tutorial is fairly simple. You should be able to do this within 45 mins to 2 hrs. This guide is seperated in 3 different parts: in part 1, we will spoof the intel wireless card as a broadcom card. In part 2, we will use OCLP to patch the card and get wifi working. In part 3, we will fix bluetooth, as it breaks sometimes during the patching. Additionally, this guide assumes that you have a fully functional hackintosh already. I am not responsible for any data lost. Proceed at your own caution and make sure to make a backup of your working EFI first.

---

## Prerequisets: 
[Hackintool](https://github.com/benbaker76/Hackintool)  
[OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher)  
[ProperTree](https://github.com/corpnewt/ProperTree) (or any other .plist editor)  
[IO80211FamilyLegacy.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Wifi/IO80211FamilyLegacy-v1.0.0.zip)  
[IOSkywalkFamily.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Wifi/IOSkywalkFamily-v1.2.0.zip)  
[AMFIPass.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Acidanthera/AMFIPass-v1.4.1-RELEASE.zip)  
[AirportItlwm.kext](https://github.com/openintelwireless/itlwm/releases) **Get the AirportItlwm_v2.3.0_stable_Ventura.kext.zip!!!**  

---

## Step 1.: Spoofing:  
First, open Hackintool and navigate to the PCIe section. There, you will find your Intel Wireless card. Mine is listed as "Intel Cooperation | Wireless 8260 | Network Controller". Look at it´s Device Path and right click it. Select "Copy device path". ![Hackintool page]([hackintool.png](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/e96836b5b26ffe3a2e0bf7cb7c29d456986b8eb4/assets/S1/Hackintool.png))

Then, open your config.plist in your .plist editor of choice and find the ```DeviceProperties``` tab. Under ```Add```, add a new dictionary with the name of *your* network card´s Device Path:
| Key | Type | Value |
| ----------- | ----------- | ----------- |
| IOName | String | pci14e4,43a0 |
| compatible | Sting | pci106b,117 |
| device-id | Data | A0430000 |
| device_type | String | Network Controller |
| model | Sting | BCM4360 802.11ac Wireless Network Adapter |
| name | String | pci14e4,43a0 |
| pci-aspm-default | Number | 0 |
| subsystem-id | Data | 17010000 |
| subsystem-vendor-id | Data | 6B100000|
| vendor-id | Data | E4140000 |

It should look like this now: ![PCIRoot](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/refs/heads/main/assets/S1/PCI_PT.png) ![PCIRoot](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/refs/heads/main/assets/S1/PCI_OCAT.png)

---

After that, it´s time to add the kexts. Add the kexts to your EFI folder: ```EFI > OC > Kexts```. In ProperTree, press "⌘ + r" to add them into the config.plist, or simply drag and drop them into your OCAT window. Make sure to watch their order very carefully. From bottom to top: *"AirportItlwm.kext > AMFIPass.kext > IO80211FamilyLegacy.kext/Contents/PlugIns/AirPortBrcmNIC.kext > IO80211FamilyLegacy.kext > IOSkywalkFamily.kext"*. Here a table:
| Number | Kext |
| ----------- | ----------- |
| 1 | IOSkywalkFamily.kext |
| 2 | IO80211FamilyLegacy.kext |
| 3 | IO80211FamilyLegacy.kext/Contents/PlugIns/AirPortBrcmNIC.kext |
| 4 | AMFIPass.kext |
| 5 | AirportItlwm.kext |

Warning: Make sure you don´t have itlwm.kext enabled. If you do, disable it or delete it completely.

It should look like this now: ![Kexts](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/refs/heads/main/assets/S1/Kexts_PT.png) ![Kexts](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/refs/heads/main/assets/S1/Kexts_OCAT.png)

---

Now, we need to block one of Apple´s kexts from loading. For that, go to the ```Kernel > Block``` section and enable ```Allow ÌOSkywalk Downgrade```.

It should look like this now: ![Block](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/refs/heads/main/assets/S1/Block_PT.png) ![Block](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/refs/heads/main/assets/S1/Block_OCAT.png)

---

Also, for OCLP to work, you need to set ```csr-active-config``` (located under ```NVRAM > 7C436110-AB2A-4BBB-A880-FE41995C9F82```) and set it to ```03080000```.

That should look like this: ![csr-active-config](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/refs/heads/main/assets/S1/CSR_PT.png) ![PCIRoot](https://raw.githubusercontent.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia/refs/heads/main/assets/S1/CSR_OCAT.png)

---

