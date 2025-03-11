# Creating ext4 file image for backup purposes
## Description

## Steps
Firstly, it needs to allocate a space for ext4 filesystem.
```console
fallocate photos.img -L 50G
```
Please not
