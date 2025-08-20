## What if program requests 1TB RAM but system has only 16 GB
1) Virtual memory concept
Linux gives each process its own large virtual address space, but its just illusion of huge memory, backed by real RAM +
swap + kernel tricks

2) memory pages
So memory is divided into small chunks called **pages** (4KB). When program asks for memory via
```
malloc(1 TB);
```
its just reserving virtual pages (aka virtual adress space). So physical RAM isnt allocated
**until program actually writes to those pages(demand paging)**

```
for (long i = 0; i < 1024L*1024L*1024L*1024L; i++) {
    arr[i] = 'A';
}
```
now the os must back every touched page with real memory, which will cause OOM error

3) Swap usage
If program actively uses more memory than its RAM, Linux moves the less-used memory pages from RAM to swap space(disk).
This makes space in RAM for active pages but swap space is mmuch slower than RAM.

4) When both RAM + Swap run out
Then the OOM Killer chooses processes to kill to free memory. It often targets the biggest memory hog (process
that uses a lot of RAM) or process with
lowest priority.
```
Out of memory: Kill process 1234 (myapp) score 987 or sacrifice child

``
