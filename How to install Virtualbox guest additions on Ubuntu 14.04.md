## install xorg
> sudo apt-get install xorg openbox

## 1. Install kernel headers and build tools

Virtualbox guest additions are compiled for the target system, so it needs the necessary kernel headers and related programs. Install the following 2 packages.

> $ sudo apt-get install build-essential module-assistant

Now run

> $ sudo m-a prepare

## 2. Compile virtualbox guest additions

Now click "Devices > Insert guest additions CD image" in the virtualbox window. This will insert the guest additions cd image into the guest OS. On Xubuntu the cd should get mounted automatically inside the /media directory.

On Ubuntu unity, you should see the cd icon on the left panel towards the lower side. Click it to open the VBox guest additions cd in file manager.

Kubuntu would give you a device notification on bottom right and you can click "open with file manager" which will mount the cd and open it up in dolphin.

Check the path of the cd file system inside the file manager (press Ctrl + L). The location should be something similar to this

/media/<username>/VBOXADDITIONS_4.3.10_93012
The shall contain your username on the system.

Mount manually

If it does not mount by itself, then you can manually mount it. Find out the device using blkid and then use the mount command to mount it somewhere in your home directory

# find out the device
$ sudo blkid
/dev/sr0: LABEL="VBOXADDITIONS_4.3.10_93012" TYPE="iso9660" 

# Or use the lsblk command
$ sudo lsblk -o NAME,TYPE,SIZE,LABEL,MOUNTPOINT,MODEL
NAME   TYPE   SIZE LABEL                      MOUNTPOINT MODEL
sda    disk     8G                                       VBOX HARDDISK   
├─sda1 part     6G                            /          
├─sda2 part     1K                                       
└─sda5 part     2G                            [SWAP]     
sr0    rom   61.7M VBOXADDITIONS_4.3.10_93012            CD-ROM
Note down the device name which is "/dev/sr0" here. Next we have to mount this device (cdrom) to access the contents.

# create directory to mount
$ mkdir cdrom

# mount the cd
$ sudo mount /dev/sr0 ~/cdrom/
[sudo] password for silver: 
mount: block device /dev/sr0 is write-protected, mounting read-only

# get inside the mounted directory
$ cd cdrom/
~/cdrom$ ls
32Bit        cert                    VBoxSolarisAdditions.pkg
64Bit        OS2                     VBoxWindowsAdditions-amd64.exe
AUTORUN.INF  runasroot.sh            VBoxWindowsAdditions.exe
autorun.sh   VBoxLinuxAdditions.run  VBoxWindowsAdditions-x86.exe
Start compiling

Navigate to the directory and run the script named VBoxLinuxAdditions.run

/media/silver/VBOXADDITIONS_4.3.10_93012$ ls
32Bit        cert                    VBoxSolarisAdditions.pkg
64Bit        OS2                     VBoxWindowsAdditions-amd64.exe
AUTORUN.INF  runasroot.sh            VBoxWindowsAdditions.exe
autorun.sh   VBoxLinuxAdditions.run  VBoxWindowsAdditions-x86.exe


/media/silver/VBOXADDITIONS_4.3.10_93012$ sudo ./VBoxLinuxAdditions.run 
[sudo] password for silver: 
Verifying archive integrity... All good.
Uncompressing VirtualBox 4.3.10 Guest Additions for Linux............
VirtualBox Guest Additions installer
Copying additional installer modules ...
Installing additional modules ...
Removing existing VirtualBox DKMS kernel modules ...done.
Removing existing VirtualBox non-DKMS kernel modules ...done.
Building the VirtualBox Guest Additions kernel modules ...done.
Doing non-kernel setup of the Guest Additions ...done.
Starting the VirtualBox Guest Additions ...done.
Installing the Window System drivers
Installing X.Org Server 1.15 modules ...done.
Setting up the Window System to use the Guest Additions ...done.
You may need to restart the hal service and the Window System (or just restart
the guest system) to enable the Guest Additions.

Installing graphics libraries and desktop services components ...done.
Note the line

Building the VirtualBox Guest Additions kernel modules ...done.
If it shows done, then virtualbox guest additions are compiled successfully.
Now restart the guest OS.

3. Verify that guest additions are working
After rebooting the OS, the screen resolution of the guest OS should adjust with the window size of virtualbox. Other things like mouse scroller, copy paste from guest to host should also work.

You can verify that the guest additions are loaded with the following command

# check loaded modules
$ lsmod | grep -io vboxguest
vboxguest

# check module 
$ modinfo vboxguest
filename:       /lib/modules/3.13.0-24-generic/updates/dkms/vboxguest.ko
version:        4.3.10
license:        GPL
description:    Oracle VM VirtualBox Guest Additions for Linux Module
author:         Oracle Corporation
.....

$ lsmod | grep -io vboxguest | xargs modinfo | grep -iw version
version:        4.3.10
4. Configure Shared folders
After installing guest additions you can share folders across the guest and host OS, allowing each of them to access each other's files. The folder exists on the host OS and is shared to the guest OS. The guest may or may not be given the permission to write to the shared folder.

Click Devices > Shared folder settings on the virtualbox window. Click the plus icon on the right side and select the directory from the host OS that you want to share with the guest OS.

If you choose "Make permanent" it becomes a Machine folder, else it is a Transient folder. You also have the option to make it read only, so that the guest OS cannot make modifications to the folder.
Once you have specified the shared directory, its time to mount it inside the guest OS. The list of shared folders would show you the name and path of the shared directory. Note down the name, and mount it using the following command

# create a directory in your home directory
$ mkdir shared

# mount using the mount command. SHARENAME is the name of the shared directory
$ sudo mount -t vboxsf SHARENAME ~/shared

# or
$ sudo mount.vboxsf SHARENAME ~/shared
You might comes across the following error message - "mount: wrong fs type, bad option".
Or "The program 'mount.vboxsf' is currently not installed."

This error is caused by a bug in VirtualBox due to which /sbin/mount.vboxsf points to a wrong path.

To fix this, you have to use the full path to the mount.vboxsf command

$ sudo /usr/lib/x86_64-linux-gnu/VBoxGuestAdditions/mount.vboxsf SHARENAME ~/shared
The bug will be fixed in upcoming releases of VirtualBox.
