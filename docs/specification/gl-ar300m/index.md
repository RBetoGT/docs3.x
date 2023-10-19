#  GL-AR300M Series (Shadow)

Best Mini Router Ever

AR300M is the our best mini router. It is small and powerful.

It has several sub-models:

| Sub-model    | NOR flash | NAND flash | Antenna  | 5G Wi-Fi | USB power control |
| ------------ | --------- | ---------- | -------- | ------- | ----------------- |
| AR300M       | 16MB      | 128MB      | Internal | No      | Yes               |
| AR300M-Ext   | 16MB      | 128MB      | External | No      | Yes               |
| AR300M16     | 16MB      | No         | Internal | No      | Yes               |
| AR300M16-Ext | 16MB      | No         | External | No      | Yes               |
| AR300M-Lite  | 16MB      | No         | Internal | No      | No                |
| AR300MD*     | 16MB      | 128MB      | Internal | Yes     | Yes               |

*AR300MD 5G Wi-Fi is suggested only to work at monitoring mode because of power consumption. 



##  Hardware Specification

|                         Model | GL-AR300M Series                                             |
| ----------------------------: | :----------------------------------------------------------- |
|                           CPU | QCA9531 650MHz                                               |
|                        Memory | DDRII 128MB                                                  |
|                       Storage | 16MB NOR, 128 MB NAND (Optional)                             |
|                    Interfaces | 1 WAN<br>1 LAN<br>1 USB2.0<br>1 Micro USB (power)<br>1 Reset Button<br>PCIe<br>UART |
|                     Frequency | 2.4GHz                                                       |
|             Transmission Rate | 300Mbps                                                      |
|                 Max. Tx Power | 20dBm                                                        |
|                      Protocol | IEEE 802.11b/g/n                                             |
| External Drive Format Support | FAT32/NTFS/EXT4/EXT3/EXT2                                    |
|                Webcam Support | MJPEG, YUV (not support from firmware v2.27)                 |
|                  DIY Features | UART, GPIO, PCIe, 3.3V & 5V power port                       |
|      External Antenna Support | Yes (optional)                                               |
|            PoE Module Support | No                                                           |
|                   Power Input | 5V/2A                                                        |
|             Power Consumption | <2W                                                          |
|             Dimension, Weight | 58mmX58mmX25mm, 39g                                          |
|                  PCIe headers | Yes                                                          |



## PCB Pinout

<div class="gl-lightbox" itemscope itemtype="http://schema.org/ImageGallery">
  <figure itemprop="associatedMedia" itemscope itemtype="http://schema.org/ImageObject">
    <a href="https://static.gl-inet.com/docs/router/en/2/hardware/ar300m/src/GL-AR300M-PINOUT-1.jpg" itemprop="contentUrl" data-size="2339x1654">
      <img src="https://static.gl-inet.com/docs/router/en/2/hardware/ar300m/src/GL-AR300M-PINOUT-1.jpg" itemprop="thumbnail" alt="gl-ar300m pcb pinout" loading="lazy" />
    </a>
  </figure>
</div>

**Note: I2C is not working in some early version of the router.**

By using 128MB NAND flash, space is not a problem and the writing speed is improved greatly. You can compile a firmware with tons of features and writing data is almost instant.

By adding PCIe interface, a 5G 802.11a/ac addon module can be connected and you can enjoy the Wi-Fi speed on a mini router.

## Performance

By adding a fast NAND flash, you can feel how the performance is improved compared to a NOR flash router.

The performance comparision between AR300M (NAND) with AR150 is below:

![performance comparison gl-ar300m vs gl-ar150](https://static.gl-inet.com/docs/router/en/2/hardware/ar300m/src/ar300m-performance.jpg){class="glboxshadow"}

It only takes 6 seconds to upload and process 250 ovpn files. When installing packages to the router, it takes no more than 20% for the time spent than using NOR flash, including the time to download resource from the Internet. So everything just happends in seconds.

## Building Firmware

The router has two flash so we have two firmwares. One firmware is installed on the NOR flash and one is installed on the NAND flash. The two firmware is independent from each other. The router will always try to boot from the NAND flash. If it fails for 3 times it will boot from the NOR flash.

By using two firmwares, businesses can have dual boot options to ensure the system can backup, upgrade and boot without problems.

### Building the generic firmware for NOR flash

The simple instructions of building the firmware can be found here: https://github.com/gl-inet/lede-17.01
You need to have a Linux machine. In this example we use Ubuntu.

```
$ sudo apt-get update
$ sudo apt-get install subversion build-essential git-core libncurses5-dev zlib1g-dev gawk flex quilt libssl-dev xsltproc libxml-parser-perl mercurial bzr ecj cvs unzip git wget
$ git clone https://github.com/domino-team/openwrt-cc.git
$ cd openwrt-cc
$ ./scripts/feeds update -a
$ ./scripts/feeds install -a
$ make menuconfig
  ..choose your compile target and packages
$ make

```

If you want to build the firmware for NOR flash, choose `GL-AR300M` as the target.

![AR300M generic](https://static.gl-inet.com/docs/router/en/2/hardware/ar300m/src/meunconfig-ar300m.jpg){class="glboxshadow"}

### Build the NAND firmware for NAND flash

If you want to build the firmware for nand flash, first choose subtarget `Generic devices with NAND flash` then choose `GL-AR300M NAND` as the target.

![AR300M NAND](https://static.gl-inet.com/docs/router/en/2/hardware/ar300m/src/meunconfig-ar300m-nand.jpg){class="glboxshadow"}

The make process will takes around 1 hour for the first time because it needs to download a lot of data from t he Internet. After compiling finished, you will find the following firmware files in `bin/ar71xx/`

* **openwrt-ar71xx-generic-gl-ar300m-squashfs-sysupgrade.bin**, for NOR flash
* **openwrt-ar71xx-nand-gl-ar300m-squashfs-sysupgrade.tar**, for NAND flash firmware upgrade inside of a working Openwrt firmware
* **openwrt-ar71xx-nand-gl-ar300m-ubi.img**, for initial NAND flash firmware build, this is used when upgrading using uboot.

**Note**

There is two file for NAND firmware. The `xxx-ubi.img` is used for first time writing to the NAND flash. If you are using uboot to upgrade the firmware, please use this one. The `xxx-sysupgrade.tar` is used for upgrading from openwrt. So if you use our stock firmware or already using an openwrt firmware, choose this one.

## Using Firmware

The firmware has exactly the same UI as our other mini routers. To determine you are using NOR flash or NAND flash, please check the available space from the `APPLICATIONS -> Plug-ins` page.

### Control which firmware you are booting into

There are two methods to control which firmware you can boot.

#### Method 1: set boot count

The router will always try to boot into the NAND firmware. But if it fails for 3 times it will try to boot into the NOR firmware. If you want to boot into the NOR firmware, you can tell the router that it failed to boot for 3 times already by setting the uboot env. To do so, you can use the following command in a SSH terminal.

```
$ fw_printenv
$ fw_setenv bootcount 3
$ reboot
```

**You need to do this each time** if you want to boot into NOR firmware, no matter you which firmware you are using. Otherwise after the firmware boot, it will reset `bootcount` variable and next boot will always try to boot the NAND firmware.

#### Method 2: using the switch

**This is only valid in the new uboot (from Mar 2017).**

First ssh to the router and set uboot env `boot_dev`

```
$ fw_printenv
$ fw_setenv boot_dev on
$ reboot
```

Then when the router boot, the router will choose which firmware to boot using the hardware switch.

1. Left side (near the reset button) --> NAND flash
2. Right side  --> NOR flash

To disable using the switch to choose firmware function, clear `boot_dev` variable.

```
$ fw_printenv
$ fw_setenv boot_dev
$ reboot
```

## Debrick

If you brick your router and it cannot boot, you can try to save it using uboot.

### Using uboot web UI

Follow [this guide](../../tutorials/debrick.md) for a general instruction to get access of uboot web UI.

Here is a video guide for entering AR300M uboot failsafe.

<iframe width="480" height="270" src="https://www.youtube.com/embed/-x0RllKkzIc" frameborder="0" allowfullscreen></iframe>

In some early version of AR300M, the uboot boot very slowly. So be patient when you press and hold the reset button. The LEDs will start to flash around 5 seconds.

There is a bug in AR300M uboot and in some computers the connection will always reset when you upload firmware. Check [this post](https://forum.gl-inet.com/t/bricked-ar300m-how-to-restore/1659){target="_blank"} to find the discussion. You can either try chaning the MTU or just try another computer.

**Note**: If you have an older version of uboot, the webUI will only be able to flash NAND flash. This is fixed in newer versions.

**Note**: When flash NAND firmware, only use the firmware with `.img` in the name, not the one with `.rar` in the name.

The following figure is the web UI of the `new` uboot.

![AR300M uboot web ui](https://static.gl-inet.com/docs/router/en/3/specification/gl-ar300m/gl-ar300m_uboot_2021.jpg){class="glboxshadow"}

### Using uboot console

If you want to use uboot console, you need to open the case and solder the serial connectors.

Then you need to set up a tftp server.

in uboot console, the command to flash NOR flash is `run lf` to flash NAND firmware is `run nlf`
