## Design Instagram Stories
Design a system that allows users to post ephemeral content (photos/videos) that disappears after 24 hours. The system should support:

- Posting stories (photos, videos, text overlays, filters)
- Viewing stories from people you follow
- Story highlights (saving stories permanently to profile)
- Story analytics (views, interactions)
- Real-time updates when new stories are posted
- Global scale (1B+ users, millions of stories posted daily)

## clarifying questions 
basically i think i also need to think beforehand like what i need to calc or how im gonna design so i need to ask here
1) what is the average photo/video size? for calculation
2) is there like a close friends feature too that restricts story view?
3) is there a limit to how many stories user can post or save?
4) the growth rate over the years?
5) DAU? peak concurrent number of users?

## answers
**1) Average media sizes:**
- Photos: ~2-3 MB (compressed for mobile)
- Videos: ~10-15 MB (15-30 second clips, mobile optimized)
- Mix: ~70% photos, 30% videos

**2) Close friends feature:**
- Yes, assume there's a "Close Friends" feature where users can share to a subset (~10-50 people typically)
- Also consider privacy levels: Public, Friends, Close Friends, Custom lists

**3) Story limits:**
- Posting: No hard limit, but typically users post 1-5 stories per day
- Highlights: Unlimited saves to profile highlights
- Individual story limit: Let's say max 10 photos/videos per story sequence

**4) Growth rate:**
- Assume 10-15% YoY user growth
- Story feature specifically grows faster (~20-30% YoY) as it's still relatively newer

**5) Scale assumptions:**
- Total users: ~2B
- DAU: ~500M-1B  
- Peak concurrent: ~100-200M during prime hours
- Stories posted: ~500M per day globally
- Story views: ~10B per day

**Additional assumptions:**
- Average user follows ~200 people
- Peak traffic during evening hours in major regions
- 80% mobile traffic

## functional requirements
User: 
- authentication
- follower and followed list

Media: 
- post, view, save story

Analytics: 
- views (aggregate function)
- interactions

## better
**User Management:**
- User authentication & authorization
- Follow/unfollow users
- Privacy settings (public, friends, close friends, custom lists)

**Story Lifecycle:**
- Create stories (photo/video upload with filters, text, stickers)
- View stories (chronological feed from followed users) 
- Auto-delete stories after 24 hours
- Save stories to Highlights (permanent profile collection)

**Story Interactions:**
- View story (increment view count)
- React to stories (heart, laugh, etc.)
- Reply to stories (DM to story creator)
- Share stories (repost to own story)

**Content Discovery:**
- Story feed (stories from people you follow)
- Story ring indicators (show who has new stories)
- Search within story highlights

**Analytics & Insights:**
- View counts and viewer list
- Interaction metrics (replies, shares, reactions)
- Story performance insights for creators

**What you were missing:**
- **Privacy controls** (biggest gap - very important for Meta)
- **Story interactions** beyond just viewing
- **Content discovery/feed** functionality  
- **Story sharing/reposting**
- **Auto-deletion mechanism** (core to Stories concept)

**Nice additions to consider:**
- Push notifications for new stories
- Story creation tools (filters, AR effects)
- Story polls/questions/quizzes

This shows you understand the full user journey, not just basic CRUD operations.

## Non functional
btw its giga -> tera -> peta (GTP) remember chatgpt

data:
story post = 500 10^6 * 500 10^6 * (0.7 x 3x 10^6 + 0.3 * 15 *x 10^6) = 0.25 * 10^18 (10^6) = 0.25 * 10^24 byte per day of stored data that is 7.5 x 10^24 byte per month
story view = i think its just read so we dont have to calc

im thinking highly available eventual consistency 

but thats just absurd i miscalc like i thought each user posts 500 million stories but i dont have to worry about dau here.

**Scale & Performance:**
- **Stories posted:** 500M/day 
- **Story views:** ~10B/day (20:1 read/write ratio)
- **Peak concurrent users:** 200M
- **Story load latency:** <500ms globally
- **Feed refresh latency:** <1s

**Storage Calculations:**
```
Daily story data = 500M stories × average size
Average size = (0.7 × 3MB) + (0.3 × 15MB) = 6.6MB
Daily storage = 500M × 6.6MB = 3.3 PB/day
Monthly storage = ~100 PB/month
```
*But stories delete after 24h, so steady state ~3-4 PB*

**Availability & Consistency:**
- **Availability:** 99.95% uptime
- **Consistency:** Eventual consistency for views/analytics
- **Strong consistency:** For story posting/deletion
- **Partition tolerance:** System must remain available during network splits

**Additional Requirements:**
- **Security:** End-to-end encryption for private stories
- **Compliance:** GDPR deletion, content moderation
- **Mobile optimization:** Adaptive bitrate, offline viewing
- **Global reach:** <100ms latency via CDN
- **Scalability:** Handle 50% traffic spikes during events
- **Data retention:** 24h auto-deletion, audit logs for 90 days
- **Fault tolerance:** Auto-failover, graceful degradation

**Cost optimization:** Tiered storage (hot → warm → cold → delete)

## high-level
# Instagram Stories - High Level System Design

## Architecture Overview

```
┌─────────────┐    ┌──────────────┐    ┌─────────────────┐
│   Mobile    │────│ Load Balancer│────│   API Gateway   │
│   Client    │    │   (nginx)    │    │  (Rate Limiting)│
└─────────────┘    └──────────────┘    └─────────────────┘
                                                │
                   ┌────────────────────────────┼────────────────────────────┐
                   │                            │                            │
              ┌────────────┐             ┌─────────────┐              ┌──────────────┐
              │    Auth    │             │   Story     │              │Social Graph  │
              │  Service   │             │  Service    │              │   Service    │
              │            │             │             │              │              │
              └────────────┘             └─────────────┘              └──────────────┘
                   │                            │                            │
              ┌────────────┐             ┌─────────────┐              ┌──────────────┐
              │User Cache  │             │Story Cache  │              │Graph Cache   │
              │  (Redis)   │             │  (Redis)    │              │  (Redis)     │
              └────────────┘             └─────────────┘              └──────────────┘
                   │                            │                            │
              ┌────────────┐             ┌─────────────┐              ┌──────────────┐
              │  User DB   │             │ Stories DB  │              │Relationships │
              │(PostgreSQL)│             │(Cassandra)  │              │  DB (Neo4j)  │
              └────────────┘             └─────────────┘              └──────────────┘
                                                │
                                    ┌───────────┼───────────┐
                                    │                       │
                            ┌───────────────┐      ┌───────────────┐
                            │    Media      │      │   Timeline    │
                            │  Processing   │      │   Service     │
                            │   Service     │      │               │
                            └───────────────┘      └───────────────┘
                                    │                       │
                            ┌───────────────┐      ┌───────────────┐
                            │      S3       │      │Timeline Cache │
                            │   Storage     │      │   (Redis)     │
                            │      +        │      └───────────────┘
                            │     CDN       │              │
                            └───────────────┘      ┌───────────────┐
                                                   │  Timeline DB  │
                                                   │  (Cassandra)  │
                                                   └───────────────┘
```

## Component Design Decisions

### 1. **Load Balancer: Nginx**

**Choice:** Nginx with geographic routing

**Why Nginx:**
- ✅ High performance (handles 100K+ concurrent connections)
- ✅ Built-in health checks and failover
- ✅ SSL termination
- ✅ Geographic routing for global CDN

**Alternatives:**
- **HAProxy**: More advanced load balancing algorithms, but less integrated ecosystem
- **Cloud LB (ALB/ELB)**: Managed service, but vendor lock-in and higher latency
- **Envoy**: Service mesh benefits, but added complexity for this use case

### 2. **API Gateway: Custom Service**

**Choice:** Custom API Gateway with rate limiting

**Why Custom:**
- ✅ Instagram-specific optimizations
- ✅ Fine-grained rate limiting per user type
- ✅ Custom authentication integration
- ✅ Request routing based on story privacy levels

**Alternatives:**
- **Kong/AWS API Gateway**: Feature-rich but generic, higher latency
- **Direct service calls**: Lower latency but no centralized policies

### 3. **Authentication Service: Separate Microservice**

**Choice:** Dedicated auth service with JWT tokens

**Why Separate:**
- ✅ Single responsibility principle
- ✅ Independent scaling
- ✅ Centralized session management
- ✅ Easy integration with other Meta services

**Database Choice:** PostgreSQL
- ✅ ACID compliance for user accounts
- ✅ Rich query capabilities for user profiles
- ✅ Mature ecosystem and tooling

**Cache:** Redis for session storage
- ✅ Fast session lookups (sub-ms)
- ✅ TTL support for session expiration
- ✅ High availability clustering

### 4. **Story Service: Core Business Logic**

**Choice:** Microservice handling story CRUD operations

**Database Choice:** Cassandra
- ✅ **Time-series data**: Stories are naturally time-ordered
- ✅ **Write-heavy**: Optimized for high write throughput
- ✅ **TTL support**: Built-in expiration for 24h stories
- ✅ **Horizontal scaling**: Easy to add nodes for growth
- ✅ **No complex joins**: Stories are mostly key-value lookups

**Alternatives:**
- **MySQL**: ACID compliance, but poor horizontal scaling for writes
- **MongoDB**: Good for JSON documents, but weaker consistency guarantees
- **DynamoDB**: Serverless scaling, but vendor lock-in and limited query flexibility

**Cache Strategy:** Redis write-through
- Story metadata cached on write
- Fast retrieval for story details
- 24h TTL matching story expiration

### 5. **Social Graph Service: Relationship Management**

**Choice:** Neo4j graph database

**Why Neo4j:**
- ✅ **Natural graph queries**: "Find followers of followers" in single query
- ✅ **Performance**: Optimized for relationship traversals
- ✅ **Flexibility**: Easy to add new relationship types (close friends, blocked users)
- ✅ **Privacy features**: Complex privacy rule evaluation

**Alternatives:**
- **PostgreSQL with adjacency lists**: Cheaper but poor performance for complex queries
- **Redis sets**: Fast but limited query capabilities
- **Separate follower/following tables**: Simple but expensive joins at scale

**Cache Strategy:** Redis for hot relationships
- Cache follower counts and recent followers
- Aggressive caching for celebrity accounts
- Cache privacy settings for fast access control

### 6. **Media Processing Service: Async Pipeline**

**Choice:** Kubernetes-based processing pipeline

**Why This Approach:**
- ✅ **Async processing**: Don't block user uploads
- ✅ **Auto-scaling**: Handle upload spikes
- ✅ **Multiple formats**: Generate thumbnails, compress videos
- ✅ **Fault tolerance**: Retry failed processing jobs

**Storage Choice:** S3 + CloudFront CDN
- ✅ **Durability**: 99.999999999% (11 9's)
- ✅ **Global distribution**: Sub-100ms latency worldwide  
- ✅ **Cost effective**: Automatic tiering to cheaper storage
- ✅ **Lifecycle policies**: Auto-delete after 24 hours

**Alternatives:**
- **Google Cloud Storage**: Similar features, but less mature ecosystem
- **On-premise**: Full control but massive infrastructure investment
- **Multiple CDNs**: Better performance but complexity

### 7. **Timeline Service: Hybrid Push/Pull**

**Choice:** Smart fanout based on follower count

**Algorithm:**
```
if (follower_count < 1000):
    push_to_all_followers()  # Write timeline entries immediately
else:
    pull_on_demand()         # Compute timeline when requested
```

**Why Hybrid:**
- ✅ **Resource optimization**: Push is expensive for celebrities
- ✅ **Latency optimization**: Push gives instant feeds for most users
- ✅ **Cost effective**: Reduces database write load by 80%

**Database Choice:** Cassandra for timelines
- ✅ **Time-series**: Natural fit for timeline data
- ✅ **Write optimization**: Efficient for fanout operations
- ✅ **TTL**: Auto-cleanup of old timeline entries

**Cache Strategy:** Redis for hot timelines
- Pre-warm popular users' timelines
- Cache recent timeline entries
- Geographic distribution of timeline cache

**Alternatives:**
- **Full Push**: Simple but expensive (1 celebrity post = 10M writes)
- **Full Pull**: Cheap writes but slow timeline generation
- **Event sourcing**: More complex but better auditability

## Data Flow

### Story Upload Flow:
1. Client requests pre-signed S3 URL from Story Service
2. Client uploads media directly to S3
3. Client notifies Story Service of successful upload
4. Media Processing Service generates thumbnails/transcodes video
5. Timeline Service performs fanout (push/pull decision)
6. CDN caches processed media globally

### Story Viewing Flow:
1. Client requests timeline from Timeline Service  
2. Timeline Service checks Redis cache first
3. On cache miss, queries Cassandra and populates cache
4. Client receives story metadata + CDN URLs
5. Client streams media directly from CDN
6. Analytics service logs view event asynchronously

## Scalability Considerations

- **Database Sharding**: Stories sharded by creator_id, Timelines by viewer_id
- **Cache Warming**: Proactive loading of popular content
- **Geographic Distribution**: Services deployed in multiple regions
- **Circuit Breakers**: Prevent cascade failures between services
- **Auto-scaling**: Kubernetes HPA based on CPU/memory/queue depth

## Key Metrics to Monitor

- **Latency**: Timeline load < 500ms, Story view < 200ms
- **Throughput**: 500M story posts/day, 10B story views/day  
- **Availability**: 99.95% uptime target
- **Cache Hit Rate**: >95% for timeline requests
- **Error Rates**: <0.1% for critical user flows
