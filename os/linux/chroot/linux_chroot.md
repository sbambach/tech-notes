# Linux chroot, to e.g. fix bootloader problems

## Description

Sometimes when your bootloader is corrupted, you need to fix it. So here are the steps to get your existing filesystems mounted and chroot'ed to fix e.g. grub or initrd.

## This example has the following partitions

- **/dev/sdc1** Boot (/boot) partition
- **/dev/sdc5** Root (/) partition

## Boot an as compatible as possible distribution

So if your real system on disc is e.g. **Ubuntu 18.04**, than please download and use the **Ubuntu 18.04 Live DVD** to boot from.

1) Download and "burn" image to disc
2) Boot it (activate support in BIOS if needed)
3) Start a terminal as root

## Steps to chroot into existing filesystem

```bash
mkdir /mnt

# Mount root partition
mount /dev/sdXY /mnt
# Mount boot partition
mount /dev/sdYZ /mnt/boot

# Bind mount existing dirs from current live dvd
# That's why you should boot a very similar distribution
mount --bind /dev /mnt/dev
mount --bind /dev/pts /mnt/dev/pts
mount --bind /proc /mnt/proc
mount --bind /sys /mnt/sys
mount --bind /run /mnt/run

# Now chroot into system
chroot /mnt /bin/bash
```

Now you can e.g. update initrd, grub, kernel drivers, ... or maybe just fix /etc/fstab.
