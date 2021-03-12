## Repository info
This repository contains the code and instructions needed to build a bootable Android 10 image for Asus Tinker Board (rk3288). Based on: https://github.com/radxa/manifests

## Current state
You can build kernel, u-boot, system image and update image without errors.
I’m not sure, that update image is correct, because:
- it's much smaller then update.img from 7.1.2 (984 MB vs 2,08 GB)
- SpiImageTools_v1.44 can’t create data.bin file from update.img
- mkupdate output is much different. Compare

creating update.img for Android 7.1.2
```
q@Komarov-A:~/aosp/RKTools/linux/Linux_Pack_Firmware/rockdev$ ./collectImages.sh && ./mkupdate.sh
Done
start to make update.img...
Android Firmware Package Tool v1.62
------ PACKAGE ------
Add file: ./package-file
Add file: ./Image/MiniLoaderAll.bin
Add file: ./Image/parameter.txt
Add file: ./Image/trust.img
Add file: ./Image/uboot.img
Add file: ./Image/misc.img
Add file: ./Image/resource.img
Add file: ./Image/kernel.img
Add file: ./Image/boot.img
Add file: ./Image/recovery.img
Add file: ./Image/system.img
Add file: ./Image/vendor0.img
Add file: ./Image/vendor1.img
Add CRC...
Make firmware OK!
------ OK ------
********RKImageMaker ver 1.63********
Generating new image, please wait...
Writing head info...
Writing boot file...
Writing firmware...
Generating MD5 data...
MD5 data generated successfully!
New image generated successfully!
Making update.img OK.
q@Komarov-A:~/aosp/RKTools/linux/Linux_Pack_Firmware/rockdev$ ls -la Image/
total 2833868
drwxr-xr-x 2 q q       4096 Mar 12 21:38 .
drwxr-xr-x 3 q q       4096 Mar 12 21:38 ..
-rw-r--r-- 1 q q     149838 Mar 12 21:38 MiniLoaderAll.bin
-rw-r--r-- 1 q q    1501720 Mar 12 21:38 boot.img
-rw-r--r-- 1 q q    8546428 Mar 12 21:38 kernel.img
-rwxr-xr-x 1 q q      49152 Mar 12 21:38 misc.img
-rwxr-xr-x 1 q q        772 Mar 12 21:38 parameter.txt
-rw-r--r-- 1 q q    6229368 Mar 12 21:38 recovery.img
-rw-r--r-- 1 q q      87040 Mar 12 21:38 resource.img
-rw-r--r-- 1 q q 2147483648 Mar 12 21:38 system.img
-rw-r--r-- 1 q q    4194304 Mar 12 21:38 trust.img
-rw-r--r-- 1 q q    4194304 Mar 12 21:38 uboot.img
-rw-r--r-- 1 q q 2241884164 Mar 12 21:38 update.img
-rw-r--r-- 1 q q   34684928 Mar 12 21:38 vendor0.img
-rw-r--r-- 1 q q   34750464 Mar 12 21:38 vendor1.img
q@Komarov-A:~/aosp/RKTools/linux/Linux_Pack_Firmware/rockdev$
```

vs

creating update.img for Android 10
```
q@Komarov-A:~/radxa/rockdev$ ./mkupdate_rk3288.sh
start to make update.img...
Android Firmware Package Tool v1.66
------ PACKAGE ------
Add file: ./package-file
Add file: ./Image/MiniLoaderAll.bin
Add file: ./Image/parameter.txt
Add file: ./Image/trust.img
Add file: ./Image/uboot.img
Add file: ./Image/misc.img
Add file: ./Image/boot.img
Add file: ./Image/dtbo.img
Add file: ./Image/vbmeta.img
Add file: ./Image/recovery.img
Add file: ./Image/super.img
Add CRC...
Make firmware OK!
------ OK ------
********RKImageMaker ver 1.66********
Generating new image, please wait...
Writing head info...
Writing boot file...
Writing firmware...
Generating MD5 data...
MD5 data generated successfully!
New image generated successfully!
Making update.img OK.
q@Komarov-A:~/radxa/rockdev$ ls -la Image-rk3288_Android10/
total 2789832
drwxr-xr-x 2 q q       4096 Mar 12 20:03 .
drwxr-xr-x 4 q q       4096 Mar 12 20:03 ..
-rw-r--r-- 1 q q        102 Mar 12 16:49 MiniLoaderAll.bin
-rw-r--r-- 1 q q    9660416 Mar 12 17:14 boot.img
-rw-r--r-- 1 q q       6129 Mar  9 22:52 config.cfg
-rw-r--r-- 1 q q        607 Mar 10 20:54 dtbo.img
-rw-r--r-- 1 q q    8776804 Mar 12 17:09 kernel.img
-rw-r--r-- 1 q q      49152 Mar  9 22:55 misc.img
-rw-r--r-- 1 q q    1048616 Mar 12 17:15 odm.img
-rw-r--r-- 1 q q        638 Mar  9 22:52 parameter.txt
-rw-r--r-- 1 q q      49152 Mar  9 22:55 pcba_small_misc.img
-rw-r--r-- 1 q q      49153 Mar  9 22:55 pcba_whole_misc.img
-rw-r--r-- 1 q q   26445824 Mar 12 17:14 recovery.img
-rw-r--r-- 1 q q     107008 Mar 12 17:09 resource.img
-rw-r--r-- 1 q q  987677064 Mar 12 17:10 super.img
-rw-r--r-- 1 q q  640577660 Mar 12 17:14 system.img
-rw-r--r-- 1 q q    4194304 Mar 12 16:46 trust.img
-rw-r--r-- 1 q q    4194304 Mar 12 16:47 uboot.img
-rw-r--r-- 1 q q 1032237060 Mar 12 20:03 update.img
-rwxr-xr-x 1 q q       4096 Mar  9 22:52 vbmeta.img
-rw-r--r-- 1 q q  141680716 Mar 12 17:15 vendor.img
q@Komarov-A:~/radxa/rockdev$
```

## Build steps for Asus Tinker Board (rk3288)

```
--- install dependencies ---
sudo apt-get update
sudo apt-get install gcc-arm-linux-gnueabihf openjdk-8-jdk python git-core gnupg flex bison gperf build-essential \
    zip curl liblz4-tool zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 \
    lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache \
    libgl1-mesa-dev libxml2-utils xsltproc unzip mtools u-boot-tools \
    htop iotop sysstat iftop pigz bc device-tree-compiler lunzip \
    dosfstools vim-common parted udev libssl-dev python3

--- install the repo launcher ---
mkdir ~/bin
PATH=~/bin:$PATH
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo

--- downloading fork of the radxa source ---
mkdir ~/radxa && cd ~/radxa
repo init -u https://github.com/tinker-android/manifests.git -b tinker-android-10 -m rockchip-q-release.xml
repo sync -d --no-tags -j4

--- set vars ---
cd ~/radxa
source build/envsetup.sh
lunch rk3288_Android10-userdebug

--- build u-boot ---
cd ~/radxa/u-boot
make ARCH=arm tinker-rk3288_defconfig
make -j8 ARCH=arm CROSS_COMPILE=/usr/bin/arm-linux-gnueabihf-
cd ~/radxa/rbin
~/radxa/u-boot/tools/boot_merger ~/radxa/rkbin/RKBOOT/RK3288MINIALL.ini
cd ~/radxa/u-boot
./tools/loaderimage --pack --trustos ~/radxa/rkbin/bin/rk32/rk3288_tee_ta_v2.01.bin trust.img
./tools/loaderimage --pack --uboot u-boot.bin uboot.img

--- build kernel ---
cd ~/radxa/kernel
make ARCH=arm rockchip_defconfig
make -j8 ARCH=arm rk3288-tinker.img

--- build system image ---
cd ~/radxa
make -j8

--- build update image ---
cd ~/radxa
ln -s RKTools/linux/Linux_Pack_Firmware/rockdev/ rockdev
./mkimage.sh
cd rockdev
ln -s Image-rk3288_Android10 Image
./mkupdate_rk3288.sh
```

## EVERYTHING BELOW IS THE ORIGINAL README.md FILE

## Env
```dockerfile
FROM ubuntu:xenial

RUN apt-get update -y && apt-get install -y openjdk-8-jdk python git-core gnupg flex bison gperf build-essential \
    zip curl liblz4-tool zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 \
    lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache \
    libgl1-mesa-dev libxml2-utils xsltproc unzip mtools u-boot-tools \
    htop iotop sysstat iftop pigz bc device-tree-compiler lunzip \
    dosfstools vim-common parted udev libssl-dev python3

RUN curl https://mirrors.tuna.tsinghua.edu.cn/git/git-repo > /usr/local/bin/repo && \
    chmod +x /usr/local/bin/repo && \
    which repo

ENV REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/'
ENV PS1="\[\033[01;37m\]\u@build\[\033[00m\]:\[\033[01;32m\]\w\[\033[00m\]:$ "

RUN apt-get install -y python-pip && pip install pycrypto
RUN apt-get install -y lzop swig
```

## Download Source Code
```bash
$ repo init -u https://github.com/radxa/manifests.git -b rockchip-android-10 -m rockchip-q-release.xml 
$ repo sync -d --no-tags -j4
```

## Build Code Script
ROCKPI 4A/B
```bash
cd ${source-dir}
./build-rockpi-4b.sh
```
Release dir: ${source-dir}/release/ROCKPI-4AB-`date +%F-%H-%M`

ROCKPI 4C
```bash
cd ${source-dir}
./build-rockpi-4c.sh
```
Release dir: ${source-dir}/release/ROCKPI-4C-`date +%F-%H-%M`

## Build Steps
### Build U-Boot
ROCKPI 4A/B
```bash
$ cd u-boot
$ ./make.sh rockpi4b
$ cd -
```
ROCKPI 4C
```bash
$ cd u-boot
$ ./make.sh rockpi4c
$ cd -
```

### Build Kernel
ROCKPI 4A/B
```bash
$ cd kernel
$ make ARCH=arm64 rockchip_defconfig android-10.config rockpi_4b.config
$ make ARCH=arm64 rk3399-rockpi-4b.img -j8
$ cd -
```
ROCKPI 4C
```bash
$ cd kernel
$ make ARCH=arm64 rockchip_defconfig android-10.config rockpi_4c.config
$ make ARCH=arm64 rk3399-rockpi-4c.img -j8
$ cd -
```

### Build AOSP
ROCKPI 4A/B
```bash
$ source build/envsetup.sh
$ lunch RockPi4B-userdebug
$ make -j8
```
ROCKPI 4C
```bash
$ source build/envsetup.sh
$ lunch RockPi4C-userdebug
$ make -j8
```

### Build Update Image
```bash
$ ln -s RKTools/linux/Linux_Pack_Firmware/rockdev/ rockdev
$ ./mkimage.sh
```
ROCKPI 4A/B
```bash
$ cd rockdev
$ ln -s Image-RockPi4B Image
$ ./mkupdate_rk3399.sh
```
ROCKPI 4C
```bash
$ cd rockdev
$ ln -s Image-RockPi4C Image
$ ./mkupdate_rk3399.sh
```

Please burn rockdev/update.img [GUIDE](https://wiki.radxa.com/Rockpi4/install/android-eMMC-rkupdate)
