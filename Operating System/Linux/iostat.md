## iostat
shows the utilisation level. But even if you see 100% busy (%util) in iostat, u cant immediately assume that the disk is bottleneck.
Thats cuz 

Write buffering / caching:
Many disks have an internal cache (RAM on the disk). If you send write requests, the disk acknowledges them quickly, stores them in cache, and writes them to the platter later.
→ From the OS’s point of view, the disk was “busy,” but it actually accepted more work easily.

Parallelism in storage arrays:
In a storage array (many disks behind a controller), the metric might show 100% busy because some disk somewhere is always busy.
But other disks in the array may be idle and ready to process requests.
→ The array overall can handle more throughput even though the busy metric for “the array” shows 100%.

and normally for HDD, 100% means its saturated cuz of mechanical head restrictions but for SDD, it could be processing tasks
in parallel

## so how to debug further?
check latency metrics via iostat -x.

await → average time (ms) per I/O request.

svctm (on some systems) → average service time.

If %util = 100% and await is growing → the disk is likely saturated.

If %util = 100% but await stays low → caching/parallelism is hiding the real capacity.
