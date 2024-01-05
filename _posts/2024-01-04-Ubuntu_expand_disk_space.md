---
title: "Expanding Disk Space in Ubuntu"
date: 2024-01-04 21:00:00 -700
categories: [Ubuntu]
tags: [homelab,proxmox,ubuntu]
---

# Expanding Disk space in Ubuntu

The following webpage is what I used to expand the disk space on my Ubuntu server that is running much of my HomeLab services.

[Expanding Disk space](https://packetpushers.net/ubuntu-extend-your-default-lvm-space/)

There are many steps to this process, just take your time and walk through every step. it can be confusing because the are many steps using, lvdisplay, vgdisplay, and pvdisplay. 

Start a the point in the document called **Use Space from Extended Physical (or Virtual) Disk** 

## Below is simple copy and paste from the website for steps that need to completed. 

First you need to increase the size of the disk being presented to the Linux OS. This is most likely done by expanding the virtual disk in KVM/VMWare/Hyper-V or by adjusting your RAID controller / storage system to increase the volume size. You can often do this while Linux is running; without shutting down or restarting. I’ve extended my 100GB disk to 200GB for my example machine.

Once that is done, you may need to get Linux to rescan the disk for the new free space. Check for free space by running 
```shell
cfdisk
``` 
and see if there is free space listed, use “q” to exit once you’re done.

***Insert Picture***

If you don’t see free space listed, then initiate a rescan of /dev/sda  with 
```shell
echo 1>/sys/class/block/sda/device/rescan
```
Once done, rerun **cfdisk** and you should see the free space listed.

***Insert Picture***

Select your /dev/sda3 partition from the list and then select **“Resize”** from the bottom menu. Hit **ENTER** and it will prompt you to confirm the new size. Hit **ENTER** again and you will now see the /dev/sda3 partition with a new larger size.

Select **“Write”** from the bottom menu, type **yes** to confirm, and hit **ENTER**. Then use **“q”** to exit the program.

Now that the LVM partition backing the  /dev/sda3 Physical Volume (PV) has been extended, we need to extend the PV itself. Run 
```shell
pvresize /dev/sda3
```
to do this and then use 
```shell
pvdisplay
```
to check the new size.

***Insert Picture***

Now let’s check the Volume Group (VG) free space with 
```shell
vgdisplay
```
***Insert Picture***

Now let’s check the size of our upstream Logical Volume (LV) using 
```shell
lvdisplay
```
Extend the LV to use up all the VG’s free space with 
```shell
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```
and then check the LV one more time with
```shell
lvdisplay
```
to make sure it has been extended.

***Insert Picture***

At this point, the block volume underpinning our root filesystem has been extended, but the filesystem itself has not been resized to fit that new volume. To do this, run 
```shell
df -h
```
to check the current size of the file system, then run
```shell
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
``` 
to resize it, and 
```shell
df -h
```
one more time to check the new file system available space.

***Insert Picture***

And there you go. You’ve now taken an expanded physical (or virtual) disk and moved that free space all the way up through the LVM abstraction layers to be used by your (critically full) root file system.