# ZooKeeper: Complete Beginner's Guide
## The Basic Concept (ELI5)

Imagine you have **3 friends keeping a shared notebook**:

```
Friend 1 has Notebook Copy
Friend 2 has Notebook Copy  
Friend 3 has Notebook Copy
```

**Rules:**
1. When someone writes something, they tell the others
2. Everyone agrees before writing it down
3. If 2 out of 3 friends agree, it's official
4. If a friend disappears, the other 2 can still work

**This is basically ZooKeeper!** The "friends" are ZooKeeper servers, the "notebook" is the shared data.

---

## What is a ZooKeeper "Ensemble"?

**Ensemble = A group of ZooKeeper servers working together**

Think of it like a **committee** that votes on decisions.

```
┌─────────────────────────────────────┐
│      ZooKeeper Ensemble             │
│                                     │
│  ┌────────┐  ┌────────┐  ┌────────┐│
│  │  ZK-1  │  │  ZK-2  │  │  ZK-3  ││
│  │(Leader)│  │ (Vote) │  │ (Vote) ││
│  └────────┘  └────────┘  └────────┘│
│       ▲          ▲          ▲       │
│       └──────────┴──────────┘       │
│     They talk to each other         │
└─────────────────────────────────────┘
```

**Key facts:**
- Usually **3, 5, or 7 servers** (odd number)
- One is the **Leader** (elected automatically)
- Others are **Followers**
- They all sync data with each other
- Majority must agree for any write (this is called **quorum**)

### **Why Odd Numbers?**

```
3 servers: Need 2 to agree (can survive 1 failure)
5 servers: Need 3 to agree (can survive 2 failures)
7 servers: Need 4 to agree (can survive 3 failures)

4 servers: Need 3 to agree (can survive 1 failure) ← Same as 3!
6 servers: Need 4 to agree (can survive 2 failures) ← Same as 5!
```

**Odd numbers = no wasted capacity**

---

## What is a ZooKeeper "Client"?

**Client = Your application (my services) that talks to ZooKeeper**

Your application doesn't run ZooKeeper - it **connects to** ZooKeeper.

```
┌──────────────────────┐
│   Your Application   │  ← This is the "client"
│   (Python/Java/Go)   │
│                      │
│  ┌────────────────┐  │
│  │ ZK Client Lib  │  │  ← Library you import
│  └────────┬───────┘  │
└───────────┼──────────┘
            │ TCP connection
            ▼
┌─────────────────────────┐
│   ZooKeeper Ensemble    │  ← The servers
│    (3-5 machines)       │
└─────────────────────────┘
```

---

### **The Code (Python example):**

```python
# Import the client library
from kazoo.client import KazooClient

# Create a client (your app)
zk = KazooClient(hosts='10.0.1.1:2181,10.0.1.2:2181,10.0.1.3:2181')
#                        ↑ Addresses of the 3 ZooKeeper servers in ensemble

# Connect to the ensemble
zk.start()

# Now you can use ZooKeeper!

# Check if table 5 is reserved
if zk.exists('/reservations/table5'):
    print("Table 5 is reserved!")
else:
    print("Table 5 is available!")
    # Reserve it!
    zk.create('/reservations/table5', b'John Smith, 7pm')

# When app shuts down
zk.stop()
```

---

## How It Actually Works (Step by Step)

### **Scenario: Register Your Server as "Alive"**

```
Step 1: Your API server starts up
┌─────────────────┐
│ API Server      │
│ 10.0.2.5:8080   │
└────────┬────────┘
         │
         │ 1. Connect to ZooKeeper
         ▼
┌─────────────────────────┐
│  ZooKeeper Ensemble     │
│  ┌────┐ ┌────┐ ┌────┐  │
│  │ZK-1│ │ZK-2│ │ZK-3│  │
│  └────┘ └────┘ └────┘  │
└─────────────────────────┘


Step 2: Create ephemeral node (node that can be deleted if it goes down) to say "I'm alive!"
API Server sends: "Create /servers/api-1"

┌─────────────────────────┐
│  ZooKeeper Ensemble     │
│                         │
│  Data tree:             │
│  /servers/              │
│    └─ api-1 ← NEW!      │
│       (ephemeral)       │
└─────────────────────────┘


Step 3: If API server crashes...
API Server: ☠️ (crashed)

ZooKeeper notices: "Hey, that connection died!"

┌─────────────────────────┐
│  ZooKeeper Ensemble     │
│                         │
│  Data tree:             │
│  /servers/              │
│    └─ api-1 ← DELETED!  │
│       (auto-removed)    │
└─────────────────────────┘


Step 4: Other services watching see the change
Load Balancer: "Oh! api-1 is gone, stop sending traffic there"
```
Actually just with this example, zookeper ensemble is just telling us if server is up or down, which a typical LB can do. Then
whats the point of ZK? Its for [more complex scenarios where maybe services need to interact with themselves or need coordination](https://github.com/brian6484/CSKnowledge/blob/main/Misc/Zookeeper/Why%20ZK%20is%20needed%20over%20LB%20.md)

---

## The Client Library

**You never run ZooKeeper yourself** - you use a **client library** in your code.

### **Popular Client Libraries:**

| Language | Library |
|----------|---------|
| Python | `kazoo` |
| Java | Official ZooKeeper client |
| Go | `github.com/go-zookeeper/zk` |
| Node.js | `node-zookeeper-client` |

### **What the Client Does:**

```
Your Code          Client Library              ZooKeeper Ensemble
   │                      │                           │
   │─── zk.create() ────►│                           │
   │                      │──── TCP request ────────►│
   │                      │                           │ (servers vote)
   │                      │                           │ (leader writes)
   │                      │◄──── Response ───────────│
   │◄── success ─────────│                           │
   │                      │                           │
   │                      │──── heartbeat ──────────►│
   │                      │◄──── heartbeat ──────────│
   │                      │  (keeps session alive)   │
```

**The client handles:**
- Opening TCP connections
- Sending requests in ZooKeeper protocol
- Keeping connection alive (heartbeats)
- Reconnecting if a ZooKeeper server dies
- Automatically switching to another server in ensemble

---

## Real Example: Leader Election

Let's see both CLIENT and ENSEMBLE working together:

### **Setup:**
```
You have 3 API servers (clients)
They all connect to 1 ZooKeeper ensemble (3 servers)
Goal: Pick one API server as "leader"
```
[why do we need a leader in our clients?]

### **The Code Each API Server Runs:**

```python
from kazoo.client import KazooClient
import socket

# This is CLIENT code (runs on YOUR server)
zk = KazooClient(hosts='zk1:2181,zk2:2181,zk3:2181')  # Connect to ENSEMBLE
zk.start()

# Each API server tries to create the SAME path
my_name = socket.gethostname()  # "api-server-1"

try:
    # Try to create /leader node
    zk.create('/leader', my_name.encode(), ephemeral=True)
    print(f"🎉 I ({my_name}) am the LEADER!")
    
    # Do leader stuff...
    
except NodeExistsError:
    print(f"😢 Someone else is leader")
    
    # Watch the leader node
    @zk.DataWatch('/leader')
    def watch_leader(data, stat):
        if stat is None:  # Leader died!
            print("Leader crashed! I'll try to become leader!")
            try:
                zk.create('/leader', my_name.encode(), ephemeral=True)
                print("🎉 I'm the new leader!")
            except NodeExistsError:
                print("Someone else became leader first")
```

### **What Happens:**

```
Time 0: All servers start
┌─────────┐  ┌─────────┐  ┌─────────┐
│ API-1   │  │ API-2   │  │ API-3   │  ← CLIENTs
└────┬────┘  └────┬────┘  └────┬────┘
     │            │            │
     └────────────┼────────────┘
                  ▼
         ┌──────────────────┐
         │ ZK Ensemble      │  ← SERVERS
         │ (3 ZK nodes)     │
         └──────────────────┘


Time 1: They all try to create /leader
API-1: "Create /leader!" ─┐
API-2: "Create /leader!" ─┼─► ZK Ensemble decides
API-3: "Create /leader!" ─┘    (whoever asked first wins)

Result:
/leader = "api-1" ← Only ONE can exist!

API-1: ✅ "I'm leader!"
API-2: ❌ "Node exists, I'll wait"
API-3: ❌ "Node exists, I'll wait"


Time 2: API-1 crashes
API-1: ☠️

ZK Ensemble: "Session died, delete /leader (it was ephemeral!)"

API-2 and API-3 get notified (they were watching!)

API-2: "Let me try!" → Creates /leader first!
API-3: "Let me try!" → Too late, node exists

API-2: ✅ "I'm the new leader!"
```

---

## Visual Summary

```
┌──────────────────────────────────────────────────────────┐
│                    YOUR INFRASTRUCTURE                    │
├──────────────────────────────────────────────────────────┤
│                                                           │
│  CLIENT SIDE (Your Applications)                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │  API-1   │  │  API-2   │  │  API-3   │              │
│  │          │  │          │  │          │              │
│  │ ┌──────┐ │  │ ┌──────┐ │  │ ┌──────┐ │              │
│  │ │ZK Lib│ │  │ │ZK Lib│ │  │ │ZK Lib│ │ ← Client libs│
│  │ └──┬───┘ │  │ └──┬───┘ │  │ └──┬───┘ │              │
│  └────┼─────┘  └────┼─────┘  └────┼─────┘              │
│       │             │             │                      │
│       └─────────────┼─────────────┘                      │
│                     │ TCP                                │
│  ═══════════════════╪═════════════════════════════════  │
│                     │                                    │
│  SERVER SIDE (ZooKeeper Ensemble)                       │
│  ┌──────────────────┴──────────────────┐               │
│  │         ZooKeeper Ensemble           │               │
│  │  ┌────────┐ ┌────────┐ ┌────────┐   │               │
│  │  │  ZK-1  │ │  ZK-2  │ │  ZK-3  │   │               │
│  │  │(Leader)│ │ (Vote) │ │ (Vote) │   │               │
│  │  └────────┘ └────────┘ └────────┘   │               │
│  │       Separate machines/VMs          │               │
│  └──────────────────────────────────────┘               │
└──────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

| Term | Simple Meaning |
|------|----------------|
| **ZooKeeper Ensemble** | A cluster of 3-5 ZooKeeper server machines that work together |
| **ZooKeeper Client** | Your application code that connects to the ensemble |
| **Client Library** | Code you import (like `kazoo`) that handles talking to ZooKeeper |
| **Ephemeral Node** | Data that auto-deletes when your app disconnects |
| **Watch** | "Tell me when this data changes" |

### **The Relationship:**
- **Ensemble = The service** (runs on its own servers)
- **Client = Your app** (connects to the service)
- Like: Database = Server, Your App = Client

---

PI-1 crashes → Load Balancer doesn't know wait doesnt load balancer know which server is healthy or not? whats the point of zookeper then?
