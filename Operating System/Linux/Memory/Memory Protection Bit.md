## Memory Protection Bit
Each page in memory has permission flags stored in the page table that control what can be done with that page.

### permission
Read (R): Can the page be read from?
Write (W): Can data be written to the page?
Execute (X): Can the CPU execute code from this page?

So there can be several combinations like r+x means readble and executable but not writable.

### mode
besides this permission theres also user mode - User/Kernal.

User mode page - normal programs can access
Kernel page - only OS Kernel can access

### prevent buffer overflow? (i dont get it)
