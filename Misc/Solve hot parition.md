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


## how to configure cache
we can do this via **multi-layer caching strategy** to solve the celebrity hotspot problem. We wanna avoid hitting the db as much as possible

## **Multi-Layer Cache Architecture**

```
┌─────────────┐    ┌──────────────┐    ┌──────────────┐    ┌─────────────┐
│   Client    │───▶│     CDN      │───▶│  Redis L1    │───▶│ Cassandra   │
│   (Mobile)  │    │   (Media)    │    │ (Metadata)   │    │ (Database)  │
└─────────────┘    └──────────────┘    └──────────────┘    └─────────────┘
     Cache             Cache              Cache              Source of Truth
     TTL: 1hr          TTL: 24hr         TTL: 6hr           TTL: 24hr
```

## **Layer 1: CDN (Media Files)**

**Configuration:**
```nginx
# CloudFront/Fastly configuration
location /stories/media/* {
    proxy_cache stories_cache;
    proxy_cache_valid 200 24h;           # Cache successful responses
    proxy_cache_valid 404 1m;            # Don't cache missing files long
    
    # Celebrity content - aggressive caching
    if ($request_uri ~ "celebrity_(\d+)") {
        proxy_cache_valid 200 48h;       # Longer TTL for hot content
        add_header X-Cache-Status $upstream_cache_status;
    }
}

# Cache warming for celebrity posts
upstream backend {
    server story-service-1.internal:8080;
    server story-service-2.internal:8080;
    server story-service-3.internal:8080;
}
```

## **Layer 2: Application Cache (Redis)**

### **Redis Cluster Configuration:**
```yaml
# Redis cluster for story metadata
redis_config:
  cluster_enabled: true
  nodes:
    - redis-1.internal:6379
    - redis-2.internal:6379  
    - redis-3.internal:6379
  replication_factor: 2        # Each key stored on 2 nodes
  max_memory: 64GB
  max_memory_policy: allkeys-lru  # Evict least recently used
```

### **Cache Key Strategy:**
```python
class StoryCacheManager:
    def __init__(self):
        self.redis = RedisCluster(nodes=redis_nodes)
        
    def get_story_metadata(self, story_id):
        cache_key = f"story:meta:{story_id}"
        
        # Try cache first
        cached_data = self.redis.get(cache_key)
        if cached_data:
            return json.loads(cached_data)
        
        # Cache miss - read from database
        story = self.read_from_cassandra(story_id)
        
        # Cache with TTL based on popularity
        ttl = self.calculate_ttl(story.creator_id)
        self.redis.setex(cache_key, ttl, json.dumps(story))
        
        return story
    
    def calculate_ttl(self, creator_id):
        if self.is_celebrity(creator_id):
            return 6 * 3600    # 6 hours for celebrities
        elif self.is_popular(creator_id):
            return 2 * 3600    # 2 hours for popular users  
        else:
            return 30 * 60     # 30 minutes for regular users
```

### **Story Timeline Caching:**
```python
def get_user_timeline(self, user_id):
    timeline_key = f"timeline:{user_id}"
    
    # Check cache first
    cached_timeline = self.redis.lrange(timeline_key, 0, 19)  # Get 20 stories
    if cached_timeline:
        return [json.loads(story) for story in cached_timeline]
    
    # Generate timeline from database
    timeline = self.generate_timeline_from_db(user_id)
    
    # Cache the timeline (list of story metadata)
    pipe = self.redis.pipeline()
    pipe.delete(timeline_key)  # Clear old timeline
    for story in timeline:
        pipe.lpush(timeline_key, json.dumps(story))
    pipe.expire(timeline_key, 3600)  # 1 hour TTL
    pipe.execute()
    
    return timeline
```

## **Celebrity-Specific Cache Strategy**

### **Hot Story Pre-Loading:**
```python
class CelebrityStoryHandler:
    def __init__(self):
        self.hot_creators = set()  # Celebrity user IDs
        
    def on_story_posted(self, creator_id, story_data):
        if creator_id in self.hot_creators:
            # Aggressive pre-caching for celebrities
            self.preload_story_globally(story_data)
        else:
            # Standard caching for regular users
            self.cache_story_locally(story_data)
    
    def preload_story_globally(self, story_data):
        # Cache in multiple regions immediately
        regions = ['us-west', 'us-east', 'europe', 'asia']
        
        for region in regions:
            regional_redis = self.get_regional_redis(region)
            
            # Cache story metadata
            story_key = f"story:meta:{story_data.story_id}"
            regional_redis.setex(story_key, 6*3600, json.dumps(story_data))
            
            # Pre-warm CDN
            self.trigger_cdn_preload(story_data.media_url, region)
```

### **Cache Warming Jobs:**
```python
# Background job to warm celebrity caches
class CacheWarmingService:
    def warm_celebrity_timelines(self):
        # Get list of celebrities posting in last hour
        recent_celebrity_posts = self.get_recent_celebrity_posts()
        
        for post in recent_celebrity_posts:
            # Pre-populate follower timelines
            top_followers = self.get_top_followers(post.creator_id, limit=10000)
            
            for follower_batch in self.batch(top_followers, 1000):
                self.async_warm_timeline_batch(follower_batch, post)
    
    def async_warm_timeline_batch(self, followers, story):
        # Background job to update follower timelines
        for follower_id in followers:
            timeline_key = f"timeline:{follower_id}"
            
            # Add new story to beginning of cached timeline
            story_json = json.dumps(story.to_dict())
            self.redis.lpush(timeline_key, story_json)
            self.redis.ltrim(timeline_key, 0, 49)  # Keep only 50 stories
            self.redis.expire(timeline_key, 3600)
```

## **Cache Invalidation Strategy**

### **Story Expiration Handling:**
```python
class StoryExpirationHandler:
    def cleanup_expired_stories(self):
        # Run every minute
        expired_stories = self.get_expired_stories()
        
        for story in expired_stories:
            # Remove from all cache layers
            self.invalidate_story_cache(story.story_id)
            self.invalidate_timeline_caches(story.creator_id)
            self.trigger_cdn_purge(story.media_url)
    
    def invalidate_story_cache(self, story_id):
        # Remove from Redis cluster
        cache_keys = [
            f"story:meta:{story_id}",
            f"story:views:{story_id}",
            f"story:analytics:{story_id}"
        ]
        
        self.redis.delete(*cache_keys)
```

## **Performance Monitoring**

### **Cache Hit Rate Tracking:**
```python
class CacheMetrics:
    def track_cache_performance(self):
        # Monitor cache hit rates
        total_requests = self.get_total_story_requests()
        cache_hits = self.get_cache_hits()
        
        hit_rate = cache_hits / total_requests
        
        if hit_rate < 0.95:  # Alert if hit rate drops below 95%
            self.alert("Cache hit rate dropped to {:.2%}".format(hit_rate))
        
        # Track by celebrity vs regular users
        celebrity_hit_rate = self.get_celebrity_cache_hit_rate()
        regular_hit_rate = self.get_regular_cache_hit_rate()
        
        self.emit_metrics({
            'cache_hit_rate_overall': hit_rate,
            'cache_hit_rate_celebrity': celebrity_hit_rate,
            'cache_hit_rate_regular': regular_hit_rate
        })
```

## **Memory and Cost Optimization**

### **Cache Size Estimation:**
```python
# Memory planning for Redis cluster
story_metadata_size = 2KB  # JSON metadata per story
timeline_entry_size = 0.5KB  # Lightweight timeline entry

# For 500M active stories in cache:
total_story_cache = 500_000_000 * 2KB = 1TB

# For 500M user timelines (20 stories each):
total_timeline_cache = 500_000_000 * 20 * 0.5KB = 5TB

# Total Redis memory needed: ~6TB across cluster
# With replication factor 2: ~12TB total
```

### **Cache Eviction Policy:**
```redis
# Redis configuration for optimal eviction
maxmemory 64gb
maxmemory-policy allkeys-lru

# Custom eviction for celebrity content
# Use LFU (Least Frequently Used) for hot content
maxmemory-policy volatile-lfu
```

## **Regional Cache Distribution**

```python
class RegionalCacheManager:
    def __init__(self):
        self.regional_clusters = {
            'us-west': RedisCluster(['redis-usw-1', 'redis-usw-2']),
            'us-east': RedisCluster(['redis-use-1', 'redis-use-2']),
            'europe': RedisCluster(['redis-eu-1', 'redis-eu-2']),
            'asia': RedisCluster(['redis-asia-1', 'redis-asia-2'])
        }
    
    def get_story_with_fallback(self, story_id, user_region):
        # Try local region first
        local_cache = self.regional_clusters[user_region]
        story = local_cache.get(f"story:meta:{story_id}")
        
        if story:
            return json.loads(story)
        
        # Fallback to other regions
        for region, cache in self.regional_clusters.items():
            if region != user_region:
                story = cache.get(f"story:meta:{story_id}")
                if story:
                    # Replicate to local region for next time
                    local_cache.setex(f"story:meta:{story_id}", 3600, story)
                    return json.loads(story)
        
        # Final fallback to database
        return self.read_from_cassandra(story_id)
```

## **Key Configuration Numbers:**

- **CDN TTL**: 24 hours for media, 1 hour for metadata
- **Redis TTL**: 6 hours for celebrities, 30 minutes for regular users  
- **Cache Hit Target**: >95% overall, >99% for celebrity content
- **Memory**: ~12TB Redis cluster globally with replication
- **Preloading**: Top 10K followers get timeline pre-warmed

This multi-layer approach reduces Cassandra load by **99%+** while maintaining sub-100ms response times globally!
