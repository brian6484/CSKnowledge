## swapping 
when os needs to swap pages to disk (i.e. ram is full)
1) os takes a page from ram (4KB)
2) writes it to disk in or more **blocks** (if disk block is 512 bytes, 8 such blocks is needed)

or when loading a file
1) file system reads **blocks** from disk
2) os loads them into **pages** in ram

so block is how u organise data on ur hard drive/SSD but page is how u organise that data in RARM
