# FileSystem
## Filesystem Hierarchy
Filesystems can vary with how they are structured, but for the most part they should conform to the Filesystem Hierarchy Standard.

Go ahead and do an ls -l / to see the directories listed under the root directory, yours may look different than mine, but the directories should for the most part look like the following:

-	_**/**_ - The root directory of the entire filesystem hierarchy, everything is nestled under this directory.
-	_**/bin**_ - Essential ready-to-run programs (binaries), includes the most basic commands such as ls and cp.
-	_**/boot**_ - Contains kernel boot loader files.
-	_**/dev**_ - Device files.
-	***/etc*** - Core system configuration directory, should hold only configuration files and not any binaries.
-	***/home*** - Personal directories for users, holds your documents, files, settings, etc.
-	***/lib*** - Holds library files that binaries can use.
-	***/media*** - Used as an attachment point for removable media like USB drives.
-	***/mnt*** - Temporarily mounted filesystems.
-	***/opt*** - Optional application software packages.
-	***/proc*** - Information about currently running processes.
-	***/root*** - The root user's home directory.
-	***/run*** - Information about the running system since the last boot.
-	***/sbin*** - Contains essential system binaries, usually can only be ran by root.
-	***/srv*** - Site-specific data which are served by the system.
-	***/tmp*** - Storage for temporary files
-	***/usr*** - This is unfortunately named, most often it does not contain user files in the sense of a home folder. This is meant for user installed software and utilities, however that is not to say you can't add personal directories in there. Inside this directory are sub-directories for /usr/bin, /usr/local, etc.
-	***/var*** - Variable directory, it's used for system logging, user tracking, caches, etc. Basically anything that is subject to change all the time.

## Filesystem Types
Different filesystems have different ways of organizing their data and we'll go into detail about what types of filesystems there are. Since there are so many different implementations available, applications need a way to deal with the different operations. So there is something called the Virtual File System (VFS) abstraction layer. It is a layer between applications and the different filesystem types, so no matter what filesystem you have, your applications will be able to work with it.

### Journaling

Journaling comes by default on most filesystem types, but just in case it doesn't, you should know what it does. Let's say you're copying a large file and all of a sudden you lose power. Well if you are on a non-journaled filesystem, the file would end up corrupted and your filesystem would be inconsistent and then when you boot back up, your system would perform a filesystem check to make sure everything is ok. However, the repairs could take awhile depending on how large your filesystem was.

Now if you were on a journaled system, before your machine even begins to copy the file, it will write what you're going to be doing in a log file (journal). Now when you actually copy the file, once it completes, the journal marks that task as complete. The filesystem is always in a consistent state because of this, so it will know exactly where you left off if your machine shutdown suddenly. This also decreases the boot time because instead of checking the entire filesystem it just looks at your journal.

### Common Desktop Filesystem Types

-	ext4 - This is the most current version of the native Linux filesystems. It is compatible with the older ext2 and ext3 versions. It supports disk volumes up to 1 exabyte and file sizes up to 16 terabytes and much more. It is the standard choice for Linux filesystems.
-	Btrfs - "Better or Butter FS" it is a new filesystem for Linux that comes with snapshots, incremental backups, performance increase and much more. It is widely available, but not quite stable and compatible yet.
-	XFS - High performance journaling file system, great for a system with large files such as a media server.
-	NTFS and FAT - Windows filesystems
-	HFS+ - Macintosh filesystem

The df command reports file system disk space usage and other details about your disk, we will talk more about this tool later.

``` bash
pete@icebox:~$ df -T

Filesystem     Type     1K-blocks    Used Available Use% Mounted on

/dev/sda1      ext4       6461592 2402708   3707604  40% /

udev           devtmpfs    501356       4    501352   1% /dev

tmpfs          tmpfs       102544    1068    101476   2% /run

/dev/sda6      xfs       13752320  460112  13292208   4% /homel

```

## Anatomy of a Disk
