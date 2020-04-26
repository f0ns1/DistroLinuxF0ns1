## DistroLinuxF0ns1
Under construction

## How to mount, a new FileSystem on Virtual machine:
 logic architecture



![ file-system ](New-OS/FileSystem:virtualOs.png)


After many local comiplations for al src code on VM, we create a root directory on new mounted fileSystem, with the follow linux strxuture:

# original Hard Disk vdi mounted, for new distro:


	/bin/lsblk  | grep sdb
	sdb      8:16   0   30G  0 disk 
	├─sdb1   8:17   0   10G  0 part /mnt/lfs
	├─sdb2   8:18   0    1K  0 part 
	├─sdb5   8:21   0    2G  0 part [SWAP]
	└─sdb6   8:22   0 18,1G  0 part /mnt/lfs/home


	ls -ltrh /dev/sdb*
	brw-rw---- 1 root root 8, 16 Apr 25 02:55 /dev/sdb
	brw-rw---- 1 root root 8, 18 Apr 25 02:55 /dev/sdb2
	brw-rw---- 1 root root 8, 21 Apr 25 02:55 /dev/sdb5
	brw-rw---- 1 root root 8, 22 Apr 25 02:55 /dev/sdb6
	brw-rw---- 1 root root 8, 17 Apr 25 02:55 /dev/sdb1


	df -h | grep /dev/sdb
	/dev/sdb1        9,8G   6,1G  3,2G  66% /mnt/lfs
	/dev/sdb6         18G   1,5G   16G   9% /mnt/lfs/home


# New environment on /mnt/lfs

usign chroot for change root directory / to /mnt/lfs

script:
	cat /mnt/lfs/chroot_lfs.sh 

	#!/bin/bash
	LFS=/mnt/lfs
	echo "Root directory: $LFS"
		/mnt/lfs/tools/bin/chroot "$LFS" /tools/bin/env -i    \
		HOME=/root                      \
		TERM="$TERM"                    \
		PS1='(lfs chroot) \u:\w\$ '     \
		PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin     \
		/tools/bin/bash --login +h

create tree directory structure for linux Operative System execution:

	(lfs chroot) root:/# ls -ltrh /
	total 84K
	drwx------   2 root root  16K Apr 24 15:33 lost+found
	dr-xr-xr-x 182 root root    0 Apr 25 00:55 proc
	drwxr-xr-x  11 root root 4.0K Apr 25 01:58 tools
	dr-xr-xr-x  13 root root    0 Apr 25 01:59 sys
	drwxr-xr-x  17 root root 3.3K Apr 25 04:03 dev
	drwxrwxrwt   2 root root   40 Apr 25 14:10 run
	drwxrwxr-x   2 root root 4.0K Apr 25 16:14 lib64
	-rwxrwxr-x   1 root root  286 Apr 25 16:28 chroot_lfs.sh
	drwxrwxr-x   2 root root 4.0K Apr 25 16:56 opt
	drwxrwxr-x   2 root root 4.0K Apr 25 16:56 mnt
	drwxrwxr-x   3 root root 4.0K Apr 25 16:56 lib
	drwxrwxr-x   2 root root 4.0K Apr 25 16:56 boot
	drwxrwxr-x   2 root root 4.0K Apr 25 16:57 srv
	drwxrwxr-x   2 root root 4.0K Apr 25 16:57 sbin
	drwxrwxr-x   4 root root 4.0K Apr 25 16:57 media
	drwxrwxr-x  10 root root 4.0K Apr 25 16:59 var
	drwxrwxr-x   2 root root 4.0K Apr 25 17:00 bin
	drwxrwxrwt   2 root root 4.0K Apr 25 17:01 tmp
	drwxrwxr-x   4 root root 4.0K Apr 25 17:01 etc
	drwxr-x---   2 root root 4.0K Apr 25 17:01 root
	drwxrwxr-x  14 root root 4.0K Apr 25 17:03 usr
	drwxr-xr-x   4 root root 4.0K Apr 25 17:08 home

# New virtual file system:

	(lfs chroot) root:/# df -a
	Filesystem     1K-blocks    Used Available Use% Mounted on
	/dev/sdb1       10218068 6327548   3351744  66% /
	/dev/sdb6       18516200 1548212  16004356   9% /home
	udev             2000936       0   2000936   0% /dev
	devpts                 0       0         0    - /dev/pts
	proc                   0       0         0    - /proc
	sysfs                  0       0         0    - /sys
	tmpfs            2020764       0   2020764   0% /run



# Grub new Distro : DistroLinuxFonsi

![ grub ](New-OS/grub.png)
