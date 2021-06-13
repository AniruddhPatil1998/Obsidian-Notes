# Kernel
`Why do we have different abstraction layers for user space and kernel? Why can't you combine both powers into one layer?`

They both operate in different modes, the kernel operates in kernel mode and the user space operates in user mode.

- In kernel mode, the kernel has complete access to the hardware, it controls everything. 
- In user space mode, there is a very small amount of safe memory and CPU that you are allowed to access.
-  Basically, when we want to do anything that involves hardware, reading data from our disks, writing data to our disks, controlling our network, etc, it is all done in kernel mode. 

## Privilege Levels
Above different modes are called privilege levels (aptly named for the levels of privilege you get) and are often described as protection rings.
- the innermost ring corresponds to the highest privilege level. 
- There are two main levels or modes in an x86 computer architecture.
-  Ring #3 is the privilege that user mode applications run in, Ring #0 is the privilege that the kernel runs in. 
-  Ring #0 can execute any system instruction and is given full trust.

` So now that we know how those privilege levels work, how are we able to write anything to our hardware? Won't we always be in a different mode than the kernel?`
The answer is with system calls, system calls allow us to perform a privileged instruction in kernel mode and then switch back to user mode.

## System Calls
- System calls (syscall) provide user space processes a way to request the kernel to do something for us.
- The kernel makes certain services available through the system call API. These services allow us to read or write to a file, modify memory usage, modify our network, etc. 
- The amount of services are fixed, so you can't be adding system calls nilly willy, your system already has a table of what system calls exist and each system call has a unique ID.

### Process
1. when you call a program like ls, the code inside this program contains a system call wrapper
1. Inside this wrapper it invokes the system call which will execute a trap
1. This trap then gets caught by the system call handler and then references the system call in the system call table.
1. Since you are running the program in non-privilege mode. So now it sees you're trying to make a syscall, it then switches you over to kernel mode, there it does lots of things
1. most importantly it looks up your syscall number, finds it in a table based on the syscall ID and then executes the function you wanted to run.
1. then it  will return back to user mode and your process will receive a return status if it was successful or if it had an error. 

You can actually view the system calls that a process makes with the strace command. The strace command is useful for debugging how a program executed.

``` bash 
$ strace ls
```

## Kernel Installation
You can install multiple kernels on your system, remember in our lesson on the boot process? In our GRUB menu we can choose which kernel to boot to.

To see what kernel version you have on your system, use the following command:

``` bash
$ uname -r
	3.19.0-43-generic
```
The uname command prints system information, the -r command will print out all of the kernel release version.

```You can install the Linux kernel in different ways, you can download the source package and compile from source or you can install it using package management tools and then just reboot into the kernel you installed. Simple right?```

``` bash
$ sudo apt install linux-generic-lts-vivid
```

Kind of, you'll need to also install other linux packages such as the linux-headers, linux-image-generic,Alternatively, if you just want the updated kernel version, just use dist-upgrade, it performs upgrades to all package on your system:

``` bash
$ sudo apt dist-upgrade
```

## Kernel Location
What happens when you install a new kernel? Well it actually adds a couple of files to your system, these files are usually added to the /boot directory.

You will see multiple files for different kernel versions:

- vmlinuz - this is the actual linux kernel
- initrd - as we've discussed before, the initrd is used as a temporary file system, used before loading the kernel
- System.map - symbolic lookup table
- config - kernel configuration settings, if you are compiling your own kernel, you can set which modules can be loaded

If your /boot directory runs out of space, you can always delete old versions of these files or just use a package manager, but be careful when doing maintenance in this directory and don't accidentally delete the kernel you are using.

## Kernel Modules
- The kernel in itself is a monolithic piece of software, when we want to add support for a new type of keyboard, we don't write this code directly into the kernel code.
- kernel modules are pieces of code that can be loaded and unloaded into the kernel on demand. 
- They allow us to extend the functionality of the kernel without actually adding to the core kernel code. We can also add modules and not have to reboot the system (in most cases).

_View a list of currently loaded modules_
``` bash
$ lsmod
```
_Load a module_
``` bash
$ sudo modprobe bluetooth
```
Modprobe loads tries the module from /lib/modules/(kernel version)/kernel/drivers. Kernel modules may also have dependencies, modprobe loads our module dependencies if they are not already loaded.
_Remove a module_
``` bash
$ sudo modprobe -r bluetooth
```
_Load on bootup_
You can also load modules during system boot, instead of temporarily loading them with modprobe (which will be unloaded when you reboot). Just modify the /etc/modprobe.d directory and add a configuration file in it like so:

_Do not load on bootup_
You can also make sure a module does not load on bootup by adding a configuration file like so: