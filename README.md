## DistroLinuxF0ns1
Under construction

## How to mount new FileSystem on Virtual machine:
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


# Create Your own distro:

# 1. installation process of toolchain:


![ toolchain architecture ](New-OS/captura_toolchain.png)

## 1.1 Set Environment:

	mkdir -v $LFS/tools
	ln -sv $LFS/tools /
	
## 1.2 Create lfs user 
	
	groupadd lfs
	useradd -s /bin/bash -g lfs -m -k /dev/null lfs
	passwd lfs
	chown -v lfs $LFS/tools
	chown -v lfs $LFS/sources
	su - lfs
	
## 1.3 configure environment for user 
	
	cat > ~/.bash_profile << "EOF"
	exec env -i HOME=$HOME TERM=$TERM PS1='\u:\w\$ ' /bin/bash
	EOF
	
	cat > ~/.bashrc << "EOF"
	set +h
	umask 022
	LFS=/mnt/lfs
	LC_ALL=POSIX
	LFS_TGT=$(uname -m)-lfs-linux-gnu
	PATH=/tools/bin:/bin:/usr/bin
	export LFS LC_ALL LFS_TGT PATH
	EOF
	
	source ~/.bash_profile

## 2.0 Construct temporary system 



## 2.1 Create new toolChain:

You mus crete a new specific user on your OS, the selected name could be lfs.

configuration user environment  vars, on .bashrc of /hom/lfs directory :


	set +h
	umask 002
	LFS=/mnt/lfs
	LC_ALL=POSIX
	LFS_TGT=$(uname -m)-lfs-linux-gnu
	PATH=/tools/bin:/bin:/usr/bin
	export LFS LC_ALL LFS_TGT PATH


 The build instructions assume that the Host System Requirements, including symbolic links, have been set properly:

    bash is the shell in use.

    sh is a symbolic link to bash.

    /usr/bin/awk is a symbolic link to gawk.

    /usr/bin/yacc is a symbolic link to bison or a small script that executes bison.

	lfs@debian-f0ns1:/tools/lib$ ls  -ltrh /usr/bin/bash
	-rwxr-xr-x 1 root root 1.2M Apr 18  2019 /usr/bin/bash
	lfs@debian-f0ns1:/tools/lib$ ls  -ltrh /usr/bin/sh  
	lrwxrwxrwx 1 root root 4 Apr 24 03:01 /usr/bin/sh -> bash
	lfs@debian-f0ns1:/tools/lib$ ls  -ltrh /usr/bin/awk 
	lrwxrwxrwx 1 root root 4 Apr 24 05:47 /usr/bin/awk -> gawk
	lfs@debian-f0ns1:/tools/lib$ ls  -ltrh /usr/bin/yacc 
	lrwxrwxrwx 1 root root 5 Apr 24 05:46 /usr/bin/yacc -> bison


## 2.2 Toolchain: [basic gcc] self-compile and self-linked libraries

When we finish the core toolchain compilation, it be able to make that simple test:

	echo 'int main(){}' > dummy.c
	$LFS_TGT-gcc dummy.c
	readelf -l a.out | grep ': /tools'

If everything is working correctly, there should be no errors, and the output of the last command will be of the form:

[Requesting program interpreter: /tools/lib64/ld-linux-x86-64.so.2]

Note that for 32-bit machines, the interpreter name will be /tools/lib/ld-linux.so.2.

	rm -v dummy.c a.out

Libraries that we need on directory:

	$LFS/home/sources:
	ls -ltrh
	total 399M
	-rw-r--r-- 1 f0ns1 f0ns1 1,2M abr 25  2012 autoconf-2.69.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1  88K oct  4  2014 sysklogd-1.5.1.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 201K dic 22  2014 iana-etc-2.30.tar.bz2
	-rw-r--r-- 1 f0ns1 f0ns1 951K feb 16  2015 libtool-2.4.6.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 159K mar  9  2015 intltool-0.51.0.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,4M jun 10  2015 inetutils-1.9.4.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,2M dic 31  2016 m4-1.4.18.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,2M ene  5  2017 gperf-3.1.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 457K ene 15  2017 zlib-1.2.11.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 2,0M mar 20  2017 pkg-config-0.29.2.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,4M may 26  2017 flex-2.6.4.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 685K ene 11  2018 mpc-1.1.0.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 618K feb  4  2018 expect5.45.4.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 766K feb  6  2018 patch-2.7.6.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,5M mar 11  2018 automake-1.16.1.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,1M abr 29  2018 xz-5.2.4.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 884K may 19  2018 procps-ng-3.3.15.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 457K jun 19  2018 attr-2.4.48.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 513K jun 19  2018 acl-2.2.53.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 297K sep 24  2018 psmisc-23.2.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 920K oct 27  2018 gdbm-1.18.1.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 514K nov 28  2018 dejagnu-1.6.2.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 4,0M dic 23  2018 groff-1.22.4.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 757K dic 30  2018 gzip-1.10.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,4M ene  1  2019 diffutils-3.7.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 9,7M ene  7  2019 bash-5.0.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 2,9M ene  7  2019 readline-8.0.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,4M ene 31  2019 mpfr-4.0.2.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 540K feb  7  2019 kmod-26.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 2,1M feb 23  2019 tar-1.32.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 5,2M mar 11  2019 coreutils-8.31.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 9,0M may 12  2019 gettext-0.20.1.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 339K jun 11  2019 less-551.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 3,0M jun 18  2019 gawk-5.0.1.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1  11K jun 30  2019 udev-lfs-20171102.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 6,1M jul  5  2019 grub-2.04.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 792K jul 13  2019 bzip2-1.0.8.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,1M jul 31  2019 kbd-2.2.0.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1  68M ago 12  2019 gcc-9.2.0.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,9M ago 29  2019 findutils-4.7.0.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 8,5M sep 10  2019 openssl-1.1.1d.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 120K sep 11  2019 sysvinit-2.96.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 383K sep 11  2019 tzdata2019c.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 249K sep 24  2019 XML-Parser-2.46.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 4,2M sep 24  2019 texinfo-6.7.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 413K sep 26  2019 expat-2.2.9.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,8M oct 23  2019 man-db-2.9.0.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,9M oct 26  2019 eudev-3.2.9.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,9M nov  5 20:09 zstd-1.4.4.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1  12M nov 10 15:14 perl-5.30.1.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 9,7M nov 21 22:39 tcl8.6.10-src.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 8,6M nov 26 23:50 elfutils-0.178.tar.bz2
	-rw-r--r-- 1 f0ns1 f0ns1  18M dic 18 22:30 Python-3.8.1.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 6,3M dic 18 22:34 python-3.8.1-docs-html.tar.bz2
	-rw-r--r-- 1 f0ns1 f0ns1 971K ene  1 17:21 libpipeline-1.5.2.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,5M ene  2 22:28 grep-3.4.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 7,6M ene  7 18:26 e2fsprogs-1.45.5.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1  97K ene 13 20:25 libcap-2.31.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,3M ene 15 05:12 sed-4.8.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 2,0M ene 18 16:29 gmp-6.2.0.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 2,3M ene 19 23:24 make-4.3.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,5M ene 24 00:02 meson-0.53.1.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,6M ene 24 22:43 shadow-4.8.1.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 753K ene 26 18:58 check-0.14.0.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 731K ene 27 14:54 iproute2-5.5.0.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 5,0M ene 31 10:49 util-linux-2.35.1.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1  21M feb  1 13:24 binutils-2.34.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1  16M feb  1 13:26 glibc-2.31.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 1,7M feb  9 17:53 man-pages-5.05.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 106M feb 11 13:45 linux-5.5.3.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 3,3M feb 12 10:54 ncurses-6.2.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 2,3M feb 13 19:12 bison-3.5.2.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1  15M feb 20 17:38 vim-8.2.0190.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 2,5K feb 29 22:30 sysvinit-2.96-consolidated-1.patch
	-rw-r--r-- 1 f0ns1 f0ns1  13K feb 29 22:30 kbd-2.2.0-backspace-1.patch
	-rw-r--r-- 1 f0ns1 f0ns1 2,8K feb 29 22:30 glibc-2.31-fhs-1.patch
	-rw-r--r-- 1 f0ns1 f0ns1 168K feb 29 22:30 coreutils-8.31-i18n-1.patch
	-rw-r--r-- 1 f0ns1 f0ns1 1,7K feb 29 22:30 bzip2-1.0.8-install_docs-1.patch
	-rw-r--r-- 1 f0ns1 f0ns1  22K feb 29 22:30 bash-5.0-upstream_fixes-1.patch
	-rw-r--r-- 1 f0ns1 f0ns1  32K feb 29 22:36 lfs-bootscripts-20191031.tar.xz
	-rw-r--r-- 1 f0ns1 f0ns1 5,0K abr 24 06:03 wget-list
	-rw-r--r-- 1 f0ns1 f0ns1 247K abr 24 06:04 bc-2.5.3.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 911K abr 24 06:05 file-5.38.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 1,3M abr 24 06:05 libffi-3.3.tar.gz
	-rw-r--r-- 1 f0ns1 f0ns1 206K abr 24 06:07 ninja-1.10.0.tar.gz

@ wget List : provided by s4v1t4r

-rw-r--r-- 1 f0ns1 f0ns1 5,0K abr 24 06:03 wget-list
DistroLinuxF0ns1/wget-list
download source of binaries:
wget --input-file=wget-list --continue --directory-prefix=<your directory>

For recreate the scenary is completly mandatory, follow (sequencially) the next steps:

	1.Compile binutils
	2.Compile gcc with dependencies
	3.Compile Linux API
	4.Compile glibc 

2.2.1.Compile bin utils:

	tar xf linux-5.5.3.tar.xz
	cd linux-5.5.3
	mkdir -v build
	cd       build
	../configure --prefix=/tools            \
             --with-sysroot=$LFS        \
             --with-lib-path=/tools/lib \
             --target=$LFS_TGT          \
             --disable-nls              \
             --disable-werror
	make
	case $(uname -m) in
  		x86_64) mkdir -v /tools/lib && ln -sv lib /tools/lib64 ;;
	esac
	make install
	cd ../../
	rm -fr linux-5.5.3.tar
	
2.2.2 Compile Gcc with dependencies:

	tar xf gcc-9.2.0.tar.xz
	cd gcc-9.2.0
	tar -xf ../mpfr-4.0.2.tar.xz
	mv -v mpfr-4.0.2 mpfr
	tar -xf ../gmp-6.2.0.tar.xz
	mv -v gmp-6.2.0 gmp
	tar -xf ../mpc-1.1.0.tar.gz
	mv -v mpc-1.1.0 mpc
	for file in gcc/config/{linux,i386/linux{,64}}.h
	do
  		cp -uv $file{,.orig}
		sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' \
		      -e 's@/usr@/tools@g' $file.orig > $file
		echo '
		#undef STANDARD_STARTFILE_PREFIX_1
		#undef STANDARD_STARTFILE_PREFIX_2
		#define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"
		#define STANDARD_STARTFILE_PREFIX_2 ""' >> $file
		touch $file.orig
	done
	case $(uname -m) in
  		x86_64)
    		sed -e '/m64=/s/lib64/lib/' \
        		-i.orig gcc/config/i386/t-linux64;;
	esac
	mkdir -v build
	cd       build
	../configure                                       \
	    --target=$LFS_TGT                              \
	    --prefix=/tools                                \
	    --with-glibc-version=2.11                      \
	    --with-sysroot=$LFS                            \
	    --with-newlib                                  \
	    --without-headers                              \
	    --with-local-prefix=/tools                     \
	    --with-native-system-header-dir=/tools/include \
	    --disable-nls                                  \
	    --disable-shared                               \
	    --disable-multilib                             \
	    --disable-decimal-float                        \
	    --disable-threads                              \
	    --disable-libatomic                            \
	    --disable-libgomp                              \
	    --disable-libquadmath                          \
	    --disable-libssp                               \
	    --disable-libvtv                               \
	    --disable-libstdcxx                            \
	    --enable-languages=c,c++
	make
	make install
	cd ../../
	rm -fr gcc-9.2.0

2.2.3 Compile Linux API:
	
	tar xf linux-5.5.3.tar.xz
	cd linux-5.5.3
	make mrproper
	make headers
	cp -rv usr/include/* /tools/include
	cd ../
	rm -fr linux-5.5.3
	
2.2.4 Compile Glibc:

	tar xf glibc-2.31.tar.xz
	cd glibc-2.31
	mkdir -v build
	cd       build
	../configure                             \
	      --prefix=/tools                    \
	      --host=$LFS_TGT                    \
	      --build=$(../scripts/config.guess) \
	      --enable-kernel=3.2                \
	      --with-headers=/tools/include
	 make
	 make install
	 


## Test executed success:

	lfs@debian-f0ns1:/mnt/lfs$ source ~/.bash_profile 
	
	lfs@debian-f0ns1:/mnt/lfs$ cat test.c 
	#include <stdio.h>
	int main() {
  	 // printf() displays the string inside quotation
  	 printf("Hi, guys ! toolchain compilationo ....");
  	 return 0;
	}
	lfs@debian-f0ns1:/mnt/lfs$ $LFS_TGT-gcc test.c
	 
	lfs@debian-f0ns1:/mnt/lfs$ ./a.out 
	Hi, guys ! toolchain compilationo ....
	
	lfs@debian-f0ns1:/mnt/lfs$ readelf -l a.out | grep ': /tools'
      		[Requesting program interpreter: /tools/lib64/ld-linux-x86-64.so.2]

## 2.3 install all packages from (src) on new toolchain:

	2.3.1 Libstdc++ from GCC-9.2.0
	2.3.2 Binutils-2.34 - Pass 2
	2.3.3 GCC-9.2.0 - Pass 2
	2.3.4 Tcl-8.6.10
	2.3.5 Expect-5.45.4
	2.3.6 DejaGNU-1.6.2
	2.3.7 M4-1.4.18
	2.3.8 Ncurses-6.2
	2.3.9 Bash-5.0
	2.3.10 Bison-3.5.2
	2.3.11 Bzip2-1.0.8
	2.3.12 Coreutils-8.31
	2.3.13 Diffutils-3.7
	2.3.14 File-5.38
	2.3.15 Findutils-4.7.0
	2.3.16 Gawk-5.0.1
	2.3.17 Gettext-0.20.1
	2.3.18 Grep-3.4
	2.3.19 Gzip-1.10
	2.3.20 Make-4.3
	2.3.21 Patch-2.7.6
	2.3.22 Perl-5.30.1
	2.3.23 Python-3.8.1
	2.3.24 Sed-4.8
	2.3.25 Tar-1.32
	2.3.26 Texinfo-6.7
	2.3.27 Xz-5.2.4 


 2.3.1 Libstdc++ from GCC-9.2.0
	
	ttar xf gcc-9.2.0.tar.xz
	cd gcc-9.2.0
	mkdir -v build
	cd       build
	../libstdc++-v3/configure           \
    	--host=$LFS_TGT                 \
    	--prefix=/tools                 \
    	--disable-multilib              \
    	--disable-nls                   \
    	--disable-libstdcxx-threads     \
    	--disable-libstdcxx-pch         \
    	--with-gxx-include-dir=/tools/$LFS_TGT/include/c++/9.2.0
    	make
    	make install
	cd ../../
	rm gcc-9.2.0
	
 2.3.2 Binutils-2.34 - Pass 2

	tar xf binutils-2.34
	cd binutils-2.34
	mkdir -v build
	cd  build
	CC=$LFS_TGT-gcc                \
	AR=$LFS_TGT-ar                 \
	RANLIB=$LFS_TGT-ranlib         \
	../configure                   \
	    --prefix=/tools            \
	    --disable-nls              \
	    --disable-werror           \
	    --with-lib-path=/tools/lib \
	    --with-sysroot
	make
	make install
	make -C ld clean
	make -C ld LIB_PATH=/usr/lib:/lib
	cp -v ld/ld-new /tools/bin
	cd ../../
	rm binutils-2.34
	
 2.3.3 GCC-9.2.0 - Pass 2

	ttar xf gcc-9.2.0.tar.xz
	cd gcc-9.2.0
	
	cat gcc/limitx.h gcc/glimits.h gcc/limity.h > \
  `	dirname $($LFS_TGT-gcc -print-libgcc-file-name)`/include-fixed/limits.h
	
	for file in gcc/config/{linux,i386/linux{,64}}.h
	do
	  cp -uv $file{,.orig}
	  sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' \
	      -e 's@/usr@/tools@g' $file.orig > $file
	echo '
	#undef STANDARD_STARTFILE_PREFIX_1
	#undef STANDARD_STARTFILE_PREFIX_2
	#define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"
	#define STANDARD_STARTFILE_PREFIX_2 ""' >> $file
	  touch $file.orig
	done
	
	case $(uname -m) in
	  x86_64)
	    sed -e '/m64=/s/lib64/lib/' \
		-i.orig gcc/config/i386/t-linux64
	  ;;
	esac
	
	tar -xf ../mpfr-4.0.2.tar.xz
	mv -v mpfr-4.0.2 mpfr
	tar -xf ../gmp-6.2.0.tar.xz
	mv -v gmp-6.2.0 gmp
	tar -xf ../mpc-1.1.0.tar.gz
	mv -v mpc-1.1.0 mpc
	
	sed -e '1161 s|^|//|' \
	    -i libsanitizer/sanitizer_common/sanitizer_platform_limits_posix.cc
	
	mkdir -v build
	cd       build
	
	make
	make install
	ln -sv gcc /tools/bin/cc
	
	cd ../../
	rm -fr gcc-9.2.0

## Test executed success:

	lfs@debian-f0ns1:/mnt/lfs$ source ~/.bash_profile 
	
	lfs@debian-f0ns1:/mnt/lfs$ cat test.c 
	#include <stdio.h>
	int main() {
  	 // printf() displays the string inside quotation
  	 printf("Hi, guys ! toolchain compilationo ....");
  	 return 0;
	}
	lfs@debian-f0ns1:/mnt/lfs$ cc test.c
	 
	lfs@debian-f0ns1:/mnt/lfs$ ./a.out 
	Hi, guys ! toolchain compilationo ....
	
	lfs@debian-f0ns1:/mnt/lfs$ readelf -l a.out | grep ': /tools'
      		[Requesting program interpreter: /tools/lib64/ld-linux-x86-64.so.2]
	

 2.3.4 Tcl-8.6.10
 
	tar xf tcl8.6.10-src.tar.gz
	cd tcl8.6.10
	cd unix
	./configure --prefix=/tools
	make
	TZ=UTC make test
	make install
	chmod -v u+w /tools/lib/libtcl8.6.so
	make install-private-headers
	ln -sv tclsh8.6 /tools/bin/tclsh
	cd ../../
	rm -fr tcl8.6.10
	
 2.3.5 Expect-5.45.4
 	
	tr xf expect5.45.4.tar.gz
	cd expect5.45.4
 	cp -v configure{,.orig}
	sed 's:/usr/local/bin:/bin:' configure.orig > configure
	./configure --prefix=/tools       \
            --with-tcl=/tools/lib \
            --with-tclinclude=/tools/include
	make
	make test
	make SCRIPTS="" install
	cd ../
	rm -fr expect5.45.4
	
 2.3.6 DejaGNU-1.6.2
 
 	tar xf dejagnu-1.6.2.tar.gz
 	cd dejagnu-1.6.2
	./configure --prefix=/tools
	make install
	make check
	cd ../
	rm -fr dejagnu-1.6.2
	
 2.3.7 M4-1.4.18
 
 	tar xf m4-1.4.18.tar.xz
	cd m4-1.4.18
	sed -i 's/IO_ftrylockfile/IO_EOF_SEEN/' lib/*.c
	echo "#define _IO_IN_BACKUP 0x100" >> lib/stdio-impl.h
	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr m4-1.4.18
	
 2.3.8 Ncurses-6.2
 
 	tar xf ncurses-6.2.tar.gz
	cd ncurses-6.2
	sed -i s/mawk// configure
	./configure --prefix=/tools \
            --with-shared   \
            --without-debug \
            --without-ada   \
            --enable-widec  \
            --enable-overwrite
	make
	make install
	ln -s libncursesw.so /tools/lib/libncurses.so
 	cd ../
	rm -fr ncurses-6.2
	
 2.3.9 Bash-5.0
 
 	tar xf bash-5.0.tar.gz
	cd bash-5.0
 	./configure --prefix=/tools --without-bash-malloc
	make
	make tests
	make install
	ln -sv bash /tools/bin/sh
	cd ../
	rm -fr bash-5.0
	
 2.3.10 Bison-3.5.2
 
 	tar xf bison-3.5.2.tar.xz
	cd bison-3.5.2
	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr bison-3.5.2
 	
 2.3.11 Bzip2-1.0.8
 	
	tar xf bzip2-1.0.8.tar.gz
	cd bzip2-1.0.8
 	make -f Makefile-libbz2_so
	make clean
	make
	make PREFIX=/tools install
	cp -v bzip2-shared /tools/bin/bzip2
	cp -av libbz2.so* /tools/lib
	ln -sv libbz2.so.1.0 /tools/lib/libbz2.so
	cd ../
	rm -fr bzip2-1.0.8
	
 2.3.12 Coreutils-8.31
 	
	tar xf coreutils-8.31.tar.gz
	cd coreutils-8.31
 	./configure --prefix=/tools --enable-install-program=hostname
	make RUN_EXPENSIVE_TESTS=yes check
	make install
	cd ../
	rm -fr coreutils-8.31
	
 2.3.13 Diffutils-3.7
 	
	tar xf diffutils-3.7.tar.xz
	cd diffutils-3.7
 	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr diffutils-3.7
	
 2.3.14 File-5.38
 
 	tar xf file-5.38.tar.gz
	cd file-5.38
 	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr file-5.38
	
 2.3.15 Findutils-4.7.0
 
 	tar xf findutils-4.7.0.tar.xz
	cd findutils-4.7.0
 	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr findutils-4.7.0
	
 2.3.16 Gawk-5.0.1
 
 	tar xf gawk-5.0.1.tar.xz
	cd gawk-5.0.1
 	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr gawk-5.0.1
	
 2.3.17 Gettext-0.20.1
 	
	tar xf gettext-0.20.1.tar.xz
	cd gettext-0.20.1
 	./configure --disable-shared
	make
	cp -v gettext-tools/src/{msgfmt,msgmerge,xgettext} /tools/bin
	cd ../
	rm -fr gettext-0.20.1
	
 2.3.18 Grep-3.4
 
 	tar xf grep-3.4.tar.xz
	cd grep-3.4
 	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr grep-3.4
	
 2.3.19 Gzip-1.10
 
 	tar xf gzip-1.10.tar.xz
	cd gzip-1.10
	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr gzip-1.10
	
 2.3.20 Make-4.3
 
 	tar xf make-4.3.tar.gz
	cd make-4.3
	./configure --prefix=/tools --without-guile
	make
	make check
	make install
	cd ../
	rm -fr tar xf make-4.3
 
 2.3.21 Patch-2.7.6
 
 	tar xf patch-2.7.6.tar.xz
	cd patch-2.7.6
	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr patch-2.7.6
	
 2.3.22 Perl-5.30.1
 
 	tar xf perl-5.30.1.tar.xz
	cd perl-5.30.1
	sh Configure -des -Dprefix=/tools -Dlibs=-lm -Uloclibpth -Ulocincpth
	make
	cp -v perl cpan/podlators/scripts/pod2man /tools/bin
	mkdir -pv /tools/lib/perl5/5.30.1
	cp -Rv lib/* /tools/lib/perl5/5.30.1
	cd ../
	rm -fr perl-5.30.1
	
 2.3.23 Python-3.8.1
 
 	tar xf Python-3.8.1.tar.xz
	cd Python-3.8.1
	sed -i '/def add_multiarch_paths/a \        return' setup.py
	./configure --prefix=/tools --without-ensurepip
	make
	make install
	cd ../
	rm -fr Python-3.8.1
	
 2.3.24 Sed-4.8
 
 	tar xf sed-4.8.tar.xz
	cd sed-4.8
	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr sed-4.8
	
 2.3.25 Tar-1.32
 
 	tar xf tar-1.32.tar.xz
	cd tar-1.32
	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr tar-1.32
	
 2.3.26 Texinfo-6.7
 
 	tar xf texinfo-6.7.tar.xz
	cd texinfo-6.7
	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr texinfo-6.7
	
 2.3.27 Xz-5.2.4
 
 	tar xf xz-5.2.4.tar.xz
	cd xz-5.2.4
	./configure --prefix=/tools
	make
	make check
	make install
	cd ../
	rm -fr xz-5.2.4


# 2.4 Stripping

	strip --strip-debug /tools/lib/*
	/usr/bin/strip --strip-unneeded /tools/{,s}bin/*
	rm -rf /tools/{,share}/{info,man,doc}
	find /tools/{lib,libexec} -name \*.la -delete

change permissions to root

	chown -R root:root $LFS/tools

# 3.0 Installing Basic System Software 


	mkdir -pv $LFS/{dev,proc,sys,run}

# 3.1 Creating Initial Device Nodes 
	
	mknod -m 600 $LFS/dev/console c 5 1
	mknod -m 666 $LFS/dev/null c 1 3

# 3.2 Mounting and Populating /dev 
	
	mount -v --bind /dev $LFS/dev

# 3.3 Mounting Virtual Kernel File Systems 

	mount -vt devpts devpts $LFS/dev/pts -o gid=5,mode=620
	mount -vt proc proc $LFS/proc
	mount -vt sysfs sysfs $LFS/sys
	mount -vt tmpfs tmpfs $LFS/run
	if [ -h $LFS/dev/shm ]; then
  		mkdir -pv $LFS/$(readlink $LFS/dev/shm)
	fi



## 4.0 chroot environment: (Change root directory from / to /mnt/lfs )

solving configuration problems

	root@debian-f0ns1:/mnt/lfs# ldd /mnt/lfs/bin/bash
	linux-vdso.so.1 (0x00007fff58917000)
	libtinfo.so.6 => /lib/x86_64-linux-gnu/libtinfo.so.6 (0x00007fbb0fe20000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fbb0fe1b000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fbb0fc5a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fbb0ff95000)

In order to solve linked libraries error, we include the next directories on our new root folder /mnt/lfs
	
	ls -r usr
	lib
	root@debian-f0ns1:/mnt/lfs# ls -R usr
	usr:
	lib

	usr/lib:
	x86_64-linux-gnu

	usr/lib/x86_64-linux-gnu:
	libc.so.6  libdl.so.2  libtinfo.so.6
	
	root@debian-f0ns1:/mnt/lfs# ls -R lib64
	lib64:
	ld-linux-x86-64.so.2


## 4.1 Change directory

For change directory, we use the follow script:

	root@debian-f0ns1:/mnt/lfs# cat chroot_init.sh 
	#!/bin/bash

	echo "LFS home = $LFS "
	/mnt/lfs/tools/bin/chroot /mnt/lfs/ /tools/bin/env -i \    
		HOME=/root                      \
		TERM="$TERM"                    \
		PS1='`whoami` \u:\w\$ '     \
		PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin  \   
		/tools/bin/bash --login +h

	echo "End chroot..."


output execution script:

	root@debian-f0ns1:/mnt/lfs# chmod +x chroot_init.sh 
	root@debian-f0ns1:/mnt/lfs# ./chroot_init.sh 
	LFS home = /mnt/lfs 
	I have no name!@debian-f0ns1:/# 
	I have no name!@debian-f0ns1:/# pwd
	/
	I have no name!@debian-f0ns1:/# exit
	logout
	End chroot...
	root@debian-f0ns1:/mnt/lfs# 


## 4.2 Create OS directories on new FileSystem

	mkdir -pv /{bin,boot,etc/{opt,sysconfig},home,lib/firmware,mnt,opt}
	mkdir -pv /{media/{floppy,cdrom},sbin,srv,var}
	install -dv -m 0750 /root
	install -dv -m 1777 /tmp /var/tmp
	mkdir -pv /usr/{,local/}{bin,include,lib,sbin,src}
	mkdir -pv /usr/{,local/}share/{color,dict,doc,info,locale,man}
	mkdir -v  /usr/{,local/}share/{misc,terminfo,zoneinfo}
	mkdir -v  /usr/libexec
	mkdir -pv /usr/{,local/}share/man/man{1..8}
	mkdir -v  /usr/lib/pkgconfig

	case $(uname -m) in
	 x86_64) mkdir -v /lib64 ;;
	esac

	mkdir -v /var/{log,mail,spool}
	ln -sv /run /var/run
	ln -sv /run/lock /var/lock
	mkdir -pv /var/{opt,cache,lib/{color,misc,locate},local}


Tree directories:

	I have no name!@debian-f0ns1:/# ls -ltrh /
	total 120K
	drwx------   2 root root  16K Apr 24 15:33 lost+found
	drwxrwxr-x   2 root root 4.0K Apr 25 22:07 opt
	drwxrwxr-x   2 root root 4.0K Apr 25 22:07 mnt
	drwxrwxr-x   2 root root 4.0K Apr 25 22:07 srv
	drwxrwxr-x   4 root root 4.0K Apr 25 22:07 media
	drwxrwxr-x  10 root root 4.0K Apr 25 22:09 var
	-rwxrwxr-x   1 root root  239 Apr 25 23:08 chroot_lfs.sh
	-rw-rw-r--   1 root root  111 Apr 25 23:10 ifconfig.eth0
	drwxrwxr-x   3 root root 4.0K Apr 25 23:47 boot
	drwxrwxr-x 128 root root 4.0K Apr 25 23:58 etc
	drwxrwxrwt   2 root root 4.0K Apr 26 02:07 tmp
	drwxr-xr-x   5 root root 4.0K Apr 26 12:03 home
	-rw-r--r--   1 root root   13 Apr 26 13:37 dummy.c
	-rw-rw-r--   1 root root  152 Apr 26 18:24 test.c
	-rwxr-xr-x   1 root root  20K Apr 26 18:25 a.out
	dr-xr-xr-x 177 root root    0 May  1 17:56 proc
	drwxr-xr-x  17 root root 3.3K May  1 17:56 dev
	drwxr-xr-x  12 root root 4.0K May  1 18:18 tools
	dr-xr-xr-x  13 root root    0 May  1 18:23 sys
	drwxr-xr-x   2 root root 4.0K May  1 18:35 bin
	drwxr-xr-x   2 root root 4.0K May  1 19:41 lib64
	-rwxr-xr-x   1 root root  264 May  1 20:02 chroot_init.sh
	drwxr-xr-x   3 root root 4.0K May  1 20:07 lib
	drwxr-xr-x   2 root root 4.0K May  1 20:09 sbin
	drwxr-x---   2 root root 4.0K May  1 20:09 root
	drwxr-xr-x  10 root root 4.0K May  1 20:09 usr
	drwxrwxrwt   2 root root   60 May  1 20:09 run


## 4.3 Creating Essential Files and Symlinks 

	ln -sv /tools/bin/{bash,cat,chmod,dd,echo,ln,mkdir,pwd,rm,stty,touch} /bin
	ln -sv /tools/bin/{env,install,perl,printf}         /usr/bin
	ln -sv /tools/lib/libgcc_s.so{,.1}                  /usr/lib
	ln -sv /tools/lib/libstdc++.{a,so{,.6}}             /usr/lib
	ln -sv bash /bin/sh
	
for mounted fileSystems on OS:
	
	ln -sv /proc/self/mounts /etc/mtab

create /ets/passwd:

	cat > /etc/passwd << "EOF"
	root:x:0:0:root:/root:/bin/bash
	bin:x:1:1:bin:/dev/null:/bin/false
	daemon:x:6:6:Daemon User:/dev/null:/bin/false
	messagebus:x:18:18:D-Bus Message Daemon User:/var/run/dbus:/bin/false
	nobody:x:99:99:Unprivileged User:/dev/null:/bin/false
	EOF

create /etc/groups:

	cat > /etc/group << "EOF"
	root:x:0:
	bin:x:1:daemon
	sys:x:2:
	kmem:x:3:
	tape:x:4:
	tty:x:5:
	daemon:x:6:
	floppy:x:7:
	disk:x:8:
	lp:x:9:
	dialout:x:10:
	audio:x:11:
	video:x:12:
	utmp:x:13:
	usb:x:14:
	cdrom:x:15:
	adm:x:16:
	messagebus:x:18:
	input:x:24:
	mail:x:34:
	kvm:x:61:
	wheel:x:97:
	nogroup:x:99:
	users:x:999:
	EOF

/var/ log routest :

	touch /var/log/{btmp,lastlog,faillog,wtmp}
	chgrp -v utmp /var/log/lastlog
	chmod -v 664  /var/log/lastlog
	chmod -v 600  /var/log/btmp



I have no name! : solved

	exec /tools/bin/bash --login +h
	[now /tools/bin/bash forun /etc/groups and /etc/passwd file]
	

## 4.4 Installing Basic System Software  (Chroot)


4.4.1 Linux-5.5.3 API Headers

	root:/home/sources# tar xf linux-5.5.3.tar.xz 
	root:/home/sources# cd linux-5.5.3
	make mrproper
	make headers
	find usr/include -name '.*' -delete
	rm usr/include/Makefile
	cp -rv usr/include/* /usr/include
	cd ../
	rm -fr linux-5.5.3

4.4.2  Man-pages-5.05 
	
	tar xf man-pages-5.05.tar.xz 
	cd man-pages-5.05
	make install
	cd ../
	rm -fr man-pages-5.05

4.4.3 GLIBC 2.31 

One or most important points: verify that this installation and configuration finish like example output

	tar xf glibc-2.31.tar.xz 
	cd glibc-2.31
	patch -Np1 -i ../glibc-2.31-fhs-1.patch
	case $(uname -m) in
	    i?86)   ln -sfv ld-linux.so.2 /lib/ld-lsb.so.3
	    ;;
	    x86_64) ln -sfv ../lib/ld-linux-x86-64.so.2 /lib64
		    ln -sfv ../lib/ld-linux-x86-64.so.2 /lib64/ld-lsb-x86-64.so.3
	    ;;
	esac
	mkdir -v build
	cd       build
	CC="gcc -ffile-prefix-map=/tools=/usr" \
	../configure --prefix=/usr                          \
		     --disable-werror                       \
		     --enable-kernel=3.2                    \
		     --enable-stack-protector=strong        \
		     --with-headers=/usr/include            \
		     libc_cv_slibdir=/lib
	make
	case $(uname -m) in
	  i?86)   ln -sfnv $PWD/elf/ld-linux.so.2        /lib ;;
	  x86_64) ln -sfnv $PWD/elf/ld-linux-x86-64.so.2 /lib ;;
	esac
	make check
	touch /etc/ld.so.conf
	sed '/test-installation/s@$(PERL)@echo not running@' -i ../Makefile
	make install
	cp -v ../nscd/nscd.conf /etc/nscd.conf
	mkdir -pv /var/cache/nscd
	mkdir -pv /usr/lib/locale
	localedef -i POSIX -f UTF-8 C.UTF-8 2> /dev/null || true
	localedef -i cs_CZ -f UTF-8 cs_CZ.UTF-8
	localedef -i de_DE -f ISO-8859-1 de_DE
	localedef -i de_DE@euro -f ISO-8859-15 de_DE@euro
	localedef -i de_DE -f UTF-8 de_DE.UTF-8
	localedef -i el_GR -f ISO-8859-7 el_GR
	localedef -i en_GB -f UTF-8 en_GB.UTF-8
	localedef -i en_HK -f ISO-8859-1 en_HK
	localedef -i en_PH -f ISO-8859-1 en_PH
	localedef -i en_US -f ISO-8859-1 en_US
	localedef -i en_US -f UTF-8 en_US.UTF-8
	localedef -i es_MX -f ISO-8859-1 es_MX
	localedef -i fa_IR -f UTF-8 fa_IR
	localedef -i fr_FR -f ISO-8859-1 fr_FR
	localedef -i fr_FR@euro -f ISO-8859-15 fr_FR@euro
	localedef -i fr_FR -f UTF-8 fr_FR.UTF-8
	localedef -i it_IT -f ISO-8859-1 it_IT
	localedef -i it_IT -f UTF-8 it_IT.UTF-8
	localedef -i ja_JP -f EUC-JP ja_JP
	localedef -i ja_JP -f SHIFT_JIS ja_JP.SIJS 2> /dev/null || true
	localedef -i ja_JP -f UTF-8 ja_JP.UTF-8
	localedef -i ru_RU -f KOI8-R ru_RU.KOI8-R
	localedef -i ru_RU -f UTF-8 ru_RU.UTF-8
	localedef -i tr_TR -f UTF-8 tr_TR.UTF-8
	localedef -i zh_CN -f GB18030 zh_CN.GB18030
	localedef -i zh_HK -f BIG5-HKSCS zh_HK.BIG5-HKSCS
	make localedata/install-locales
	
## 4.3.1 Configuring Glibc 


4.3.1.1 Adding nsswitch.conf 

	cat > /etc/nsswitch.conf << "EOF"
	# Begin /etc/nsswitch.conf

	passwd: files
	group: files
	shadow: files

	hosts: files dns
	networks: files

	protocols: files
	services: files
	ethers: files
	rpc: files

	# End /etc/nsswitch.conf
	EOF

4.3.1.2 Adding time zone data 

	tar -xf ../../tzdata2019c.tar.gz

	ZONEINFO=/usr/share/zoneinfo
	mkdir -pv $ZONEINFO/{posix,right}

	for tz in etcetera southamerica northamerica europe africa antarctica  \
		  asia australasia backward pacificnew systemv; do
	    zic -L /dev/null   -d $ZONEINFO       ${tz}
	    zic -L /dev/null   -d $ZONEINFO/posix ${tz}
	    zic -L leapseconds -d $ZONEINFO/right ${tz}
	done

	cp -v zone.tab zone1970.tab iso3166.tab $ZONEINFO
	zic -d $ZONEINFO -p America/New_York
	unset ZONEINFO
	

	tzselect
	
Output :

	Please identify a location so that time zone rules can be set correctly.
	Please select a continent, ocean, "coord", or "TZ".
	1) Africa							     7) Europe
	2) Americas							     8) Indian Ocean
	3) Antarctica							     9) Pacific Ocean
	4) Asia								    10) coord - I want to use geographical coordinates.
	5) Atlantic Ocean						    11) TZ - I want to specify the timezone using the Posix TZ format.
	6) Australia
	#? 7
	Please select a country whose clocks agree with yours.
	 1) ?land Islands	  11) Czech Republic	    21) Ireland		      31) Monaco		41) Serbia
	 2) Albania		  12) Denmark		    22) Isle of Man	      32) Montenegro		42) Slovakia
	 3) Andorra		  13) Estonia		    23) Italy		      33) Netherlands		43) Slovenia
	 4) Austria		  14) Finland		    24) Jersey		      34) North Macedonia	44) Spain
	 5) Belarus		  15) France		    25) Latvia		      35) Norway		45) Svalbard & Jan Mayen
	 6) Belgium		  16) Germany		    26) Liechtenstein	      36) Poland		46) Sweden
	 7) Bosnia & Herzegovina  17) Gibraltar		    27) Lithuania	      37) Portugal		47) Switzerland
	 8) Britain (UK)	  18) Greece		    28) Luxembourg	      38) Romania		48) Turkey
	 9) Bulgaria		  19) Guernsey		    29) Malta		      39) Russia		49) Ukraine
	10) Croatia		  20) Hungary		    30) Moldova		      40) San Marino		50) Vatican City
	#? 44
	Please select one of the following timezones.
	1) Spain (mainland)
	2) Ceuta, Melilla
	3) Canary Islands
	#? 1

	The following information has been given:

		Spain
		Spain (mainland)

	Therefore TZ='Europe/Madrid' will be used.
	Selected time is now:	Sun May  3 19:16:50 CEST 2020.
	Universal Time is now:	Sun May  3 17:16:50 UTC 2020.
	Is the above information OK?
	1) Yes
	2) No
	#? 1

	You can make this change permanent for yourself by appending the line
		TZ='Europe/Madrid'; export TZ
	to the file '.profile' in your home directory; then log out and log in again.

	Here is that TZ value again, this time on standard output so that you
	can use the /usr/bin/tzselect command in shell scripts:
	Europe/Madrid

	
	ln -sfv /usr/share/zoneinfo/Europe/Madrid /etc/localtime
	'/etc/localtime' -> '/usr/share/zoneinfo/Europe/Madrid'

	
4.3.1.3 Configuring the Dynamic Loader 

	cat > /etc/ld.so.conf << "EOF"
	# Begin /etc/ld.so.conf
	/usr/local/lib
	/opt/lib

	EOF
	
	cat >> /etc/ld.so.conf << "EOF"
	# Add an include directory
	include /etc/ld.so.conf.d/*.conf

	EOF
	mkdir -pv /etc/ld.so.conf.d

## 4.4  Adjusting the Toolchain 

Important point too, yout must verify and compare your output with the example

	basckup and new link
	
	mv -v /tools/bin/{ld,ld-old}
	mv -v /tools/$(uname -m)-pc-linux-gnu/bin/{ld,ld-old}
	mv -v /tools/bin/{ld-new,ld}
	ln -sv /tools/bin/ld /tools/$(uname -m)-pc-linux-gnu/bin/ld

	gcc -dumpspecs | sed -e 's@/tools@@g'                   \
	-e '/\*startfile_prefix_spec:/{n;s@.*@/usr/lib/ @}' \
	-e '/\*cpp:/{n;s@$@ -isystem /usr/include@}' >      \
	`dirname $(gcc --print-libgcc-file-name)`/specs
	  
4.4.1  Verify Adjusting the Toolchain 


	root :/home/sources/glibc-2.31/build# echo 'int main(){}' > dummy.c
	root :/home/sources/glibc-2.31/build# cc dummy.c -v -Wl,--verbose &> dummy.log
	root :/home/sources/glibc-2.31/build# readelf -l a.out | grep ': /lib'
	      [Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]
	root :/home/sources/glibc-2.31/build# grep -o '/usr/lib.*/crt[1in].*succeeded' dummy.log
	/usr/lib/../lib/crt1.o succeeded
	/usr/lib/../lib/crti.o succeeded
	/usr/lib/../lib/crtn.o succeeded
	root :/home/sources/glibc-2.31/build# grep -B1 '^ /usr/include' dummy.log
	#include <...> search starts here:
	 /usr/include
	root :/home/sources/glibc-2.31/build# grep 'SEARCH.*/usr/lib' dummy.log |sed 's|; |\n|g'
	SEARCH_DIR("=/tools/x86_64-pc-linux-gnu/lib64")
	SEARCH_DIR("/usr/lib")
	SEARCH_DIR("/lib")
	SEARCH_DIR("=/tools/x86_64-pc-linux-gnu/lib");
	root :/home/sources/glibc-2.31/build# grep "/lib.*/libc.so.6 " dummy.log
	attempt to open /lib/libc.so.6 succeeded
	root :/home/sources/glibc-2.31/build# grep found dummy.log
	found ld-linux-x86-64.so.2 at /lib/ld-linux-x86-64.so.2
	root :/home/sources/glibc-2.31/build# rm -v dummy.c a.out dummy.log
	removed 'dummy.c'
	removed 'a.out'
	removed 'dummy.log'

	cd ../../
	rm -fr glibc-2.31

## 4.5 zlib-1.2.11 install

	tar xf zlib-1.2.11
	cd zlib-1.2.11
	./configure --prefix=/usr
	make
	make check
	make install
	mv -v /usr/lib/libz.so.* /lib
	ln -sfv ../../lib/$(readlink /usr/lib/libz.so) /usr/lib/libz.so
	cd ../
	rm -fr zlib-1.2.11

## 4.6 Bzip2-1.0.8 install

	tar xf bzip2-1.0.8.tar.gz
	cd bzip2-1.0.8
	patch -Np1 -i ../bzip2-1.0.8-install_docs-1.patch
	sed -i 's@\(ln -s -f \)$(PREFIX)/bin/@\1@' Makefile
	sed -i "s@(PREFIX)/man@(PREFIX)/share/man@g" Makefile
	make -f Makefile-libbz2_so
	make clean
	make
	make PREFIX=/usr install
	cp -v bzip2-shared /bin/bzip2
	cp -av libbz2.so* /lib
	ln -sv ../../lib/libbz2.so.1.0 /usr/lib/libbz2.so
	rm -v /usr/bin/{bunzip2,bzcat,bzip2}
	ln -sv bzip2 /bin/bunzip2
	ln -sv bzip2 /bin/bzcat
	cd ../
	
	bzip2 --version
	bzip2, a block-sorting file compressor.  Version 1.0.8, 13-Jul-2019.

	   Copyright (C) 1996-2019 by Julian Seward.

	   This program is free software; you can redistribute it and/or modify
	   it under the terms set out in the LICENSE file, which is included
	   in the bzip2 source distribution.

	   This program is distributed in the hope that it will be useful,
	   but WITHOUT ANY
	   
	   WARRANTY; without even the implied warranty of
	   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
	   LICENSE file for more details.


## 4.7 xz-5.2.4 install
	
	tar xf xz-5.2.4.tar.xz
	cd xz-5.2.4
	./configure --prefix=/usr    \
            --disable-static \
            --docdir=/usr/share/doc/xz-5.2.4
	make
	make check
	make install
	mv -v   /usr/bin/{lzma,unlzma,lzcat,xz,unxz,xzcat} /bin
	mv -v /usr/lib/liblzma.so.* /lib
	ln -svf ../../lib/$(readlink /usr/lib/liblzma.so) /usr/lib/liblzma.so
	cd ../
	rm -fr xz-5.2.4
	

## 4.8 Installation of File 

	tar xf file-5.38.tar.gz
	cd file-5.38
	./configure --prefix=/usr
	make
	make check
	make install
	cd ../
	rm -fr file-5.38
	
## 4.9 Readline-8.0 

	tar xf readline-8.0.tar.gz
	cd readline-8.0
	sed -i '/MV.*old/d' Makefile.in
	sed -i '/{OLDSUFF}/c:' support/shlib-install
	./configure --prefix=/usr    \
            --disable-static \
            --docdir=/usr/share/doc/readline-8.0
	make SHLIB_LIBS="-L/tools/lib -lncursesw"
	make SHLIB_LIBS="-L/tools/lib -lncursesw" install
	mv -v /usr/lib/lib{readline,history}.so.* /lib
	chmod -v u+w /lib/lib{readline,history}.so.*
	ln -sfv ../../lib/$(readlink /usr/lib/libreadline.so) /usr/lib/libreadline.so
	ln -sfv ../../lib/$(readlink /usr/lib/libhistory.so ) /usr/lib/libhistory.so
	install -v -m644 doc/*.{ps,pdf,html,dvi} /usr/share/doc/readline-8.0
	cd ../
	rm -fr readline-8.0

## 4.10 M4-1.4.18
	
	tar xf m4-1.4.18.tar.xz
	cd m4-1.4.18
	sed -i 's/IO_ftrylockfile/IO_EOF_SEEN/' lib/*.c
	echo "#define _IO_IN_BACKUP 0x100" >> lib/stdio-impl.h
	./configure --prefix=/usr
	make
	make check
	make install
	cd ../
	rm -fr m4-1.4.18

## 4.11 Bc-2.5.3 
	
	tar xf bc-2.5.3.tar.gz
	cd bc-2.5.3
	PREFIX=/usr CC=gcc CFLAGS="-std=c99" ./configure.sh -G -O3
	make
	make test
	make install
	cd ../
	rm -fr bc-2.5.3

## 4.12 Binutils-2.34 

	tar xf binutils-2.34.tar.xz
	cd binutils-2.34
	sed -i '/@\tincremental_copy/d' gold/testsuite/Makefile.in
	mkdir -v build
	cd       build
	../configure --prefix=/usr       \
		     --enable-gold       \
		     --enable-ld=default \
		     --enable-plugins    \
		     --enable-shared     \
		     --disable-werror    \
		     --enable-64-bit-bfd \
		     --with-system-zlib
	make tooldir=/usr
	
## 4.13 GMP-6.2.0

	tar xf gmp-6.2.0.tar.xz
	cd gmp-6.2.0
	./configure --prefix=/usr    \
            --enable-cxx     \
            --disable-static \
            --docdir=/usr/share/doc/gmp-6.2.0
	make
	make html
	make check 2>&1 | tee gmp-check-log
	awk '/# PASS:/{total+=$3} ; END{print total}' gmp-check-log //PASS=197
	make install
	make install-html
	cd ../
	rm -fr gmp-6.2.0
	
## 4.14 MPFR-4.0.2 

	tar xf mpfr-4.0.2.tar.xz
	cd mpfr-4.0.2
	./configure --prefix=/usr        \
            --disable-static     \
            --enable-thread-safe \
            --docdir=/usr/share/doc/mpfr-4.0.2
	make
	make html
	make check 
	
	============================================================================
	Testsuite summary for MPFR 4.0.2
	============================================================================
	# TOTAL: 180
	# PASS:  180
	# SKIP:  0
	# XFAIL: 0
	# FAIL:  0
	# XPASS: 0
	# ERROR: 0
	============================================================================

	make install
	make install-html
	
## 4.15 MPC-1.1.0

	tar xf mpc-1.1.0.tar.gz 
	cd mpc-1.1.0
	./configure --prefix=/usr    \
            --disable-static \
            --docdir=/usr/share/doc/mpc-1.1.0
	 make
	make html
	make check
	make install
	make install-html
	cd ../
	rm -fr mpc-1.1.0

## 4.16 Attr-2.4.48 

	tar xf attr-2.4.48.tar.gz
	cd attr-2.4.48
	./configure --prefix=/usr     \
            --bindir=/bin     \
            --disable-static  \
            --sysconfdir=/etc \
            --docdir=/usr/share/doc/attr-2.4.48
	make
	make check
	make install
	mv -v /usr/lib/libattr.so.* /lib
	ln -sfv ../../lib/$(readlink /usr/lib/libattr.so) /usr/lib/libattr.so
	cd ..
	rm -fr attr-2.4.48

## 4.17 Acl-2.2.53
	
	tar xf acl-2.2.53.tar.gz
	cd acl-2.2.53
	./configure --prefix=/usr         \
            --bindir=/bin         \
            --disable-static      \
            --libexecdir=/usr/lib \
            --docdir=/usr/share/doc/acl-2.2.53
	make
	make install
	mv -v /usr/lib/libacl.so.* /lib
	ln -sfv ../../lib/$(readlink /usr/lib/libacl.so) /usr/lib/libacl.so
	cd ..
	rm -fr acl-2.2.53

## 4.18 Shadow-4.8.1

	tar xf shadow-4.8.1.tar.xz
	cd shadow-4.8.1
	sed -i 's/groups$(EXEEXT) //' src/Makefile.in
	find man -name Makefile.in -exec sed -i 's/groups\.1 / /'   {} \;
	find man -name Makefile.in -exec sed -i 's/getspnam\.3 / /' {} \;
	find man -name Makefile.in -exec sed -i 's/passwd\.5 / /'   {} \;
	sed -i -e 's@#ENCRYPT_METHOD DES@ENCRYPT_METHOD SHA512@' \
	       -e 's@/var/spool/mail@/var/mail@' etc/login.defs
	sed -i 's@DICTPATH.*@DICTPATH\t/lib/cracklib/pw_dict@' etc/login.defs
	sed -i 's/1000/999/' etc/useradd
	./configure --sysconfdir=/etc --with-group-name-max-length=32
	make
	make install
	cd ../
	rm -fr shadow-4.8.1

4.18.1 Configuring Shadow 

	pwconv
	grpconv
	sed -i 's/yes/no/' /etc/default/useradd
	
	cat /etc/default/useradd 
	# useradd defaults file
	GROUP=999
	HOME=/home
	INACTIVE=-1
	EXPIRE=
	SHELL=/bin/bash
	SKEL=/etc/skel
	CREATE_MAIL_SPOOL=no

	echo "root:root" |chpasswd // add root password
	
	cat /etc/shadow | grep root
	root:$6$5zhpIWyag5G4qmT$YJUUOKu/l0jr7h1GeyArp69DH.JzRbeescODhXrCSEsD0Y8IolUiIvUzErZW5/iJBUOwX.zEdfxjabejklMKX.:18389:0:99999:7:::

	(lfs chroot) root:/home/sources/shadow-4.8.1# su - root
	root@debian-f0ns1:~# 


## 4.19  GCC-9.2.0
	
	tar xf gcc-9.2.0.tar.xz
	cd gcc-9.2.0
	case $(uname -m) in
	  x86_64)
	    sed -e '/m64=/s/lib64/lib/' \
		-i.orig gcc/config/i386/t-linux64
	  ;;
	esac
	sed -e '1161 s|^|//|' \
    -i libsanitizer/sanitizer_common/sanitizer_platform_limits_posix.cc
    	mkdir -v build
	cd       build
	SED=sed                               \
	../configure --prefix=/usr            \
             --enable-languages=c,c++ \
             --disable-multilib       \
             --disable-bootstrap      \
             --with-system-zlib
	make
	ulimit -s 32768
	//test
	chown -Rv nobody . 
	su nobody -s /bin/bash -c "PATH=$PATH make -k check"fin
	cd ../
	rm -fr gcc-9.2.0

## 4.20 Pkg-config-0.29.2

	tar xf pkg-config-0.29.2.tar.gz
	cd pkg-config-0.29.2
	./configure --prefix=/usr              \
            --with-internal-glib       \
            --disable-host-tool        \
            --docdir=/usr/share/doc/pkg-config-0.29.2
	make
	make check
	make install
	cd ../
	rm -fr pkg-config-0.29.2

## 4.21 Ncurses-6.2

	tar xf ncurses-6.2.tar.gz
	cd ncurses-6.2
	sed -i '/LIBTOOL_INSTALL/d' c++/Makefile.in
	./configure --prefix=/usr           \
            --mandir=/usr/share/man \
            --with-shared           \
            --without-debug         \
            --without-normal        \
            --enable-pc-files       \
            --enable-widec
	make
	make install
	mv -v /usr/lib/libncursesw.so.6* /lib
	ln -sfv ../../lib/$(readlink /usr/lib/libncursesw.so) /usr/lib/libncursesw.so
	for lib in ncurses form panel menu ; do
    		rm -vf                    /usr/lib/lib${lib}.so
    		echo "INPUT(-l${lib}w)" > /usr/lib/lib${lib}.so
    		ln -sfv ${lib}w.pc        /usr/lib/pkgconfig/${lib}.pc
	done
	rm -vf                     /usr/lib/libcursesw.so
	echo "INPUT(-lncursesw)" > /usr/lib/libcursesw.so
	ln -sfv libncurses.so      /usr/lib/libcurses.so
	mkdir -v       /usr/share/doc/ncurses-6.2
	cp -v -R doc/* /usr/share/doc/ncurses-6.2

