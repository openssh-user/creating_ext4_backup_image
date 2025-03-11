# Creating a sparse ext4 image for backup purposes
## Introdution
Backups can be stored in tarballs. But there is a problem. It takes a time to unarchive required files. When a tarball is large, there is a trouble.

Also backups can be stored in sparse file systems (.img). An access time to files is significantly smaller than tarballs. All it needs is to mount a file system image.
## Steps
1. Firstly, it needs to preallocate a space for ext4 file system.
```console
user@debian:~$ fallocate photos_2014_2018.img -l 50G
```
Please note that it has to provide a size with reserve space, not an exact size of your data to store in an image. How to shrink an image so that there is no free space to left, it will be described below. 

2. After that it needs to build ext4 filesystem. 
```console
user@debian:~$ sudo mkfs.ext4 photos_2014_2018.img
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done
Creating filesystem with 13107200 4k blocks and 3276800 inodes
Filesystem UUID: 613e09ae-4792-4e69-abf4-a358103fead0
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
	4096000, 7962624, 11239424

Allocating group tables: done
Writing inode tables: done
Creating journal (65536 blocks): done
Writing superblocks and filesystem accounting information: done
```
The file system is ready to use!

3. It is necessary to make sure there is an enough space to store backup data.
```console
user@debian:~$ sudo mount photos_2014_2018.img /mnt
user@debian:~$ df -h 
Filesystem      Size  Used Avail Use% Mounted on
...
/dev/loop0       49G   24K   47G   1% /mnt
```
4. If there is no an enough space, it has to resize a filesystem.
```console
user@debian:~$ sudo umount photos_2014_2018.img
user@debian:~$ sudo e2fsck -f photos_2014_2018.img
user@debian:~$ sudo resize2fs photos_2014_2018.img 52G
```
5. Now it is time to write data to image.
```console
user@debian:~$ sudo mount photos_2014_2018.img /mnt
user@debian:~$ sudo cp -r photos_2014_2018 /mnt
```
7. To decrease the size of image it has to shrink the file system. File system shrinking will minimize its  size  as  much  as  possible, given the files stored in the file system.
```console
user@debian:~$ sudo umount photos_2014_2018.img 
user@debian:~$ sudo e2fsck -f photos_2014_2018.img
user@debian:~$ sudo resize2fs -M photos_2014_2018.img
```
7. Let make the file system read only.
```console
user@debian:~$ sudo tune2fs -O read-only photos_2014_2018.img
```
8. Now the image integrity can be rechecked.
```console
user@debian:~$ sha512sum photos_2014_2018.img
660858093bcc1f39b367e300a536ed5f44c0d170776db5e8c196d6de90958cac014178c0fc695c84d312181dd62dacfb37f6275954774a39b6bacaf6730f7c34 photos_2014_2018.img
```
