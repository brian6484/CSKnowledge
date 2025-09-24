
## Design a Real-time Chat System

**Problem Statement:**
Design a real-time messaging system similar to WhatsApp or Slack that allows users to send and receive messages instantly. The system should support both one-on-one conversations and group chats.

**Initial Context:**
- The system needs to handle millions of active users
- Users should be able to send text messages, images, and files
- Messages should be delivered in real-time when users are online
- The system should work across multiple devices (mobile, web, desktop)
- Messages should be persistent and retrievable when users come back online

## Clarifying questions
**1) Scale & Geography:**
- 5M DAU is a good baseline
- Peak concurrent users: 1M (not 10M - that would be unrealistic concurrency)
- Geographic distribution: Global, but concentrated in North America and Europe initially
- Plan for 10x growth over 2 years

**2) Message Sizes:**
- Average text message: 100 bytes
- 80% of messages are text, 15% images, 5% files
- Average image after compression: 200KB
- Average file: 2MB
- File upload limit: 100MB

**3) Notifications:**
- Yes, push notifications are required
- Support for mobile push (iOS/Android) and web notifications

**4) Group Size:**
- Small groups: up to 50 members (majority of groups)
- Large groups: up to 1,000 members (for team/community chats)
- No broadcast channels needed initially

**5) Status Features:**
- Read receipts: Yes (show when message is delivered and read)
- Online status: Yes (online, last seen)
- Typing indicators: Yes

**Additional context I'm providing:**
- Message retention: Permanent storage
- Acceptable latency: <100ms for real-time delivery
- Availability target: 99.9% uptime

## Functional req
No, **functional requirements** are not calculations! Let me clarify:

**Functional Requirements** = **WHAT** the system should do (the features/capabilities)
**Non-Functional Requirements** = **HOW WELL** it should do them (performance, scale, reliability)

## Functional Requirements (Features):

**Core Messaging:**
- Users can send/receive text messages in real-time
- Users can send/receive images and files (up to 100MB)
- Support one-on-one conversations
- Support group chats (up to 1,000 members)

**User Management:**
- User registration and authentication
- User profiles and contact management
- Add/remove users from groups
- CRUD groups

**Message Features:**
- Message delivery confirmation
- Read receipts (delivered/read status)
- Message history/persistence
- Search through message history

**Presence & Status:**
- Show user online status
- Show "last seen" timestamp
- Typing indicators

**Notifications:**
- Push notifications for offline users
- Real-time notifications for online users
- Offline users: Need to route through external push notification services, store pending notifications
Online users: Direct delivery through existing WebSocket connections, no external services needed

**Multi-device Support:**
- Sync conversations across devices
- Multiple device login support

**Multi region support**

## Non-functional
Eventual consistency makes sense for chat (brief delays in message ordering across regions is acceptable)
✅ Availability over strong consistency: Right call for user experience.

Good thinking on CAP theorem and the calculations! Let me give you feedback:

## CAP Theorem Analysis:
✅ **Correct**: Partition tolerance is unavoidable in distributed systems
✅ **Good choice**: Eventual consistency makes sense for chat (brief delays in message ordering across regions is acceptable)
✅ **Availability over strong consistency**: Right call for user experience

**Messages per day**: 5M users × 10 messages = 50M messages ✅

**Storage per message**:
- Text: 100 bytes × 0.8 = 80 bytes
- Images: 200KB × 0.15 = 30KB  
- Files: 2MB × 0.05 = 100KB
- **Total per message**: ~130KB (your 10^4 approximation is close)

**Daily storage**: 50M × 130KB = **6.5TB/day** ✅
**Monthly**: ~200TB/month (your 6TB seems low)
 **With replication**: ~600TB/month total

*Units*: Kilo → Mega → **Giga** → **Tera** → **Peta**

## Other Non-Functional Requirements you're missing:

**Performance:**
- Message delivery latency < 100ms
- Support 1M concurrent users
- Handle 500K messages/second at peak

**Scalability:**
- Horizontal scaling capability
- Plan for 10x growth

**Reliability:**
- 99.9% uptime
- Data backup/recovery
- Fault tolerance

**Security:**
- End-to-end encryption
- Authentication/authorization

## High-Level System Design

```
[Mobile/Web Clients] 
        ↓
[Load Balancer/API Gateway]
        ↓
[Microservices Pool]
   ├── Auth Service (HTTPS)
   ├── Message Service (WebSocket + HTTPS) 
   ├── User Service (HTTPS)
   ├── Notification Service (HTTPS)
   └── Media Service (HTTPS)
        ↓
[Data Layer]
   ├── Redis Cache (Online users, sessions)
   ├── Message DB (NoSQL - Cassandra/HBase)
   ├── User DB (SQL - PostgreSQL)
   ├── File Storage (AWS S3/CDN)
   └── Message Queues (Kafka/RabbitMQ)
```

## 2. Detailed Component Architecture

### **Connection Management**
```
Client → Load Balancer → WebSocket Server Pool (part of Message Service)
                              ↓
                         Redis (store connection mapping)
Redis: user_id → {server_id, connection_id, timestamp}
```
We use Redis for connection management because we need sub-millisecond lookups to route messages between servers in real-time. Database would be too slow, and in-memory only doesn't work in a distributed system where users connected to different servers need to communicate.

Thats also how we know the online status of users cuz when user comes online
```
1. User opens app → Establishes WebSocket connection
2. WebSocket server stores: Redis.set("user:12345", {
   server_id: "ws-server-3",
   connection_id: "conn_abc123", 
   timestamp: 1640995200,
   status: "online"
})

def is_user_online(user_id):
    user_data = redis.get(f"user:{user_id}")
    if user_data:
        # Check if timestamp is recent (within last 30 seconds)
        if (current_time - user_data.timestamp) < 30:
            return True
    return False

# Example usage:
if is_user_online("12345"):
    send_via_websocket(message)
else:
    queue_for_push_notification(message)
```

and when user goes offline
```
1. WebSocket connection closes (user closes app, network issue, etc.)
2. WebSocket server detects disconnection
3. Redis.delete("user:12345") or Redis.set("user:12345", {status: "offline", last_seen: timestamp})
```

### **Message Flow - 1-on-1 Chat**
```
1. Sender → Message Service (via WebSocket)
2. Message Service:
   - Validates message
   - Stores in Message DB
   - Checks recipient status in Redis
3a. If recipient ONLINE:
    - Forward message via WebSocket
    - Send delivery confirmation
3b. If recipient OFFLINE:
    - Queue for push notification
    - Store as unread message
```

```
Scenario: Alice wants to send "Hello!" to Bob

1. Alice sends message → Message Service receives it

2. Message Service checks: is_user_online("bob_123")
   → Redis lookup: "user:bob_123" 
   → Returns: {status: "online", server_id: "ws-server-2", last_ping: 1640995190}
   → Current time: 1640995200 (10 seconds ago = ONLINE)

3. Since Bob is ONLINE:
   → Route message to "ws-server-2"
   → Server-2 finds Bob's WebSocket connection
   → Sends message directly: websocket.send("Hello!")

4. If Bob was OFFLINE:
   → Store message in pending_messages queue
   → Send push notification via FCM/APNS
   → When Bob comes online later, deliver queued messages
```

some edge case is for stale connection/multi devices.

### **Message Flow - Group Chat**
```
Small Groups (<50 users):
Message → Fan-out to each member → Individual delivery

Large Groups (50+ users):
Message → Store in DB → Members pull when they connect
Use message sequence numbers for sync
```

## 3. Database Schema Design

### **Message Storage (NoSQL - Cassandra/HBase)**
```
Table: messages
Partition Key: chat_id + date (YYYYMMDD)
Clustering Key: timestamp + message_id
Columns: sender_id, content, message_type, file_url, status

Example Key: "chat_123_20241201:1640995200:msg_456"
```

## **Partition Key vs Clustering Key**

### **Partition Key:**
- **Purpose**: Determines **which physical node** the data is stored on
- **Distribution**: Used for **horizontal sharding** across cluster
- **Uniqueness**: Data with same partition key goes to **same node**

### **Clustering Key:**
- **Purpose**: Determines **ordering of rows within a partition**
- **Sorting**: Rows are **sorted by clustering key** within each partition
- **Retrieval**: Enables **range queries** within a partition

## **Concrete Example for Chat Messages:**

```sql
CREATE TABLE messages (
    chat_id text,           -- Partition Key
    date text,              -- Partition Key  
    timestamp bigint,       -- Clustering Key
    message_id uuid,        -- Clustering Key
    sender_id text,
    content text,
    message_type text,
    PRIMARY KEY ((chat_id, date), timestamp, message_id)
);
```

## **How This Works:**

### **Partition Key: `(chat_id, date)`**
```
chat_123_20241201 → Node A
chat_123_20241202 → Node B  
chat_456_20241201 → Node C
```
- All messages for **chat_123 on Dec 1st** go to **same node**
- **Distributes load** across multiple nodes by chat and date

### **Clustering Key: `(timestamp, message_id)`**
```
Within Node A (chat_123_20241201):
├── 09:00:00:msg_001
├── 09:15:30:msg_002  
├── 10:45:22:msg_003
└── 11:20:15:msg_004
```
- Messages are **automatically sorted** by time within partition
- **Fast range queries**: "Get messages between 9AM-10AM"

## **Why This Design is Powerful:**

### **Efficient Queries:**
```sql
-- FAST: Query within single partition
SELECT * FROM messages 
WHERE chat_id = 'chat_123' AND date = '20241201' 
AND timestamp > 1640995200;

-- SLOW: Query across multiple partitions  
SELECT * FROM messages 
WHERE timestamp > 1640995200;  -- No partition key!
```

### **Scalability:**
- **Hot chats** get distributed across dates
- **Popular chats** don't overwhelm single node
- **Time-based queries** are efficient within each partition

## **Real-World Benefits:**

```
Chat with 1M messages:
├── 2024-12-01: 50K messages (Node A)
├── 2024-12-02: 50K messages (Node B)  
├── 2024-12-03: 50K messages (Node C)
└── ...

Query "last 100 messages today" = Single node lookup + sorted retrieval
```

**Partition Key** = **Where** the data lives
**Clustering Key** = **How** the data is organized within that location

### **User Data (SQL - PostgreSQL)**
```
users: user_id, username, email, created_at
chats: chat_id, chat_type, created_at, updated_at  
chat_members: chat_id, user_id, role, joined_at
```

### **Cache Layer (Redis)**
```
online_users: user_id → {server_id, last_seen}
user_sessions: session_token → user_data
unread_counts: user_id → {chat_id: count}
```
so 1 single redis server isnt enuff. lets make a cluster.  Masters handle all reads/writes by default, replicas provide backup. If a master fails, its replica automatically becomes the new master within ~10 seconds. This gives us both horizontal scaling and fault tolerance.
```
Redis Cluster (6 nodes):
├── Master-1: hash slots 0-5461     (online_users data)
├── Master-2: hash slots 5462-10922 (user_sessions data)  
├── Master-3: hash slots 10923-16383 (unread_counts data)
├── Slave-1: replica of Master-1
├── Slave-2: replica of Master-2
└── Slave-3: replica of Master-3
```

# Redis automatically distributes keys based on hash slots
"user:12345" → CRC16("user:12345") % 16384 → slot 8234 → Master-2
"session:abc123" → CRC16("session:abc123") % 16384 → slot 3456 → Master-1
"unread:67890" → CRC16("unread:67890") % 16384 → slot 12000 → Master-3

## **Alternative: Functional Sharding (Separate by Data Type)**
it might be better cuz each cluster might have diff access pattern and scaling needs

### **Dedicated Redis Clusters for Each Use Case:**
```
Redis Cluster 1 (Online Users):
├── redis-online-1 (master)
├── redis-online-2 (replica)

Redis Cluster 2 (User Sessions):  
├── redis-session-1 (master)
├── redis-session-2 (replica)

Redis Cluster 3 (Unread Counts):
├── redis-unread-1 (master) 
├── redis-unread-2 (replica)
```

### **Application Layer Routing:**
```python
class CacheManager:
    def __init__(self):
        self.online_cache = redis.Redis(host='redis-online-cluster')
        self.session_cache = redis.Redis(host='redis-session-cluster')  
        self.unread_cache = redis.Redis(host='redis-unread-cluster')
    
    def set_user_online(self, user_id, data):
        self.online_cache.set(f"user:{user_id}", data)
    
    def get_session(self, token):
        return self.session_cache.get(f"session:{token}")
    
    def update_unread_count(self, user_id, chat_id, count):
        self.unread_cache.hset(f"unread:{user_id}", chat_id, count)
```

## **Scaling Numbers for 5M DAU:**

### **Memory Requirements:**
```
Online Users: 1M concurrent × 200 bytes = 200MB
User Sessions: 5M users × 1KB = 5GB  
Unread Counts: 5M users × 10 chats × 50 bytes = 2.5GB
Total: ~8GB (with overhead ~15GB)
```

### **Recommended Setup:**
```
Redis Cluster: 6 nodes (3 masters + 3 replicas)
Each master: 8GB RAM, handles ~2.5GB data + overhead
Handles 100K+ ops/second across cluster
```

## **For System Design Interview:**

**Good Answer:**
> "We'll use Redis Cluster with 6 nodes (3 masters, 3 replicas) to handle our cache load. Data is automatically sharded across nodes using hash slots. For 5M DAU, this gives us ~100K+ operations/second capacity and handles our 15GB total cache requirement."

**Even Better:**
> "We might consider functional sharding - separate Redis clusters for online presence vs session data, as they have different access patterns and scaling needs."

## **Benefits of This Approach:**
- ✅ **Automatic sharding** across nodes
- ✅ **High availability** with replicas  
- ✅ **Horizontal scaling** - add more nodes as needed
- ✅ **Different TTL policies** per data type
- ✅ **Independent scaling** of different cache types

## 4. Real-Time Communication

### **WebSocket Connection Pool**
```
[WebSocket Gateway Layer]
├── Connection Manager (tracks user → server mapping)
├── Message Router (routes to correct server/user)
└── Heartbeat Manager (detects disconnections)
```

### **Message Delivery Strategy**
- **Online users**: Direct WebSocket delivery
- **Offline users**: Store in message queue + push notification
- **Message ordering**: Use vector clocks or sequence numbers
- **Delivery guarantees**: At-least-once delivery with deduplication

## 5. Scaling Considerations

### **Horizontal Scaling**
- **Message Service**: Stateless, scale based on concurrent connections
- **Database Sharding**: Shard by chat_id for group messages, user_id for direct messages
- **WebSocket Servers**: Use consistent hashing for connection distribution

### **Data Partitioning**
```
Messages Sharding Strategy:
- Direct messages: Hash(min(user1_id, user2_id))
- Group messages: Hash(chat_id)
- Time-based partitioning for archival
```

## 6. Key Design Decisions & Trade-offs

### **Consistency vs Availability**
- **Choice**: Eventual consistency for message ordering
- **Trade-off**: Slight delays in message ordering vs system availability
- **Implementation**: Vector clocks for ordering, eventual synchronization

### **Push vs Pull for Group Messages**
- **Small groups**: Push (real-time, higher server load)
- **Large groups**: Pull/Poll (lower server load, slight delay)
- **Threshold**: 50 users

### **Storage Strategy**
- **Hot data** (recent messages): In-memory cache + primary DB
- **Cold data** (old messages): Archive to cheaper storage
- **Retention**: Keep recent 6 months in fast storage

## 7. Estimated Resource Requirements

### **Storage**
- **Daily message volume**: 50M messages = ~6.5TB/day
- **Monthly storage**: ~200TB/month
- **With replication**: ~600TB/month total

### **Compute**
- **Concurrent users**: 1M peak
- **WebSocket servers**: ~200 servers (5K connections each)
- **Message throughput**: 500K messages/second peak

### **Bandwidth**
- **Peak bandwidth**: ~10Gbps
- **CDN usage**: Media files served via CDN to reduce origin load

