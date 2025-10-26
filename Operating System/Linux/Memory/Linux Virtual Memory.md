## What if program requests 1TB RAM but system has only 16 GB
1) Virtual memory concept
Linux gives each process its own large virtual address space, but its just illusion of huge, **CONTIGUOUS** (NOT NON CONTIGUOUS cuz we have no holes) memory, backed by real RAM +
swap + kernel tricks. The physical memory is non-contiguous cuz the pages are scattered all over in Ram.

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

in inactive list is there inactive anonymous and just inavtive pages? Yes, the Linux kernel essentially separates the **Inactive** list into two main categories, which correspond directly to the types of memory pages we've discussed:

1.  **Inactive Anonymous Pages**
2.  **Inactive File-Backed Pages**

The single "Inactive" category you often see in simplified explanations is actually composed of these two distinct lists, which the kernel treats differently for memory management.

***

## The Two Inactive Lists

The kernel maintains these two lists so it can efficiently decide how to free the RAM when necessary.

### 1. Inactive Anonymous Pages (Swappable) 💾

These pages hold the unique data of a running program (like heap and stack).

* **Action for Reclamation:** If the kernel needs to free the RAM occupied by an inactive anonymous page, it **must be written to swap space** on the disk first. This is because the data has no other copy saved anywhere else.
* **Likelihood of Swap:** High. Anonymous pages are the primary target when the kernel needs to increase the amount of available RAM.

### 2. Inactive File-Backed Pages (Discardable) 📜

These pages hold data that was read from a file on a filesystem (the Page Cache).

* **Action for Reclamation:**
    * If the page is **clean** (the data in RAM is an exact match for the data on disk), the kernel can simply **discard** the page, making the RAM free instantly. If the data is needed later, it can be re-read from the file on disk.
    * If the page is **dirty** (the data in RAM has been modified but not yet saved to the file), the kernel must first **write the page back to the original file** on disk before the RAM can be freed.
* **Likelihood of Swap:** These pages are not typically "swapped" to the swap file/partition. Instead, they are simply **reclaimed** (thrown away or written back to the *file* they came from), which is often a faster operation than using the general swap area.

In summary, the kernel uses the **Inactive** status to identify pages that are "cold" (not recently used), and then uses the distinction between **Anonymous** and **File-Backed** to decide whether to save the page to swap or save it back to its original file (or just discard it).

4) When both RAM + Swap run out
Then the OOM Killer chooses processes to kill to free memory. It often targets the biggest memory hog (process
that uses a lot of RAM) or process with
lowest priority.
```
Out of memory: Kill process 1234 (myapp) score 987 or sacrifice child

``
