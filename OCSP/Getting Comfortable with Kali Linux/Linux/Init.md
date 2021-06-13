# Init
## System V
- Sys V starts and stops processes sequentially, so let's say if you wanted to start up a service named foo-a, well before foo-b can work, you have to make sure foo-a is already running. 
- Sys V does that with scripts, these scripts start and stop services for us, we can write our own scripts or most of the time use the ones that are already built in the operating system and are used to load essential services.
- The pros of using this implementation of init, is that it's relatively easy to solve dependencies, since you know foo-a comes before foo-b, however performance isn't great because usually one thing is starting or stopping at a time.
- When using Sys V, the state of the machine is defined by runlevels which are set from 0 to 6. These different modes will vary depending on the distribution, but most of the time will look like the following:

	- 0: Shutdown
	- 1: Single User Mode
	- 2: Multiuser mode without networking
	- 3: Multiuser mode with networking
	- 4: Unused
	- 5: Multiuser mode with networking and GUI
	- 6: Reboot

-  When your system starts up, it looks to see what runlevel you are in and executes scripts located inside that runlevel configuration. 
-  The scripts are located in /etc/rc.d/rc[runlevel number].d/ or /etc/init.d. Scripts that start with S(start) or K(kill) will run on startup and shutdown, respectively. 
-  The numbers next to these characters are the sequence they run in.
-  We see when we switch to runlevel 0 or shutdown mode, our machine will try to run a script to kill the updates services and then openvpn.
-   To find out what runlevel your machine is booting into, you can see the default runlevel in the /etc/inittab file. You can also change your default runlevel in this file as well.

### System V Service
There are many command line tools you can use to manage Sys V services.
- List services
	``` bash
	$ service --status-all
	```
	
- Start a service
	``` bash
	$ sudo service networking start
	```

- Stop a service
	``` bash
	$ sudo service networking stop
	```

- Restart a service
	``` bash
	$ sudo service networking restart
	```

These commands aren't specific to Sys V init systems, you can use these commands to manage Upstart services as well. Since Linux is trying to move away from the more traditional Sys V init scripts, there are still things in place to help that transition.

## Upstart
- Upstart was developed by Canonical, so it was the init implementation on Ubuntu for a while, however on modern Ubuntu installations systemd is now used. 
- Upstart was created to improve upon the issues with Sys V, such as the strict startup processes, blocking of tasks, etc. Upstart's event and job driven model allow it to respond to events as they happen.
- To find out if you are using Upstart, if you have a /usr/share/upstart directory that's a pretty good indicator.
- Jobs are the actions that Upstart performs and events are messages that are received from other processes to trigger jobs
- Inside these job configurations, it'll include information on how to start jobs and when to start jobs.
- There are many ways to write the configuration file and you'll discover that when you look at the different job configurations available.

The way that Upstart works is that:

- First, it loads up the job configurations from /etc/init
- Once a startup event occurs, it will run jobs triggered by that event.
- These jobs will make new events and then those events will trigger more jobs
- Upstart continues to do this until it completes all the necessary jobs

### Upstart Jobs
- Upstart can trigger a lot of events and jobs to run, unfortunately there is no easy way to see where an event or job originated, so you'll have to poke around the job configurations in /etc/init.
-  Most of the time, you won't ever need to look at the Upstart job configuration files, but you will want to control some specific jobs more easily.

There are a lot of useful commands you can use in an Upstart system.
- View jobs
	``` bash
	$ sudo initctl list
	```

- View specific job
	``` bash
	$ sudo initctl status <job name>
	```
- Manually start/stop/restart a job
	``` bash
	$ sudo initctl start/restart/stop networking
	```

- Manually emit an event
	``` bash
	$ sudo initctl emit some_event
	```

## Systemd 
- Systemd is slowly becoming the emerging standard for init. If you have a /usr/lib/systemd directory, you're most likely using systemd.

- Systemd uses goals to get your system up and running. Basically you have a target that you want to achieve and this target also has dependencies that we need to achieve.
-  Systemd is extremely flexible and robust, it does not follow a strict sequence to get processes started. 
  
Here's what happens during the typical systemd boot:

1. First, systemd loads its configuration files, usually located in /etc/systemd/system or /usr/lib/systemd/system
1. Then it determines its boot goal, which is usually default.target
1. Systemd figures out the dependencies of the boot target and activates them
 
 Similar to Sys V runlevels, systemd boots into different targets:

- poweroff.target - shutdown system
- rescue.target - single user mode
- multi-user.target - multiuser with networking
- graphical.target - multiuser with networking and GUI
- reboot.target - restart

The default boot goal of default.target usually points to the graphical.target.

The main object that systemd works with are known as units. Systemd doesn't just stop and start services, it can mount filesystems, monitor your network sockets, etc and because of that robustness it has different types of units it operates. The most common units are:

- Service units - these are the services we've been starting and stopping, these unit files end in .service
- Mount units - These mount filesystems, these unit files end in .mount
- Target units - These group together other units, the files end in .target

Now, let's get into some commands you can use with systemd units:

- List units
	``` bash
	$ systemctl list-units
	```
- View status of  units
	``` bash
	$ systemctl status networking.service
	```
- Start/Stop/Restart a Service
	``` bash
	$ sudo systemctl start/restart/stop networking.service
	```
- Enable/Disable units
``` bash
	$ sudo systemctl enable/disable networking.service
	```