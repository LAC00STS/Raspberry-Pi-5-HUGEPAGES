# Raspberry-Pi-5-HUGEPAGES
Enable HUGEPAGE support on the Raspberry Pi 5!


HUGEPAGE support is not enabled by default on Raspberry Pi.  To enable it you must recompile the Linux kernel.  I have provided a prebuilt kernel with HUGEPAGE support in this repo.  If you wish to build the kernel yourself follow these instructions.  This was performed on a Raspberry Pi 5 8GB.  

## Install dependencies
`sudo apt update`  
`sudo apt install -y git build-essential bc bison kmod cpio flex libncurses5-dev libelf-dev libssl-dev`

## Clone the Raspberry Pi Linux Kernel Repository
`git clone --depth=1 https://github.com/raspberrypi/linux`  
`cd linux`

## Configure Kernel
`KERNEL=kernel_2712`  
`make bcm2712_defconfig`   

## Enable hugepage support in the kernel configuration - One line at a time
```bash
sed -i 's/# CONFIG_HUGETLBFS.*/CONFIG_HUGETLBFS=y/' .config
sed -i 's/# CONFIG_TRANSPARENT_HUGEPAGE.*/CONFIG_TRANSPARENT_HUGEPAGE=y/' .config
sed -i 's/# CONFIG_HUGETLB_PAGE.*/CONFIG_HUGETLB_PAGE=y/' .config 
sed -i 's/# CONFIG_ARCH_HAS_HUGEPD.*/CONFIG_ARCH_HAS_HUGEPD=y/' .config
sed -i 's/# CONFIG_HUGETLB_PAGE_SIZE_VARIABLE.*/CONFIG_HUGETLB_PAGE_SIZE_VARIABLE=y/' .config
sed -i 's/# CONFIG_ARCH_WANT_GENERAL_HUGETLB.*/CONFIG_ARCH_WANT_GENERAL_HUGETLB=y/' .config
sed -i 's/# CONFIG_SYSFS.*/CONFIG_SYSFS=y/' .config
sed -i 's/# CONFIG_CHECKPOINT_RESTORE.*/CONFIG_CHECKPOINT_RESTORE=y/' .config
sed -i 's/# CONFIG_HIGH_RES_TIMERS.*/CONFIG_HIGH_RES_TIMERS=y/' .config
```

## Kernel Config 2  
Additional pop up messages will be prompted to you requiring a yes or no answer.   Select no [by typing n] for each prompt.

## Build the kernel

`make -j4 Image.gz modules dtbs`  
`sudo make modules_install`

## Install the kernel
```bash
sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/firmware/
sudo cp arch/arm64/boot/dts/overlays/*.dtb* /boot/firmware/overlays/
sudo cp arch/arm64/boot/dts/overlays/README /boot/firmware/overlays/
sudo cp arch/arm64/boot/Image.gz /boot/firmware/$KERNEL.img
```

## Reboot and kernel will be installed and active

You can verify that HUGEPAGE support is enabled by running the command
`grep -i huge /proc/meminfo`  
Displays the number of HUGEPAGES currently allocated
`grep HugePages_Total /proc/meminfo`  

You can further configure HUGEPAGE allocation and size as you would on any other Linux system.

# HUGEPAGE Allocation - 1 GB Pages - For XMRIG

Add to the end of line of file - Sets 3 - 1 GB pages at boot
`sudo nano /boot/cmdline.txt`  
`default_hugepagesz=2M hugepagesz=1G hugepages=3`




