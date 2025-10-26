## Thrashing
Thrashing occurs when system spends more time **swapping pages between RAM and disk**, rather than spending time executing processes. This happens cuz theres not enough RAM and processes constantly
need pages from disk, causing continous major page faults.

## Solution
Add more physical ram or reduce number of Running processes or decrease process memory usage ora djust the swappiness parameter (that linux uses to swap aggressively)
