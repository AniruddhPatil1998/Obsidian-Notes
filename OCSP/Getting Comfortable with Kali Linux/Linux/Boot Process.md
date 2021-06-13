# Boot Process
## BIOS
- The first step in the Linux boot process is the BIOS which performs system integrity checks.
-  The BIOS's main goal is to find the system bootloader.
-  So once the BIOS boots up the hard drive, it searches for the boot block to figure out how to boot up the system.
-  Depending on how you partition your disk, it will look to the master boot record (MBR) or GPT.
-  The MBR is located in the first sector of the hard drive, the first 512 bytes.
-  The MBR contains the code to load another program somewhere on the disk, this program in turn actually loads up our bootloader.

## UEFI
- There is another way to boot up your system instead of using BIOS and that is with UEFI (stands for "Unified extensible firmware interface"). 
- The GPT format was intended for use with EFI. You don't necessarily need EFI if you are booting a GPT disk.
-  The first sector of a GPT disk is reserved for a "protective MBR" to make it possible to boot a BIOS-based machine.
-  UEFI stores all the information about startup in an .efi file. This file is stored on a special partition called EFI system partition on the hardware. Inside this partition it will contain the bootloader.
-   UEFI comes with many improvements from the traditional BIOS firmware. 
-   However, since we are using Linux, the majority of us are using BIOS. So all of these lessons will be going along with that pretense.

## BootLoader
 The bootloader's main responsibilities are:
- Booting into an operating system, it can also be used to boot to non-Linux operating systems
- Select a kernel to use
- Specify kernel parameters

So we know that the bootloader's main goal is to load up the kernel, but where does it find the kernel? To find it, we will need to look at our kernel parameters.

- _initrd_   - Specifies the location of initial RAM disk
- _BOOT_IMAGE_ - This is where the kernel image is located 
- _root_ - The location of the root filesystem, the kernel searches inside this location to find init. It is often represented by it's UUID or the device name such as /dev/sda1.
- _ro_ - This parameter is pretty standard, it mounts the fileystem as read-only mode.
- _quiet_ - This is added so that you don't see display messages that are going on in the background during boot.
- _splash_ - This lets the splash screen be shown.

## [[Kernel]]
### Initrd vs Initramfs
- The kernel manages our systems hardware, however not all drivers are available to the kernel during bootup. 
- So we depend on a temporary root filesystem that contains just the essential modules that the kernel needs to get to the rest of the hardware. 
- In older versions of Linux, this job was given to the initrd (initial ram disk). The kernel would mount the initrd, get the necessary bootup drivers, then when it was done loading everything it needed, it would replace the initrd with the actual root filesystem. 
- These days, we have something called the initramfs, this is a temporary root filesystem that is built into the kernel itself to load all the necessary drivers for the real root filesystem, so no more locating the initrd file.

###  Mounting the root filesystem
- Now the kernel has all the modules it needs to create a root device and mount the root partition. 
- The root partition is actually mounted in read-only mode first so that fsck can run safely and check for system integrity.
-  Afterwards it remounts the root filesystem in read-write mode. Then the kernel locates the init program and executes it.

## init
There are actually three major implementations of init in Linux:
### System V init (sysv) 
- This is the traditional init system. It sequentially starts and stops processes, based on startup scripts. 
- The state of the machine is denoted by runlevels, each runlevel starts or stops a machine in a different way.
###  Upstart
- This is the init you'll find on older Ubuntu installations.
-  Upstart uses the idea of jobs and events and works by starting jobs that performs certain actions in response to events.
### Systemd
- This is the new standard for init, it is goal oriented. Basically you have a goal that you want to achieve and systemd tries to satisfy the goal's dependencies to complete the goal.
