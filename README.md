Dear developers,

Welcome to the Mozilla Firefox OS Contribution Program for Flatfish. For those who wish to modify the uboot, you will learn in this concise guide how to:

* **Download the toolchain and get sources**
* **Download the necessary patches**
* **Build & update the uboot**

The following steps will allow you to compile and update your Flatfish uboot image (Ubuntu 12.04 x64):

1. Export the WORKSPACE
 ```
 export WORKSPACE=`pwd`
 ```

2. Acquire the Allwinner A31 uboot source
 * Visit the website **http://linux-sunxi.org/A31**
 * Find the **["External links"]** and get into **["Allwinner A31 U-boot source code"]** repository 
 * Find the revision tag: **["A31-Android4.2-v3.2"]** and get the snapshot, extract the tarball, move and rename the uboot source folder to **$WORKSPACE/uboot**
 
3. Download Flatfish uboot patch
 ```
 cd $WORKSPACE
 git clone https://github.com/flatfish-fox/flatfish-uboot.git
 cd flatfish-uboot
 git checkout Bug-1026963 -- 0001-Bug-1026963-Flatfish-Need-a-bare-boot-image-supporti.patch
 git checkout Bug-1033619 -- 0001-Bug-1033619-Flatfish-Make-Vendor-ID-in-Fastboot-mode.patch
 ```

4. Download and set the cross compiler path for compiling the uboot
 ```
 cd $WORKSPACE
 wget https://launchpad.net/linaro-toolchain-binaries/trunk/2012.02/+download/gcc-linaro-arm-linux-gnueabi-2012.02-20120222_linux.tar.bz2
 tar jxvf gcc-linaro-arm-linux-gnueabi-2012.02-20120222_linux.tar.bz2
 export PATH=$WORKSPACE/gcc-linaro-arm-linux-gnueabi-2012.02-20120222_linux/bin:$PATH
 ```

5. Patch the uboot source
 ```
 cd $WORKSPACE/uboot
 patch -p1 < $WORKSPACE/flatfish-uboot/0001-Allwinner-A31-u-boot-patches-for-flatfish-tablet.patch
 patch -p1 < $WORKSPACE/flatfish-uboot/0001-Bug-1026963-Flatfish-Need-a-bare-boot-image-supporti.patch
 patch -p1 < $WORKSPACE/flatfish-uboot/0001-Bug-1033619-Flatfish-Make-Vendor-ID-in-Fastboot-mode.patch
 ```

6. Compile the uboot
 ```
 ./build.sh -p sun6i
 ```

7. Update the u-boot.bin
 * Connect your Flatfish tablet device via USB.
 ```
 adb shell mkdir /mnt/bootloader
 adb shell busybox mount -t vfat /dev/block/mmcblk0p2 /mnt/bootloader
 adb shell rm /mnt/bootloader/linux/u-boot.bin
 adb push u-boot.bin /mnt/bootloader/linux
 adb reboot
 ```

["MozillaWiki"]:https://wiki.mozilla.org/FirefoxOS/TCP/Patching
["External links"]:http://linux-sunxi.org/A31#External_links
["Allwinner A31 U-boot source code"]:http://git.rhombus-tech.net/?p=u-boot.git;a=tree;h=refs/heads/allwinner-sunxi-a31;hb=refs/heads/allwinner-sunxi-a31
["A31-Android4.2-v3.2"]:http://git.rhombus-tech.net/?p=u-boot.git;a=commit;h=ccc221931dd5bc90409ca8c176025ddde84fd8d4
