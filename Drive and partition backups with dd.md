There are a lot of backup utilities available, but the simplest backup tool is available already with every single Linux distribution out there. The 'dd' tool simply copies standard input to standard output, read in 512-byte blocks.

With this, dd can copy a file to another file, or a partition to another partition, or file to partition (and vice versa). This makes dd quite versatile, and perfect for cloning partitions and drives.

For instance, to make an exact clone of the /boot partition to a backup file, you could use:
```
# dd if=/dev/sda1 of=/srv/boot.img
```
This copies the contents of the partition /dev/sda1 (mounted, for example, as /boot) to the output file /srv/boot.img. Note that dd copies "empty" space too, so if the partition is 200MB in size, even if it only contains 100MB of data, the output file will be 200MB in size. As an example:
```
# df -hT | grep sda1
/dev/sda1     ext2    198M   86M  102M  46% /boot
# dd if=/dev/sda1 of=/srv/boot.img
417627+0 records in
417627+0 records out
213825024 bytes (214 MB) copied, 2.07951 s, 103 MB/s

# du -sh /srv/boot.img
204M   /srv/boot.img
The file can be compressed after the copy, or during by piping the input to gzip or bzip2:

# dd if=/dev/sda1 | bzip2 -9f >/srv/boot2.img.bz2
417627+0 records in
417627+0 records out
213825024 bytes (214 MB) copied, 31.5072 s, 6.8 MB/s
# du -sh boot2.img.bz2
111M   boot2.img.bz2
```

Likewise, partitions can be restored from these backup copies:
```
# dd if=/srv/boot.img of=/dev/sda1
```
or:
```
# bunzip2 -dc /srv/boot2.img.bz2 | dd of=/dev/sda1
```
If you wanted to duplicate an existing drive to another, you would obtain a drive of the same (or larger) size. Assuming the drive to copy is /dev/sda and the destination drive is /dev/sdb, first use fdisk to recreate the appropriately-sized partitions, then use dd to do the actual cloning:
```
# sfdisk -d /dev/sda | sfdisk /dev/sdb
# fdisk -l /dev/sda; fdisk -l /dev/sdb
```
Compare the output of the two fdisk commands and make sure the partitions on /dev/sdb match those on /dev/sda. Once this is done, you can copy each partition using:
```
# dd if=/dev/sda of=/dev/sdb bs=446 count=1
# dd if=/dev/sda1 of=/dev/sdb1
# dd if=/dev/sda2 of=/dev/sdb2
```
The first dd call copies the MBR from the first disk to the second. This will allow the second disk to be booted, when it replaces the first. The first 446 bytes are copied with this command; that is the boot code we need.

Depending on the size of the disk and partitions, this could take some time, but the end result will be a perfectly cloned system. For best results, if you do intend to replace the one drive with another, use a LiveCD or USB boot instead of booting off of /dev/sda; this will ensure that nothing changes on-disk during the copy.