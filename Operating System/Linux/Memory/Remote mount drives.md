## Would those commands show a remote mounted drive?

**YES, they would!** Even remote/network filesystems show up as mounted if they're actually mounted.

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

To see the diff, see [here]()

---

## So which commands to use for mounted filesystems?

✅ **`mount`** - Shows ALL mounted filesystems (local + network)  
✅ **`df -h`** - Shows ALL mounted filesystems with space usage  
❌ **`lsblk`** - Only shows physical/virtual block storage devices
