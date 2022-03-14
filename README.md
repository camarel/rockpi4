# rockpi4

Notes on how to install Debian on Rock Pi 4.

## Installation

The installer will not prepare the disk / card with a boot loader. Before starting the installation you need to prepare the SD card with a boot loader.

You will need a second SD card for the installer.


### Prepare the destination card

* [Dowload boot loader](https://deb.debian.org/debian/dists/stable/main/installer-arm64/current/images/u-boot/rock64-rk3328.img.gz)


Erase existing partition table on the SD card:

```
sudo dd if=/dev/zero of=/dev/mmcblk0 bs=1M count=20
```

Install boot loader:

```
gunzip rock-pi-4-rk3399.img.gz
sudo dd if=rock-pi-4-rk3399.img  of=/dev/mmcblk0
```

This will create a first partition with no type and a certain offset. During the installation proceess, you can use and format this partition to ext2 for `/boot`

Create a second partition to host the root file system for the installation.


### Create the installer card

There is no ready made installer image for the Rock Pi 4. You need to download two different files, and create the iso yourself. 

> Debian provides installer images in the form of a device-specific
part (containing the partition table and the system firmware) and a
device-independent part (containing the actual installer), which can be
unpacked and concatenated together to build a complete installer image.
> 
> The device-specific part is named firmware.<board_name>.img.gz
and the device-independent part is named partition.img.gz.

Download the two parts:

* [Partition table and firmware](https://deb.debian.org/debian/dists/stable/main/installer-arm64/current/images/netboot/SD-card-images/firmware.rock-pi-4-rk3399.img.gz)
* [Installer artition](https://deb.debian.org/debian/dists/stable/main/installer-arm64/current/images/netboot/SD-card-images/partition.img.gz)

Create the installer image:

```
zcat firmware.rock-pi-4-rk3399.img.gz partition.img.gz > installer.img
```

Write the installer on a second SD card:

```
sudo dd if=installer.img  of=/dev/mmcblk0
```

### Installing

Put the last created SD card into the Rock Pi 4. This should boot and start the Debian installer. Once the installer runs, like after choosing location you could switch the cards and put the card with the boot loader.

During partitioning, choose the first partition for `/boot` (ext-2) and the second partition for `/` (ext-4). 


## Wifi firmware

Due to copyright problems the firmware for the wifi / bluetooth chip are not included in Debian. You will need to install them yourself. There are diferent places where you can find them:

* https://github.com/LibreELEC/brcmfmac_sdio-firmware
* https://github.com/pyavitz/firmware/tree/radxa
* https://github.com/armbian/firmware/tree/master/brcm


I tried the ones from LibreELEC:

```
mkdir tmp
cd tmp
git clone https://github.com/LibreELEC/brcmfmac_sdio-firmware.git
sudo mkdir /lib/firmware/brcm
sudo cp brcmfmac43456-sdio.* /lib/firmware/brcm/
```

After reboot you can check, if the firmware is loaded:

```
dmesg | grep brcm
```

Good luck!
