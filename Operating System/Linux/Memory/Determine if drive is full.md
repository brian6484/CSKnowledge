For disk space, theres 2 commands - disk **space and inodes avaialability**

## df -h (omg its not -dh!!)
df = disk free

-h = human-readable format and df -h shows disk space. The most impt is -h uses **binary units** (1024-based) where 1Kb is 1024 bytes. But if u use -H it uses **decimal units** wher 1Kb is
1000 bytes. So the output will be slightly bigger numbers. -h is more common cuz storage manufacturers often advertise in decimal, but actual filesystem uses binary so -h gives the **real capacity**
of the OS.

```
df -h output:
Filesystem      Size  Used Avail Use%
/dev/sda1        50G   35G   12G  75%

df -H output:
Filesystem      Size  Used Avail Use%
/dev/sda1        54G   38G   13G  75%
```

## df -u (inode usage)
So file system can be full in 2 ways - running out of disk space OR running out of inodes. Every file uses inode and if u hit inode limit, u cant create new files even if disk space is
a lot
```
**`df -i` (inode usage):**
Filesystem     Inodes IUsed IFree IUse% Mounted on
/dev/sda1     3276800 250000 3026800   8% /
/dev/sda2     6553600 1200000 5353600  19% /home
tmpfs         2019200   12   2019188   1% /dev/shm
```

## WRONG ANSWERS!
### free -m
Thats for memory (ram + swap space) usage in Megabytes! Not disk. available = free + buff/cache
```
**`free -m` (memory in MB—for reference, NOT disk):**
              total  used  free  shared  buff/cache  available
Mem:          15923  8456  4521    234      2945      6891
Swap:         2047   1024  1023
```

### du -sh
It shows disk usage, but its different from df. It shows how much space **a specific directory/file is using**. -s is summary (total only and not showing subdirectories) and -h is human
readable.
```
du -sh /home
45G     /home
(Only shows how much /home uses, nothing about availability)

df -h
Filesystem      Size  Used Avail Use%
/dev/sda2       100G   45G   55G  45%
(Shows total, used, available, and percentage—tells you if full/free)
```

actually better to have cleaner output via sort -rh
```
du -sh /var/* | sort -rh | head -10
```
