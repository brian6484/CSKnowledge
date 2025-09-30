## Redis: Single Server vs Cluster

### **Single Redis Server (Small Scale)**

```
┌─────────────────┐
│  Redis Server   │
│  (1 instance)   │
│                 │
│  Port: 6379     │
│  Memory: 64GB   │
└─────────────────┘
```

**Characteristics:**
- ✅ Simple to set up
- ✅ Fast (all data in one place)
- ❌ Single point of failure
- ❌ Limited by one machine's RAM
- ❌ No redundancy

**Good for:**
- Development/testing
- Small apps (<10GB data)
- Non-critical caching

**For Facebook scale: NOT ENOUGH!** ❌

---

### **Redis Cluster (Production at Scale)**

```
┌─────────────────────────────────────────────────────────┐
│                    Redis Cluster                         │
│                                                          │
│  Master 1        Master 2        Master 3               │
│  (Slots 0-5461)  (Slots 5462-    (Slots 10923-         │
│                   10922)          16383)                │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐         │
│  │ Server A │    │ Server B │    │ Server C │         │
│  │ Port 6379│    │ Port 6379│    │ Port 6379│         │
│  └─────┬────┘    └─────┬────┘    └─────┬────┘         │
│        │               │               │                │
│        ↓               ↓               ↓                │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐         │
│  │ Replica 1│    │ Replica 2│    │ Replica 3│         │
│  │ Server D │    │ Server E │    │ Server F │         │
│  └──────────┘    └──────────┘    └──────────┘         │
└─────────────────────────────────────────────────────────┘
```

**Yes, cluster = multiple servers!**

---

## How Redis Cluster Works

### **Key Concept: Hash Slots**

Redis Cluster divides keys into **16,384 hash slots** (0-16383): Hash slot is like hash(key) in consistnet hash ring where a certain range of
hash values/slots are processed by a certain redis node.

```python
# When you write a key, Redis calculates which slot it belongs to
key = "post:123:reactions"
slot = CRC16(key) % 16384  # Let's say slot = 7532

# Slot 7532 is on Master 2
# So this key goes to Master 2 (Server B)
```

**Slot Distribution:**
```
Master 1 (Server A): Slots 0 - 5461      (5462 slots)
Master 2 (Server B): Slots 5462 - 10922  (5461 slots)
Master 3 (Server C): Slots 10923 - 16383 (5461 slots)

Total: 16,384 slots
```

---

### **Example: Where Keys Are Stored**

```python
# Calculate slots for our keys
"post:123:reactions" → CRC16 hash → slot 7532 → Master 2 ✓
"post:456:reactions" → CRC16 hash → slot 2341 → Master 1 ✓
"posts:by_reactions" → CRC16 hash → slot 14582 → Master 3 ✓

# Data is distributed across all 3 masters!
```

**Distribution:**
```
Server A (Master 1):
  post:456:reactions = {like: 5, love: 2}
  post:789:reactions = {like: 12}
  ...

Server B (Master 2):
  post:123:reactions = {like: 8, love: 1}
  post:234:reactions = {like: 3}
  ...

Server C (Master 3):
  posts:by_reactions = ZSET {post:123: 8, post:456: 5, ...}
  posts:by_time = ZSET {post:123: 1706178600, ...}
  ...
```

The system is composed of at least three servers or nodes, with two acting as masters for sharded data and a third potentially managing global or aggregated indexes.

### Server A and Server B (Master Nodes)
These servers hold the primary, sharded data.

* **Key Pattern: `post:456:reactions`** and **`post:123:reactions`**
    * This is a **Redis Hash** data structure, which is ideal for storing fields and values of an object.
    * **The Key:** The format uses a colon (`:`) convention, suggesting a structure like `<type>:<ID>:<field>`. The key is specific to a single post and a single data type (`reactions`).
    * **The Value (Hash):** `like` and `love` are fields, and the numbers (`5`, `2`, `12`, `8`, `1`) are their corresponding counts (values).
    * **Sharding:** Since there are no curly braces `{}` in the keys, the entire key (`post:456:reactions`, `post:123:reactions`) is hashed to determine which specific node (Server A or Server B) will store the data. This means that a client needs to know the entire key to locate the data on the correct node.

### Server C (Index Node)
This server appears to be handling **secondary indexing** across the data sharded on Servers A and B.

* **Key: `posts:by_reactions`**
    * This is a **Redis Sorted Set (ZSET)**. Sorted Sets are used to store unique strings (members) with a numerical score, making them perfect for creating ordered indexes.
    * **Members:** The members are **post keys** (e.g., `post:123`, `post:456`).
    * **Scores:** The scores are the **reaction counts** (e.g., `8`, `5`), likely representing the total number of a specific, most important reaction (like `like` or an aggregate).
    * **Purpose:** This ZSET allows the application to quickly retrieve the **top N posts** by reaction count (leaderboard) or fetch posts within a certain reaction range, regardless of which primary master (A or B) holds the full reaction data.

* **Key: `posts:by_time`**
    * This is also a **Redis Sorted Set (ZSET)**.
    * **Members:** Post keys (e.g., `post:123`).
    * **Scores:** The score is a **Unix timestamp** (e.g., `1706178600`).
    * **Purpose:** This allows the application to quickly retrieve the **most recent posts** or posts within a specific time range.

***

## Implications for a Clustered Environment

The key difference between the keys on Servers A/B and Server C is how they relate to the **Redis Cluster's sharding mechanism**:

1.  **Primary Data (A/B):** The reaction data keys are simple, and their sharding is determined by the **full key string**.
2.  **Index Data (C):** The index keys (`posts:by_reactions`, `posts:by_time`) are likely **global indexes**. If Server C is a separate master in the cluster, these index keys might all hash to Server C because they contain no hash tag (`{}`) and their keys are unique global names, or a system administrator has configured the cluster to ensure these keys (and the hash slots they map to) all reside on Server C.

This setup requires the application logic to:
* **Update:** Increment the counts in the Hash on Server A/B (e.g., `HINCRBY post:456:reactions like 1`).
* **Index:** Concurrently update the Sorted Sets on Server C (e.g., `ZINCRBY posts:by_reactions 1 post:456`). This is a common **denormalization pattern** in sharded databases to support queries that cross multiple shards.

---

### **Client Automatically Finds Right Server**

```python
from redis.cluster import RedisCluster

# Connect to cluster (provide any node, it will discover others)
redis = RedisCluster(
    startup_nodes=[
        {"host": "server-a", "port": 6379},
        {"host": "server-b", "port": 6379},
        {"host": "server-c", "port": 6379}
    ],
    decode_responses=True
)

# Client automatically routes to correct server!
redis.hincrby("post:123:reactions", "like", 1)
# Client calculates: slot 7532 → routes to Master 2 (Server B)

redis.zincrby("posts:by_reactions", 1, "post:123")
# Client calculates: slot 14582 → routes to Master 3 (Server C)
```

**Client handles routing transparently!**

---

## Master-Replica Architecture

Each master has **replicas** (slaves) for redundancy:

```
Master 1 (Server A)              Replica 1 (Server D)
  Writes: ✅                       Writes: ❌ (read-only)
  Reads: ✅                        Reads: ✅
  ↓                               ↑
  └─────── Replication ──────────┘
          (async copy)
```

**Replication Flow:**

```
1. Client writes to Master 1:
   HINCRBY post:456:reactions like 1

2. Master 1 executes write immediately

3. Master 1 replicates to Replica 1 (async)
   Replica 1 applies the same write

4. Now both have the same data!
```

---

## What Happens When Redis Goes Down?

### **Scenario 1: Single Master Fails**

```
Before:
Master 2 (Server B) ← Handling requests
  ↓ replicates to
Replica 2 (Server E) ← Standby

After Master 2 crashes:
Master 2 (Server B) ❌ DOWN
  
Replica 2 (Server E) ✅ Promoted to Master!
```

**Automatic Failover (30 seconds):**

```
Time: 0s
Master 2 crashes

Time: 15s
Other masters detect failure (heartbeat timeout)
Cluster enters failover mode

Time: 30s
Replica 2 promoted to Master
Cluster reconfigured
Slots 5462-10922 now served by Replica 2 (now Master)

Time: 30s+
Service restored! ✅
Clients automatically reconnect to new master
```

**During those 30 seconds:**
- ❌ Keys in slots 5462-10922 unavailable
- ✅ Keys in other slots still work
- ✅ 2/3 of data still accessible

---

### **Scenario 2: Entire Redis Cluster Down**

**This is the CRITICAL scenario!**

```
All Redis servers crash simultaneously
(Power outage, network partition, etc.)

Impact:
❌ Real-time counters unavailable
❌ Can't sort by popularity in real-time
❌ Hot post cache unavailable

But application still works! ✅ (degraded mode)
```

---

## Degraded Mode Without Redis

### **Application Behavior:**

```python
class ReactionService:
    
    async def add_reaction(self, post_id, user_id, reaction_type):
        try:
            # Step 1: Write to Cassandra (ALWAYS works)
            await cassandra.execute(
                "INSERT INTO reactions (post_id, user_id, reaction_type) VALUES (?, ?, ?)",
                (post_id, user_id, reaction_type)
            )
            
            # Step 2: Try to update Redis
            try:
                await redis.hincrby(f"post:{post_id}:reactions", reaction_type, 1)
                await redis.zincrby("posts:by_reactions", 1, f"post:{post_id}")
            except RedisConnectionError:
                # Redis is down! Log error but CONTINUE
                logger.error(f"Redis unavailable, skipping cache update")
                # DON'T fail the request! ✅
            
            # Step 3: Publish to Kafka
            await kafka.publish("reaction.added", {...})
            
            # Step 4: Return success (even if Redis failed!)
            return {"status": "success"}
            
        except Exception as e:
            # Only fail if Cassandra fails (source of truth)
            logger.error(f"Cassandra write failed: {e}")
            raise
```

**What users experience:**
- ✅ Can still like posts (saved to Cassandra)
- ✅ UI updates (frontend increments count optimistically)
- ❌ Like counts may be slightly stale
- ❌ "Sort by popular" may be inaccurate temporarily

---

### **Search Service Without Redis:**

```python
class SearchService:
    
    async def search_posts(self, query, sort_by='relevance'):
        # Get matching posts from Elasticsearch
        es_results = await elasticsearch.search(...)
        
        if sort_by == 'popular':
            try:
                # Try to get counts from Redis
                post_ids = [hit['_id'] for hit in es_results['hits']['hits']]
                scores = await redis.get_scores(post_ids)
                sorted_posts = sort_by_scores(post_ids, scores)
                
            except RedisConnectionError:
                # Redis down! Fallback to Elasticsearch counts
                logger.warning("Redis unavailable, using ES counts")
                
                # Sort by reaction_count field in ES (slightly stale)
                sorted_posts = sort_by_field(es_results, 'reaction_count')
                
                # OR fallback to sorting by time
                # sorted_posts = sort_by_field(es_results, 'created_at')
        
        return sorted_posts
```

**Fallback chain:**
```
1. Try Redis (real-time, accurate) ✅
   ↓ (if Redis down)
2. Use Elasticsearch counts (2 second lag) ⚠️
   ↓ (if ES also has issues)
3. Sort by timestamp (always works) ✅
```

---

## Recovery: Rebuilding Redis

When Redis comes back online, we need to rebuild the data:

### **Option 1: Rebuild from Cassandra**

```python
async def rebuild_redis_from_cassandra():
    """
    Rebuild Redis counters from Cassandra reaction_counts table
    Run this when Redis cluster is restored
    """
    logger.info("Starting Redis rebuild from Cassandra...")
    
    # Fetch all aggregated counts from Cassandra
    rows = await cassandra.execute(
        "SELECT post_id, total_count, like_count, love_count, angry_count FROM reaction_counts"
    )
    
    pipeline = redis.pipeline()
    
    for row in rows:
        post_id = row['post_id']
        
        # Rebuild sorted set (for ranking)
        pipeline.zadd(
            "posts:by_reactions",
            {f"post:{post_id}": row['total_count']}
        )
        
        # Rebuild hash (for breakdown)
        pipeline.hset(
            f"post:{post_id}:reactions",
            mapping={
                'like': row['like_count'],
                'love': row['love_count'],
                'angry': row['angry_count']
            }
        )
    
    # Execute all commands in batch
    await pipeline.execute()
    
    logger.info(f"Rebuilt {len(rows)} posts in Redis")
```

**Timeline:**
```
Redis cluster restored at 10:00:00
Run rebuild script at 10:00:05
Process 1 billion posts at 100K/sec
Complete at 10:02:45 (2 minutes 40 seconds)

During rebuild:
- Some counters available (already rebuilt)
- Some missing (not yet rebuilt)
- Gradually improves as more data loaded
```

---

### **Option 2: Rebuild from Kafka (Event Replay)**

```python
async def rebuild_redis_from_kafka():
    """
    Replay last 7 days of reaction events from Kafka
    More accurate than Cassandra if there were recent issues
    """
    logger.info("Rebuilding Redis by replaying Kafka events...")
    
    # Reset consumer to 7 days ago
    consumer = KafkaConsumer(
        'reaction.added',
        bootstrap_servers=['kafka:9092'],
        auto_offset_reset='earliest',
        consumer_timeout_ms=60000  # 1 minute timeout
    )
    
    # Seek to timestamp (7 days ago)
    seven_days_ago = int((datetime.utcnow() - timedelta(days=7)).timestamp() * 1000)
    consumer.poll()  # Initialize
    for partition in consumer.assignment():
        offset = consumer.offsets_for_times({partition: seven_days_ago})
        consumer.seek(partition, offset[partition].offset)
    
    # Replay events
    processed = 0
    for message in consumer:
        event = json.loads(message.value)
        
        # Apply event to Redis
        await redis.hincrby(
            f"post:{event['post_id']}:reactions",
            event['reaction_type'],
            1
        )
        await redis.zincrby(
            "posts:by_reactions",
            1,
            f"post:{event['post_id']}"
        )
        
        processed += 1
        if processed % 100000 == 0:
            logger.info(f"Replayed {processed} events")
    
    logger.info(f"Replay complete! Processed {processed} events")
```

---

## Redis Persistence (Backup)

Redis can save snapshots to disk for recovery:

### **RDB Snapshots (Point-in-Time)**

```redis
# Redis config
save 900 1      # Save if 1 key changed in 15 minutes
save 300 10     # Save if 10 keys changed in 5 minutes
save 60 10000   # Save if 10K keys changed in 1 minute

# Creates dump.rdb file on disk
```

**Recovery:**
```
1. Redis crashes
2. Restart Redis
3. Redis loads dump.rdb from disk
4. Most recent snapshot restored
5. May lose last few minutes of data (since last snapshot)
```

---

### **AOF (Append-Only File) - Every Command**

```redis
# Redis config
appendonly yes
appendfsync everysec  # Sync to disk every second

# Logs every write command
1706178600.123 HINCRBY post:123:reactions like 1
1706178600.456 ZINCRBY posts:by_reactions 1 post:123
1706178601.789 HINCRBY post:456:reactions love 1
```

**Recovery:**
```
1. Redis crashes
2. Restart Redis
3. Redis replays AOF file (all commands)
4. Full state restored
5. Maximum 1 second of data loss
```

---

## High Availability Setup (Production)

### **Facebook-Scale Redis Deployment:**

```
┌────────────────────────────────────────────────────────┐
│              Redis Cluster (50+ servers)               │
│                                                        │
│  Shard 1        Shard 2        Shard 3    ...Shard 25 │
│  ┌────────┐    ┌────────┐    ┌────────┐   ┌────────┐ │
│  │Master 1│    │Master 2│    │Master 3│   │Master25│ │
│  │32GB RAM│    │32GB RAM│    │32GB RAM│   │32GB RAM│ │
│  └────┬───┘    └────┬───┘    └────┬───┘   └────┬───┘ │
│       │             │             │             │     │
│       ↓             ↓             ↓             ↓     │
│  ┌────────┐    ┌────────┐    ┌────────┐   ┌────────┐ │
│  │Replica1│    │Replica2│    │Replica3│   │Replica │ │
│  └────────┘    └────────┘    └────────┘   └────────┘ │
│       ↓             ↓             ↓             ↓     │
│  ┌────────┐    ┌────────┐    ┌────────┐   ┌────────┐ │
│  │Replica │    │Replica │    │Replica │   │Replica │ │
│  │  (2nd) │    │  (2nd) │    │  (2nd) │   │  (2nd) │ │
│  └────────┘    └────────┘    └────────┘   └────────┘ │
└────────────────────────────────────────────────────────┘

Total: 25 masters + 50 replicas = 75 servers
Capacity: 800GB (25 * 32GB)
Can lose any 2 servers per shard without data loss
```

**Redundancy:**
- Each master has 2 replicas
- If master fails → replica 1 promotes
- If replica 1 fails → replica 2 takes over
- Can tolerate 2 simultaneous failures per shard

---

## Complete Failure Handling Flow

### **Timeline: Master 2 Fails**

```
Time: 00:00
Master 2 (Server B) crashes (hardware failure)
Keys in slots 5462-10922 unavailable

Time: 00:15
Sentinel/Cluster Manager detects failure
(missed 3 heartbeat pings)

Time: 00:30
Replica 2 promoted to Master
Cluster topology updated
All nodes notified

Time: 00:31
Applications reconnect to new Master 2
post:123:reactions available again ✅

Time: 00:35
New replica provisioned (Server G)
Starts syncing from new Master 2

Time: 05:00
Full replication complete
Redundancy restored ✅

Total downtime for affected keys: 31 seconds
```

---

## Monitoring & Alerts

**What to monitor:**

```python
# Redis health checks
redis_metrics = {
    "cluster_state": "ok",  # or "fail"
    "cluster_slots_ok": 16384,
    "cluster_slots_pfail": 0,  # Partially failed
    "cluster_slots_fail": 0,    # Failed
    
    # Per-node metrics
    "memory_used": "28GB / 32GB",
    "memory_fragmentation_ratio": 1.2,
    "evicted_keys": 0,
    "connected_clients": 1250,
    "ops_per_sec": 125000,
    
    # Replication lag
    "replica_lag_seconds": 0.5,  # < 1 second is good
    
    # Persistence
    "last_save_time": "2025-01-15 10:30:00",
    "rdb_changes_since_last_save": 45231
}
```

**Alerts:**
```
🔴 CRITICAL: Redis master down
🔴 CRITICAL: Redis cluster has failed slots
🟡 WARNING: Memory usage > 90%
🟡 WARNING: Replication lag > 5 seconds
🟡 WARNING: Evictions occurring (memory full)
```

---

## Summary

| Aspect | Single Server | Redis Cluster |
|--------|---------------|---------------|
| **Servers** | 1 | 3-100+ servers |
| **Capacity** | Limited to 1 machine's RAM | TBs of data |
| **Redundancy** | None | Multiple replicas |
| **Failover** | Manual | Automatic (30s) |
| **Downtime if crash** | Until manually restarted | ~30 seconds |
| **Data distribution** | All on one server | Sharded across cluster |
| **Facebook scale** | ❌ Not suitable | ✅ Required |

**Key Takeaways:**

1. **Redis Cluster = Multiple Servers** (typically 3-100+ for large scale)
2. **Data is sharded** across masters using hash slots
3. **Each master has replicas** for redundancy
4. **Automatic failover** in 30 seconds if master fails
5. **Application doesn't fail** if Redis goes down (degraded mode)
6. **Rebuild from Cassandra or Kafka** when Redis recovers
7. **Multiple layers of redundancy** prevent data loss

For Facebook scale, you'd have **50-100 Redis servers** in a cluster with 2-3 replicas each!

Does this clarify how Redis clustering works and what happens when it goes down?
