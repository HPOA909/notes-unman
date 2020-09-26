The main issue with installing Android in Qubes is that the Android
installer expects to be installed in /dev/sda but Qubes presents disks
at /dev/xvdX.
This is discussed
[here](https://github.com/unman/notes/blob/master/disks_in_Qubes), and a
solution is outlined.

# Prepare the system
1. `mkdir qemu_dmargs`
2. Download `init_patch` and `patch_init` from
https://github.com/unman/change_disk
3. Copy those files in to dom0.
    If the files were downloaded in qube1, then in dom0:
    `qvm-run -p qube1 'cat /home/user/Downloads/init_patch' > qemu_dmargs/init_patch`
    `qvm-run -p qube1 'cat /home/user/Downloads/patch_init' > qemu_dmargs/patch_init`
4.  `cp /usr/lib/xen/boot/stubdom-linux-rootfs > stubdom-linux-rootfs.orig`
5.  `cd  qemu_dmargs`
6.  `chmod +x patch_init`
7.  `sudo ./patch_init`

# Download the installer
8.  Download an installer image from https://www.android-x86.org/download. I use android-x86_64-9.0-r2.iso.

# Create the Android qube
9.  `qvm-create --standalone -l red --property kernel='' --property virt_mode=HVM --property memory=4000 reaction`
10. `qvm-start reaction --cdrom=qube1:/home/user/Downloads/android-x86_64-9.0-r2.iso`.
    Choose the option to Install to harddisk
    Create/Modify partitions - say "No" to GPT, and create a New Primary partition. Accept the default size; make it Bootable;Write:Quit.
    In the Installer, the partition you created will be highlighted.  Click OK
    Format as ext4. Select "Yes" to Confirm.
    Say "Yes" to Install GRUB boot loader.
    Say "Yes" to Install /system as read-write.

    Once installation has completed, select "Reboot" - the qube will shut down.

11.  Make sure that the new qube is connected to a netvm
12.  `qvm-start reaction `

## Configure the system
13. Configure Android as usual. At the "Connect to Wi-Fi" stage, select "See all available networks", and at the next window, select VirtWifi.
    This will enable the qube to use the netvm. Network configuration is automatic, and "tablet" will be online.
14.	Complete configuration.

To shutdown the qube, use `qvm-shutdown`

Note that it is also possible to create the qube at stage 7 as a StandAlone Template.
You can then use this as a template for other qubes, which will effectoivelly act as DisposableVMs.

N.B The Android qube is heavy on memory and CPU resources.

 
  


