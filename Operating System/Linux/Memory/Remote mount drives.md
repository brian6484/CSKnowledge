## What is mount?
Mounting means attaching a filesystem (from disk, usb drive, network share, etc) to a specific directory in Linux so that u can access and see its contents.
For example, 

```
mount /dev/sda1 /mnt/usb
```
makes the files on the USB drive (/dev/sda1) accessibile under the /mnt/usb directory. Before moutning the filesystem exists but is **not accessible**

## Would those commands show a remote mounted drive?

Even remote/network filesystems show up as mounted if they're actually mounted.

### `mount | grep mnt`
Would show something like:
```
//fileserver.company.com/share on /mnt/company-share type cifs (rw,relatime,...)
```
**or**
```
fileserver.company.com:/exports/share on /mnt/company-share type nfs (rw,relatime,...)
```

### `df -h`
Would show:
```
Filesystem                          Size  Used Avail Use% Mounted on
//fileserver.company.com/share      2.0T  1.2T  800G  60% /mnt/company-share
```

### `lsblk`
**NO - This one wouldn't show network shares!**  
`lsblk` only shows **block devices** (physical/virtual disks like hard drives, SSDs, USB drives).  
Network filesystems (NFS, CIFS/SMB) aren't block devices, so they won't appear here.

To see the diff, see [here](https://github.com/brian6484/CSKnowledge/tree/main/Operating%20System/Linux/Memory)

## So which commands to use for mounted filesystems?

✅ **`mount`** - Shows ALL mounted filesystems (local + network)  
✅ **`df -h`** - Shows ALL mounted filesystems with space usage  
❌ **`lsblk`** - Only shows physical/virtual block storage devices
