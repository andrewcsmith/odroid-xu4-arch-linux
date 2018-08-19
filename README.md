# odroid-xu4-arch-linux
I have been wrestling this odroid mini computer to the ground and I will win and hopefully also not forget everything I have learned

* [Basic instructions from archlinuxarm.org](https://archlinuxarm.org/platforms/armv7/samsung/odroid-xu4)
* Important information re: the actual start sector of the filesystem [Odroid Partition Table](https://wiki.odroid.com/odroid-xu4/software/partition_table)

1. Attach working eMMC chip to the MMC module on the back side of the Odroid XU4
2. Set boot switch to MMC
3. Boot Odroid / Ubuntu 16.04 Default (into terminal / sans gui if you like that)
4. Attach new eMMC chip to micro sd adapter, and insert into micro sd slot

After running the following command, you should see two different cards:

```bash
ls /dev/mmcblk*
```

They should be `/dev/mmcblk0` and `/dev/mmcblk1`. Both should have extensions, such as `/dev/mmcblk0boot0`,  etc. You are currently booting from `/dev/mmcblk0` (the eMMC card directly attached), and `/dev/mmcblk1` is the one plugged into the micro sd adapter. `/dev/mmcblk1` is the card we will be loading Arch Linux onto.

Okay now for the fun part. Do this all as root (don't typo):

```bash
su root # default password: odroid
# wipe the beginning (bootloader) of the SD card, including bootloader partitions
dd if=/dev/zero of=/dev/mmcblk1 bs=1M count=8
```

Partition the eMMC:

```bash
fdisk /dev/mmcblk1
```

1. Type **o**. This will clear out any partitions on the drive.
2. Type **p**. There should be no partitions (it will say something like "dos volume")
3. Type **n**, then **p** for primary. **1** for the first partition on the drive, and **3072** for the first sector, then press enter to accept the default last sector.
4. Write the partition table and exit by typing **w**.

**NOTE**: The [official instructions"](https://archlinuxarm.org/platforms/armv7/samsung/odroid-xu4) will tell you to start the first partition at 4096. This did not work for me; the partition table docs are clear that 3072 is the beginning of the boot partition.

Create and mount the filesystem:

```bash
ls /dev/mmcblk1* # Make sure you see something like mmcblk1p1
mkfs.ext4 /dev/mmcblk1p1
mkdir /arch
# Note that we're mounting the FS partition, not the full eMMC disk. We don't want to access the bootloader sectors.
mount /dev/mmcblk1p1 /arch
```

Download and extract the OS:

```bash
cd /arch
wget http://os.archlinuxarm.org/os/ArchLinuxARM-odroid-xu3-latest.tar.gz
tar -xf ArchLinuxARM-odroid-xu3-latest.tar.gz
```

Flash the bootloader to the correct card:

```bash
cd /arch/boot
sh sd_fusing.sh /dev/mmcblk1
cd ../..
umount /arch
```

Shut it down!

```bash
shutdown now
```

Disconnect both cards, and swap them, placing the card that used to be in the micro sd port (which should now have Arch loaded onto it) into the eMMC spot on the bottom side of the XU4. Boot!
