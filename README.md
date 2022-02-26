# Raspberry Pi Preempt-RT Patch
Real-time patch for Raspberry Pi with Preempt-rt kernel

+ It contains additional support as an option to use W5500 Ethernet network driver for users who need extra ethernet port on raspberry pi.
+ You can find out much more simplified method using pre-built kernel on the [link](https://github.com/shkwon98/RPi_PreemptRT_PreBuilt)

---

### HARDWARE:
+ Raspberry Pi 4
+ (Option) WIZnet W5500 Ethernet chip with SPI interface


### SOFTWARE:
+ Raspbian (buster) [Download](http://downloads.raspberrypi.org/raspbian/images/raspbian-2020-02-14/2020-02-13-raspbian-buster.zip)
+ Preempt-RT real time kernel (4.19.y-rt) [Download](https://github.com/raspberrypi/linux/tree/rpi-4.19.y-rt)


### BUILD:

#### [Linux PC]

+ Create directory

      ~$ mkdir rpi-kernel
      ~$ cd rpi-kernel
      ~/rpi-kernel$ mkdir rt-kernel

+ Clone repositories

      ~/rpi-kernel$ git clone -b rpi-4.19.y-rt https://github.com/raspberrypi/linux.git
      ~/rpi-kernel$ git clone https://github.com/raspberrypi/tools.git

+ Environment variable setting

      ~/rpi-kernel$ export ARCH=arm
      ~/rpi-kernel$ export CROSS_COMPILE=~/rpi-kernel/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-
      ~/rpi-kernel$ export INSTALL_MOD_PATH=~/rpi-kernel/rt-kernel
      ~/rpi-kernel$ export INSTALL_DTBS_PATH=~/rpi-kernel/rt-kernel

+ Setting for Raspberry pi 4

      ~/rpi-kernel$ export KERNEL=kernel7l
      ~/rpi-kernel$ cd ~/rpi-kernel/linux/
      ~/rpi-kernel/linux$ make bcm2711_defconfig

+ Configurate Kernel Compile options

      ~/rpi-kernel/linux$ sudo apt-get install libncurses-dev libssl-dev flex bison
      ~/rpi-kernel/linux$ make menuconfig
      [Kernel hacking] → [Debug preemptible kernel] Off
      (Option) [Device Drivers] → [Network device support] → [Ethernet driver support] → [WIZnet W5100 Ethernet support], [WIZnetW5100/W5200/W5500 Ethernet support for SPI mode] On

+ Kernel Compile

      ~/rpi-kernel/linux$ make -j4 zImage modules dtbs
      ~/rpi-kernel/linux$ make –j4 modules_install dtbs_install

+ Create .img Kernel file

      ~/rpi-kernel/linux$ mkdir $INSTALL_MOD_PATH/boot
      ~/rpi-kernel/linux$ ./scripts/mkknlimg ./arch/arm/boot/zImage $INSTALL_MOD_PATH/boot/$KERNEL.img

+ Replace the Kernel to Raspberry Pi

      ~/rpi-kernel/linux$ cd $INSTALL_MOD_PATH
      ~/rpi-kernel/rt-kernel$ tar czf ../rt-kernel.tgz *
      ~/rpi-kernel/rt-kernel$ cd ..
      ~/rpi-kernel$ scp rt-kernel.tgz <Username>@<ipaddress>:/tmp
      ( <Username>: username of raspberry pi, <ipaddress>: IP address of raspberry pi )


    
#### [Raspberry Pi]

+ Installing the Kernel

      ~$ cd /tmp
      /tmp$ tar xzf rt-kernel.tgz
      /tmp$ cd boot
      /tmp/boot$ sudo cp -rd * /boot/
      /tmp/boot$ cd ../lib
      /tmp/lib$ sudo cp -rd * /lib/
      /tmp/lib$ cd ../overlays
      /tmp/overlays$ sudo cp -d * /boot/overlays
      /tmp/overlays$ cd ..
      /tmp$ sudo cp -d bcm* /boot/

+ Boot configutarion

      /tmp$ sudo nano /boot/config.txt
      Type "kernel=kernel7l.img" in the first line
      (Option) Type "dtoverlay=w5500  in the last line

+ Reboot and check the patched kernel

      /tmp$ sudo reboot
      ~$ uname -r


### BENCHMARK:
+ [Cyclictest benchmark](https://github.com/shkwon98/Cyclictest)
