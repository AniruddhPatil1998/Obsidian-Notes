# Linux
## [[Boot Process]]
The Linux boot process can be broken down in 4 simple stages:

1. BIOS
	The BIOS (stands for "Basic Input/Output System") initializes the hardware and makes sure with a Power-on self test (POST) that all the hardware is good to go. The main job of the BIOS is to load up the bootloader.
2. Bootloader
	The bootloader loads the kernel into memory and then starts the kernel with a set of kernel parameters. One of the most common bootloaders is GRUB, which is a universal Linux standard.
3. [[Kernel]]
	When the kernel is loaded, it immediately initializes devices and memory. The main job of the kernel is to load up the init process.
4. [[Init]]
	Remember the init process is the first process that gets started, init starts and stops essential service process on the system. There are three major implementations of init in Linux distributions. The main purpose of init is to start and stop essential processes on the system. There are three major implementations of init in Linux, System V
	
### [[Kernel]] 
-  The kernel is the core of the operating system.
-   The Linux operating system can be organized into three different levels of abstraction.
	- The most basic level is hardware, this includes our CPU, memory, hard disks, networking ports, etc. The physical layer that actually computes what our machine is doing.

	- The next level is the kernel, which handles process and memory management, device communication, system calls, sets up our filesystem, etc. The kernel's job is to talk to the hardware to make sure it does what we want our processes to do.

	- And the level that you are familiar with is the user space, the user space includes the shell, the programs that you run, the graphics, etc. 


## [[The FileSystem]]
A filesystem is the methods and data structures that an operating system uses to keep track of files on a disk or partition; that is, the way the files are organized on the disk. The word is also used to refer to a partition or disk that is used to store the files or the type of the filesystem.

In computing, a file system or filesystem (often abbreviated to fs) controls how data is stored and retrieved. Without a file system, data placed in a storage medium would be one large body of data with no way to tell where one piece of data stops and the next begins. By separating the data into pieces and giving each piece a name, the data is easily isolated and identified. Taking its name from the way paper-based data management system is named, each group of data is called a "file." The structure and logic rules used to manage the groups of data and their names is called a "file system."




