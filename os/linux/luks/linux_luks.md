# Linux LUKS (Linux Unified Key Setup)

## Description

LUKS is a disk encryption specification intended for Linux and is based on an enhanced version auf cryptsetup and dm-crypt.

See also [official website](https://gitlab.com/cryptsetup/cryptsetup/blob/master/README.md).

## Prerequisite

- Get root with `sudo -i`
- Install needed software with `apt-get install cryptsetup`
- Optional: Wipe disk before using with LUKS

    ```bash
    dd if=/dev/zero of=/dev/sdXY iflag=nocache oflag=direct bs=4096
    ```

## Create and use of crypted devices

- Create an encrypted device

    ```bash
    cryptsetup luksFormat -c aes-xts-plain64 -s 512 -h sha512 -i 5000 -y /dev/sdXY
    ```

- Test it

    ```bash
    cryptsetup luksDump /dev/sdXY
    ```

- Open device

    ```bash
    $ cryptsetup luksOpen /dev/sdXY sdXY_crypt
    Enter passphrase for /dev/sdXY: ****************
    ```

    This will create a device like **/dev/mapper/sdXY_crypt**.

- Close device

    ```bash
    cryptsetup luksClose sdXY_crypt
    ```

## Booting from crypted device needs more steps

- Get UUID of given device

    ```bash
    $ lsblk -f /dev/sdX
    NAME   FSTYPE      LABEL UUID                                 MOUNTPOINT
    sdX
    ├─sdX1 ext4              763b1a31-0a41-453c-aebb-8f28e45b19db
    ├─sdX2
    └─sdX5 crypto_LUKS       92e4fc6c-eac0-434e-9d4c-316449a0f122
    ```

- Setup /etc/crypttab

    ```txt
    sdXY_crypt UUID=92e4fc6c-eac0-434e-9d4c-316449a0f122 none luks,tries=3
    ```

- Update initrd

    ```bash
    update-initramfs -c -k all
    ```
