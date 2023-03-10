# RAID5 lost, RAID5 recovered

> 原文：[https://dev.to/rkeene/raid5-lost-raid5-recovered-3kld](https://dev.to/rkeene/raid5-lost-raid5-recovered-3kld)

## lost+found

While I was on vacation a few years ago a RAID enclosure at one of our districts sites died. Attempts by off-site personnel to revive it were unsuccessful. Nobody local to the site could resolve the situation either. When I got back from vacation this problem was waiting for me. I got back to the office, then out to the district site and resolved the issue by manually reconstructing the array.

A few weeks later a coworker asked me about what the problem was and how it was resolved.

Below is my reply.

* * *

Mark,

I attached the enclosure in JBOD mode to my Linux workstation. I
wrote my own (userspace) RAID5 driver to reconstruct the array [(attached)](http://www.rkeene.org/projects/info/resources/diatribes/bloglog/raid5/raid5d.c). I then determined the number of disks in the missing RAID5 parity group (11). I then determined the order of the 11 disks -- of which there are 39,916,800 possible arrangements. I then determined that 1 disk was missing (so I only had 10 of the 11 disks, the missing disk's location still had to be determined so it did not reduce the number of possible combinations). I then determined that one of the members I was accessing was an outdated member. I then physically repaired one of the disks to get an up-to-date member. I then got the partitions on the local drives from Ian. I then created loop-back devices with differing offsets to represent the hardware array's partitions and the slices on the disk. I then ran "file" on all the loopback devices so I could determine which filesystems they were and when they were last accessed. I used "dd" to copy the loopback devices to the NFS server to be mounted using lofiadm on the UNIX system from the NFS server.

I also mounted the filesystems up on my workstation. Here's the script I used to do that ("sdk" was an oudated member of the array -- got most of the data using this, but there was some filesystem corruption):

```
# ./raid5d 6149 /dev/sdc MISSING /dev/sdf /dev/sdk /dev/sde /dev/sdi /dev/sdd /dev/sdj /dev/sdb /dev/sdg /dev/sdh 
```

Enter fullscreen mode Exit fullscreen mode

Once I repaired the previously failed disk:

```
# ./raid5d 6149 /dev/sdc /dev/sdl /dev/sdf MISSING /dev/sde /dev/sdi /dev/sdd /dev/sdj /dev/sdb /dev/sdg /dev/sdh
# nbd-client bs=4096 127.0.0.1 6149 /dev/nbd1
# losetup -r -o 805306368 --sizelimit $[36 * 1024 * 1024 * 1024] /dev/loop0 /dev/nbd1
# losetup -r -o 39460012032 --sizelimit $[36 * 1024 * 1024 * 1024] /dev/loop1 /dev/nbd1
# losetup -r -o 78114717696 --sizelimit $[256 * 1024 * 1024 * 1024] /dev/loop2 /dev/nbd1
# losetup -r -o 352992624640 --sizelimit $[256 * 1024 * 1024 * 1024] /dev/loop3 /dev/nbd1
# losetup -r -o 627870531584 --sizelimit $[679622426624 - 627870531584] /dev/loop4 /dev/nbd1
# losetup -r -o 679622426624 --sizelimit $[773094113280 - 679622426624] /dev/loop5 /dev/nbd1
# mount -t ufs -o ro,ufstype=sun,onerror=umount /dev/loop3 /mnt/hostwork/mnt/mount3
# mount -t ufs -o ro,ufstype=sun,onerror=umount /dev/loop4 /mnt/hostwork/mnt/mount4
# mount -t ufs -o ro,ufstype=sun,onerror=umount /dev/loop5 /mnt/hostwork/mnt/mount5 
```

Enter fullscreen mode Exit fullscreen mode

Here's how I copied the data to the NFS server:

```
# dd if=/dev/loop0 of=loop0.img bs=128k
# dd if=/dev/loop1 of=loop1.img bs=128k
# dd if=/dev/loop3 of=loop3.img bs=128k
# dd if=/dev/loop4 of=loop4.img bs=128k
# dd if=/dev/loop5 of=loop5.img bs=128k 
```

Enter fullscreen mode Exit fullscreen mode

Here's how I determined the mountpoint and age of all the filesystems using "file":

```
# dd if=/dev/loop0 bs=128k count=1 2>/dev/null | file -
/dev/stdin: Unix Fast File system [v1] (big-endian), last mounted on /mount0, last written at Thu Jan 12 21:51:21 2012, clean flag 253, number of blocks 37740000, number of data blocks 37164676, number of cylinder groups 712, block size 8192, fragment size 1024, minimum percentage of free blocks 1, rotational delay 0ms, disk rotational speed 90rps, TIME optimization
# dd if=/dev/loop1 bs=128k count=1 2>/dev/null | file -
/dev/stdin: Unix Fast File system [v1] (big-endian), last mounted on /mount1, last written at Thu Jan 12 21:50:10 2012, clean flag 253, number of blocks 37740000, number of data blocks 37164676, number of cylinder groups 712, block size 8192, fragment size 1024, minimum percentage of free blocks 1, rotational delay 0ms, disk rotational speed 90rps, TIME optimization
# dd if=/dev/loop2 bs=128k count=1 2>/dev/null | file -
/dev/stdin: Unix Fast File system [v1] (big-endian), last mounted on /mount2, last written at Thu Jan 12 20:23:54 2012, clean flag 253, number of blocks 268415040, number of data blocks 264335586, number of cylinder groups 5483, block size 8192, fragment size 1024, minimum percentage of free blocks 1, rotational delay 0ms, disk rotational speed 90rps, TIME optimization
etc... 
```

Enter fullscreen mode Exit fullscreen mode

Here are the partitons on the logical drive, from Ian (only ld0 was damaged):

```
 (02:12:43) Ian * partitions
  LD/LV    ID-Partition        Size
  -------------------------------------
  ld0-00   00000000-00        256MB
  ld0-01   00000000-01        256MB
  ld0-02   00000000-02        256MB
  ld0-03   00000000-03      36.00GB
  ld0-04   00000000-04      36.00GB
  ld0-05   00000000-05     256.00GB
  ld0-06   00000000-06     256.00GB
  ld0-07   00000000-07      96.41GB
  ld1-00   11111111-00     682.39GB
  ld2-00   22222222-00     272.96GB
  ld2-01   22222222-01     272.96GB 
```

Enter fullscreen mode Exit fullscreen mode

Obviously I'm omitting a lot of the details, but this is a basic overview.

Let me know if you have any further questions.

* * *

I told the users they should migrate their data off that controller.

They are still using it.

Hooray.