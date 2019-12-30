MR3020 as a webcam streamer based on different documentation.


Prepare the image
===============

We will download our imagebuilder and prepare a folder to make our custom image:

```
wget http://downloads.openwrt.org/releases/17.01.5/targets/ar71xx/generic/lede-imagebuilder-17.01.5-ar71xx-generic.Linux-x86_64.tar.xz
wget http://downloads.openwrt.org/releases/17.01.5/targets/ar71xx/generic/sha256sums
sha256sum --ignore-missing -c sha256sums
tar xvf lede-imagebuilder-17.01.5-ar71xx-generic.Linux-x86_64.tar.xz
cd lede-imagebuilder-17.01.5-ar71xx-generic.Linux-x86_64
```

Make sure the sha256 signatures are matching.


Customize our target
====================

Default packages are coming with the image.
But we must append more packages to it.

We must to add support and other for:
- ssh server: dropbear
- video support (uvc camera only): kmod-usb-core kmod-usb2 kmod-video-core kmod-video-uvc
- video application: mjpg-streamer motion
- 3G support: kmod-usb-serial kmod-usb-serial-option kmod-usb-serial-wwan usb-modeswitch

Unfortunately, there is not enough memory space on the MR3020 to install those, we could do with an external rootfs like on a usb dongle:
- gps support: gpsd
- ntp tool set for timing: ntp-utils


We run the following command to produce our image:

```
make image PROFILE="tl-mr3020-v1" PACKAGES="kmod-usb-core kmod-usb2 kmod-usb-ledtrig-usbport dropbear kmod-usb-core kmod-usb2 kmod-video-core kmod-video-uvc mjpg-streamer motion kmod-usb-serial kmod-usb-serial-option kmod-usb-serial-wwan usb-modeswitch" EXTRA_IMAGE_NAME="wifi-cam" FILES="../rootfs/"
```

We then have an image with our configuration:
```
file bin/targets/ar71xx/generic/*.bin 
```


Copy to the system:
===================

In "bin/targets/ar71xx/generic" either use the:
- lede-17.01.5-wifi-cam-ar71xx-generic-tl-mr3020-v1-squashfs-sysupgrade.bin for upgrading a with openwrt already on it.
- lede-17.01.5-wifi-cam-ar71xx-generic-tl-mr3020-v1-squashfs-factory.bin for flashing a new system (with manufacturer firmware).

To perform an upgrade:

```
scp bin/targets/ar71xx/generic/lede-17.01.5-wifi-cam-ar71xx-generic-tl-mr3020-v1-squashfs-sysupgrade.bin root@192.168.1.1:/tmp
sysupgrade lede-17.01.5-wifi-cam-ar71xx-generic-tl-mr3020-v1-squashfs-sysupgrade.bin
```

Reboot the device and connect to it.

Accessing the system
====================

Following the IT address the stream be accessed with:

```
cvlc http://10.0.99.149?action=stream
```

Login and password are both 'openwrt'.

