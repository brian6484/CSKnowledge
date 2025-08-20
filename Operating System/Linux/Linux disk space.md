## df -h
df = disk filesystem, shows space usage of all mounted file systems
-h = human-readable, sizes in KB, MB, GB instead of blocks

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       100G   80G   20G  80% /
/dev/sdb1       500G  100G  400G  20% /data
```

## du -sh <directory>
du = "disk usage", shows size of files/directories
-s = summarise, *total size of directory*, not each file
-h = human-readable
```
$ du -sh /var/log
2.5G    /var/log
```
it shows /var/log folder is 2.5gb. But it isnt that intuitive like idk which file/subfolder is biggest 
so we do 1 more step
shows the top 10 largest files/folders inside /var/log,
```
du -h /var/log | sort -rh | head -n 10
```
