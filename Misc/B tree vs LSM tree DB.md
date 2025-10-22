## B-tree

Tree structure stored on disk:
                [50]
               /    \
          [25]      [75]
         /   \      /   \
      [10] [40]  [60] [90]
```

- Data organized in a **balanced tree**
- Each node is a disk page (typically 4KB)
- Always kept sorted and balanced

### Write Operation (Expensive!)

**Writing "Hello" with key 35:**
This key is an index used to locate the data ("Hello" INSIDE THE TREE.

1. Read root node → navigate to [25]
2. Read [25] node → navigate to [40]
3. Read [40] node (leaf) → find insertion point
4. **Write [40] back to disk** with new entry. Once it knows position, it inserts (key=35, value="hello") into page buffer. Then, it **writes
modified page back to disk** and this is where i/o happens. Unlike reads which may come from cache, a write MUST PERSIST to disk, which is slower.
5. If node is full → **split node** → update parent → **cascade writes up the tree**
6. **Random disk I/O** for each level. Each level(level of tree like building of apartment) might touch different disk pages and are
**not stored sequentially on disk** and are scattered (unlike LSM tree). So each page read/write involves random disk i/o (disk head
must seek to diff locations). Disk seeks are expensive so thats y B-Tree is much slower than reads.

**Cost: Multiple random disk reads + writes per insert**

### Read Operation (Fast!)

**Reading key 35:**

1. Read root → go left to [25]
2. Read [25] → go right to [40]  
3. Read [40] → found!

**Cost: O(log n) reads, typically 3-4 disk reads**

### Why B-trees Favor Reads

✅ Data always up-to-date on disk
✅ Fast point lookups: O(log n)
✅ Fast range scans: data is sorted

❌ Writes require random I/O (slow on spinning disks)
❌ Write amplification: one write → multiple disk writes

---

## LSM Trees (Write-Optimized)

### How They Work

**LSM = Log-Structured Merge Tree**
```
Memory (MemTable):
[40→"new"] [35→"hello"] [10→"hi"]  ← New writes go here (fast!)

Disk (SSTables - immutable files):
Level 0:  [30→"x"] [20→"y"]
Level 1:  [10→"old"] [25→"z"] [40→"prev"]
```

### Write Operation (Fast!)

**Writing key 35:**

1. **Write to MemTable** (in-memory sorted structure) ← **Super fast!**
2. Also append to **Write-Ahead Log** for durability
3. Done! ✅

**Cost: O(log n) in-memory operation + sequential disk append**

### Read Operation (Slower)

**Reading key 35:**

1. Check MemTable → not found
2. Check Level 0 SSTable → not found
3. Check Level 1 SSTable → not found
4. Check Level 2... (keep searching)

**Cost: Must check multiple levels**

To speed this up, LSM trees use:
- **Bloom filters**: "Is key 35 in this file?" (fast negative check)
- **Compaction**: Merge files to reduce levels

### Background: Compaction
```
Periodically, merge smaller files into larger ones:

Level 0: [10] [20] [30]  ──┐
                            ├─→ Merge → [10, 20, 30, 40, 50]
Level 1: [40] [50]      ──┘
```

This keeps read performance acceptable.

### Why LSM Trees Favor Writes

✅ **Sequential writes**: All writes go to memory, then flushed sequentially
✅ **No random I/O** during writes
✅ **High write throughput**: Can handle millions of writes/sec

❌ Reads check multiple files (slower)
❌ Compaction uses CPU/disk in background
❌ Read amplification: one read → check many files

---

## Side-by-Side Comparison

| Aspect | B-Tree | LSM Tree |
|--------|--------|----------|
| **Write pattern** | Random I/O | Sequential I/O |
| **Write speed** | Slower | **Very fast** |
| **Read speed** | **Faster** | Slower (but optimized with bloom filters) |
| **Space efficiency** | Better (no duplicates) | Worse (old versions until compaction) |
| **Updates** | In-place modification | Append new version |
| **Good for** | Read-heavy workloads | **Write-heavy workloads** |

---

## Real-World Usage

### B-Tree Databases
- **PostgreSQL, MySQL (InnoDB)**
- Use cases: Traditional OLTP, banking, transactions
- Profile: Balanced read/write, strong consistency needs

### LSM-Tree Databases
- **Cassandra, HBase, RocksDB, LevelDB**
- Use cases: Time-series data, logs, **messaging apps**, IoT
- Profile: High write volume, eventual consistency okay

---

## For WhatsApp Messages

**Why LSM trees (Cassandra) win:**
```
Write pattern: Billions of messages per day
├─ Each message written once
└─ Read 2-3 times on average

Write:Read ratio ≈ 1:2.5
