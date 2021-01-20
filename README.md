# CM4_LCD_LT070ME05000
Raspberry Pi CM4 bindings for JDI LT070ME05000 1200x1920 IPS display

![CM4Ext Nano with LCD](https://raw.githubusercontent.com/harlab/CM4_LCD_LT070ME05000/main/Documentation/CM4ExtNano_JDI1.jpeg)

# Overview
JDI LT070ME05000 is 7" 1200x1920 4-lane DSI IPS display used in Asus/Google Nexus 7 2013 tablet. To use it, 4-lane DSI interface is required, so it can be used with Raspberry Pi Compute Modules 3 and 4.
Some suppliers have these displays available from ~$US20 shipped.
There is a driver for this panel in a mainline already included, so we only need to compile/install it and make device tree overlay

# Software installation
We need build rpi-5.10.y branch with LT070ME05000 module enabled. This is well documented at Raspberry Pi help pages [here](https://www.raspberrypi.org/documentation/linux/kernel/building.md) and [here](https://www.raspberrypi.org/documentation/linux/kernel/configuring.md)

```
sudo apt install git bc bison flex libssl-dev make
sudo apt install libncurses5-dev
git clone --depth=1 --branch rpi-5.10.y https://github.com/raspberrypi/linux
cd linux
KERNEL=kernel7l
make bcm2711_defconfig
make menuconfig
```
Navigate to Device Drivers/Graphics support/Display panel, check JDI LT070ME05000 panel and save

Now we need to build and install kernel:
```
make -j4 zImage modules dtbs
sudo make modules_install
sudo cp arch/arm/boot/dts/*.dtb /boot/
sudo cp arch/arm/boot/dts/overlays/*.dtb* /boot/overlays/
sudo cp arch/arm/boot/dts/overlays/README /boot/overlays/
sudo cp arch/arm/boot/zImage /boot/$KERNEL.img
```

[Device Tree Source](https://github.com/harlab/CM4_LCD_LT070ME05000/blob/main/dt_overlay/lt070me05000.dts) can be modified to assign another GPIO numbers, then compiled and copied to boot partition:
```
dtc -@ -I dts -O dtb -o lt070me05000.dtbo lt070me05000.dts
sudo cp lt070me05000.dtbo /boot/overlays/
```

Then edit /boot/config.txt to add:
```
lcd_ignore=1
dtoverlay=vc4-kms-v3d
dtoverlay=lt070me05000
```
and reboot

After reboot screen should we working and have portrait orientation. To adjust, use Main Menu/Preferences/Screen configuration utility
