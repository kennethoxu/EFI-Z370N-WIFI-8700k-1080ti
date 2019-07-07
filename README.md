# Z370N WIFI 8700k 1080ti High Sierra Hackintosh

 Since we're using a 1080ti, we need to get high Sierra as its likely the last OS to support nvidia drivers. Don't use Unibeast or Multibeast, they are garbage.
 
 ![Geek bench](https://i.imgur.com/n8BPGLl.png)

## Specs
- Gigabyte Z370N Wifi
- EVGA 1080Ti SC Black Edition
- Intel Core i7-8700k
- Corsair SF600
- Samsung 960 EVO NVMe
- Corsair LPX 32gb (2x16gb) 3200 MHZ
- Dancase A4 v2
- Noctua NH-L9I
 
## 0) Prerequisites
- 1-3 USB Drives (8+ gb), 3 for more flexibility
- Access to OSX computer to get OS image
- Access to a Windows computer to unfuck your windows image if necessary
- Ability to not need your computer for the next few days
- 40+ GB of unallocated space in your disk

## 1) Create bootable USBs of Windows and Ubuntu
- (Optinal) Create a bootable Windows USB at the [windows 10 download](https://www.microsoft.com/en-us/software-download/windows10ISO) on your windows computer. On other OS's it only presents the option to download the ISO. On windows you can download the tool to create the USB.
- (Optional) Create a bootable Ubuntu USB  on the [ubuntu tutorials site](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0).
- We can use Windows or Ubuntu to manage partitions in case anything gets fucked up. On Ubuntu we can use `gparted`, on windows we can use the `Disk management`.

## 1) Download the High Sierra image
- Downloading High Sierra from the app store doesn't work. Apple no longer gives you the ability to get the full installer to prevent shenanigans like what we're about to do. I was able to download it from a Yosemite system using the [high sierra patcher tool](http://dosdude1.com/highsierra/). Yes it looks sketch but the guy has a [youtube page](https://www.youtube.com/watch?v=tZ6TomIhqcY) and I was also able to confirm the checksums of `installESD.dmg` and `baseSystem.dmg` [here](https://github.com/notpeter/apple-installer-checksums). The downloaded installer should be ~5.3 gb.
 
## 2) Create the OSX Installer USB
- Follow the [vanilla hackintosh guide](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/building-the-usb-installer) to create the USB installer. When you are done, the USB drive should be named `Install macOS High Sierra`.
- Download [clover configurator](https://mackie100projects.altervista.org/download-clover-configurator/), we use this mount the USB EFI and modify config.plist.

## 3) Update your USBs config.plist.	
- Mount your `EFI on Install macOS High Sierra` -> mount partition.
- Copy the EFI from the [USB Installer EFI folder](USB-Installer-EFI) and put it on the mounted USB.
- This doesn't deal with the GPU which is fine for now, you we'll deal with that post install. You can leave your monitors plugged into the GPU. I'm on display port and it worked fine.

## 4) BIOS Settings pre-install
You probably don't need all of these, experiment.
- (IMPORTANT) Save & Exit → Load Optimized Defaults
- (IMPORTANT) Peripherals → USB Configuration → XHCI Hand-off : Enabled
- (IMPORTANT) Chipset → Vt-d : Disabled
- M.I.T. → Advanced Memory Settings  Extreme Memory Profile(X.M.P.) : Profile1
- BIOS → Fast Boot : Disabled
- BIOS → LAN PXE Boot Option ROM : Disabled
- BIOS → Storage Boot Option Control : UEFI
- Peripherals → Trusted Computing → Security Device Support : Disabled
- Peripherals → Network Stack Configuration → Network Stack : Disabled
- Peripherals → USB Configuration → Legacy USB Support : Auto
- Chipset → Wake on LAN Enable : Disabled
- Chipset → IOAPIC 24-119 Entries : Enabled
- Peripherals → Initial Display Output : PCIe 1 Slot
- Chipset → Integrated Graphics : Disabled (Set to Enabled after installing macOS if you want Hardware Encoding, HEVC, etc)

## 5) Install OSX
- Format your unallocated disk space to APFS and install it there using `Disk Utility`. I would start with at least 128gb but its up to you. You can add more later but its annoying.


## 7) Post Installation
Boot your hackintosh from the EFI on your APFS partition. This will be how you boot from now on. You can modify it the same way as we did the USB one, through `clover configurator`. We're gonna modify it below for better usage.
- Generate your SerialNumber and SMUUID using `clover configurator`. Don't share this. You will need to copy it around for the next steps.
- [Follow this to install nvidia drivers](https://hackintosher.com/guides/properly-install-nvidia-drivers-high-sierra-10-13/)
- Fix sleep by copying the `config.plist` from [here](Post-Installed-APFS-EFI/EFI/CLOVER/config.plist). Messing around with darkwake didn't work for me YMMV, I copied the above config.plist and it works.
- The built in WIFI and bluetooth is not supported, buy a [BCM94352Z](https://www.ebay.com/itm/New-BCM94352Z-AC-WIFI-BT-WLAN-CARD-For-LENOVO-N50-70-B50-70-Y40-70-B40-80-Touch/272100347722). Kext's are included to make this work [here](Post-Installed-APFS-EFI/EFI/CLOVER/kexts/Other/)

## 8) WIFI 
Buy and replace stock wifi card with BCM94352Z. Theres 2 screws holding in the module in the motherboard and 1 keeping the enclosure attached. You need to wiggle the enclosure to get it out after removing the inner screw. Follow [the instructions](https://hackintosher.com/forums/thread/enabling-third-party-broadcom-wlan-802-11a-b-g-n-wifi-bluetooth-cards-on-a-hackintosh-bcm94352z-bcm94322.6/) to get the appropriate kexts. Make sure you follow the steps at the bottom for moving kexts to Library/Extensions, else risk stability issues.

## Addenum: Moving disk space from windows to OSX on dual boot drive
Its easier to keep the OS installations separate, however if you have a NVME SSD like me and prefer both OS's on the same ssd for speed reasons, this is how you to do it.
- Shrink partition from windows, leave it as unallocated space, do not format
- Using ubuntu Gparted, move the partition next to OSX partition, if applicable, delete the 16 mb microsoft reserved partition if it cannot be moved
- Grow the osx partition to include the new space:
- `diskutil list`
- `diskutil apfs resizeContainer diskOs2 200g` Replace 200g with total number you want your osx partition to be. Replace disokOs2 with the appropriate partition as referenced from `disktuil list`



