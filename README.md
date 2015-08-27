# raspberry-arch
HOW TO install and configure Arch Linux on Raspberry Pi

## SD Card Creation

Replace sdX in the following instructions with the device name for the SD card as it appears on your computer.

1. Start fdisk to partition the SD card:

   `fdisk /dev/sdX`

2. At the fdisk prompt, delete old partitions and create a new one:
  1. Type __o__. This will clear out any partitions on the drive.
  2. Type __p__ to list partitions. There should be no partitions left.
  3. Type __n__, then __p__ for primary, __1__ for the first partition on the drive, press __ENTER__ to accept the default first sector, then type __+100M__ for the last sector.
  4. Type __t__, then __c__ to set the first partition to type W95 FAT32 (LBA).
  5. Type __n__, then __p__ for primary, __2__ for the second partition on the drive, and then press __ENTER__ twice to accept the default first and last sector.
  6. Write the partition table and exit by typing __w__.

   Your final partition table should be something like that:
   ```
   Device      Boot    Start      End   Sectors     Size     Id  Type
   /dev/sdb1            2048   206847    204800     100M      c  W95 FAT32 (LBA)
   /dev/sdb2          206848 15523839  15316992     7.3G     83  Linux
   ```

3. Create and mount the FAT filesystem:

   ```
   mkfs.vfat /dev/sdX1
   mkdir boot
   mount /dev/sdX1 boot
   ```

4. Create and mount the ext4 filesystem:

   ```
   mkfs.ext4 /dev/sdX2
   mkdir root
   mount /dev/sdX2 root
   ```

5. Download and extract the root filesystem (as root, not via sudo):
    
   ```
   wget http://archlinuxarm.org/os/ArchLinuxARM-rpi-latest.tar.gz
   bsdtar -xpf ArchLinuxARM-rpi-latest.tar.gz -C root
   sync
   ```

6. Move boot files to the first partition:

   `mv root/boot/* boot`

7. (Optional) Configure a static IP address:

   You need to configure [systemd-networkd](https://wiki.archlinux.org/index.php/Systemd-networkd#Wired_adapter_using_a_static_IP). Edit the configuration file `root/etc/systemd/network/eth0.network`

   ```
   [Match]
   Name=eth0

   [Network]
   Address=192.168.1.8/24
   Gateway=192.168.1.1
   DNS=8.8.8.8
   DNS=8.8.4.4
   ```

8. Unmount the two partitions:

   `umount boot root`

9. Insert the SD card into the Raspberry Pi, connect ethernet, and apply 5V power.

10. Use the serial console or SSH to the IP address (given to the board by your network DHCP server or static address configured in step 7).
  * Login as the default user _alarm_ with the password _alarm_.
  * The default _root_ password is _root_.

Source: [archlinuxarm.org](http://archlinuxarm.org/platforms/armv6/raspberry-pi)
