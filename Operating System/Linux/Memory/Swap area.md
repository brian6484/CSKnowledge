## swap area
Its a portion of HDD or SSD that linux kernel uses as virtual memory when physical RAM is full. So essentially it allows the system to continue running, even when 
it doesnt have enuff RAM for all active processes.

Swapping out(paging): when ram is nearly full, kernel moves (or swaps out) inactive chunks of data (called pages) from RAM to swap area on the disk.
This frees up space in ram.

Swapping in: if process later needs the dat that was moed to swap, kernel copies(or swaps in) that data back from disk to ram.

trade-off is using swap prevents system from oom but it comes at performance cost. Disk access is much slower than RAM access, so **excessive swapping(thrashing)**
severely degrades system speed.

## 2 main ways to set aside swap area
1) dedicated swap partition

so this is the part that weve been talking about so far. Its a completely separate section of hdd that is specifically for swapping. 

2) swap file
This is a regular file that lives with other files on an existing filesystem. U can create a file of specific size (using `dd` command) then
use `mkswap` or `swapon` to tell kernel to treat the file as swap space.
