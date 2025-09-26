## scenario
for insta story data storage, wat id do we should as parition id?

## so why creator id as parition key? and not story id or timebased id?
If we do story or timebased id, when we do a query search based on the user, which is the normal functionality of viewing someone's stories, then we have to look up multiple parititions/servers for this search. While it distributes load evenly amongst partitions, the query becomes very expensive so I think we should use creator id.

But with creator id, with cheaper queries, there is this hotspot problem where a celebrity can post stories and all his stories are stored in just 1 parition/server. So single node becomes the bottleneck, making it hot parittion. For this, im not sure how to fix.

For this we have some solutions
1) aggressive caching (Redis/CDN) - based on the creator id, we can store that guy's stories in cache so that we dont have to search up db. Only the cache misses hit the hot partition
```py
def get_celebrity_stories(creator_id):
    # 99%+ cache hit rate for celebrities
    cached_stories = redis.get(f"user_stories:{creator_id}")
    if cached_stories:
        return cached_stories  # Served from cache, never hits Cassandra
    
    # Only cache misses hit the "hot" Cassandra partition
    stories = cassandra.query("SELECT * FROM stories WHERE creator_id = ?", creator_id)
    redis.setex(f"user_stories:{creator_id}", 3600, stories)  # Cache for 1 hour
    return stories

# Result: Celebrity's Cassandra partition gets 1% of reads instead of 100%
```

Hot Stories (celebrities): 
→ Cached aggressively in Redis/Memcached
→ Multiple cache replicas per region
→ CDN for media files

Normal Stories:
→ Standard Cassandra with creator_id partitioning
→ Regular cache TTL

2) read replicas - so u can have 1 primary db and multiple read replicas of that db. 
```py
def read_with_replica_routing(creator_id):
    if is_celebrity(creator_id):
        # Route reads across multiple replicas
        replica_nodes = get_replicas_for_partition(creator_id)  # [node1, node2, node3]
        chosen_replica = random.choice(replica_nodes)  # Load balance
        return chosen_replica.read(creator_id)
    else:
        # Normal routing for regular users
        return primary_node.read(creator_id)
```

## so why is cache better than read replicas?
tbc 
