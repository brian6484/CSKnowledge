Read until the end to improve fault tolerance (physical nodes, not vnodes, can carry replicas of other physical nodes for FA)

## Server scalability
For horizontal scaling to **increase scalability**, virtual nodes (vnodes) are essential for better load distribution

## The Problem Without Virtual Nodes

### Scenario: Physical Nodes Only

```
Hash Ring with 4 Physical Nodes:

        Node A (0°)
            ↓
    ←───────●───────→
   ↗                 ↘
Node D              Node B
(270°)              (90°)
   ↖                 ↙
    ←───────●───────→
            ↑
        Node C (180°)
```

**Data Distribution:**
- Node A: handles 0° to 90° = 25% of data
- Node B: handles 90° to 180° = 25% of data
- Node C: handles 180° to 270° = 25% of data
- Node D: handles 270° to 360° = 25% of data

**Looks perfect, right? WRONG!**

### Problems with Physical Nodes Only:

#### **Problem 1: Uneven Hash Distribution**
```
In reality, user IDs don't hash evenly:

Node A: 15% of data  ← Underutilized
Node B: 35% of data  ← Overloaded (Hot spot!)
Node C: 28% of data  
Node D: 22% of data
```

**Why?** Hash functions distribute data randomly, not perfectly evenly.

#### **Problem 2: Adding/Removing Nodes**
that results in a huge load redistribution

**Adding Node E:**
```
Before (4 nodes):
Node A → Node B → Node C → Node D
 25%      25%      25%      25%

After adding Node E (5 nodes):
Node A → Node E → Node B → Node C → Node D
 20%      20%      20%      20%      20%

Problem: ALL nodes need rebalancing!
- Node B loses 5% to Node E
- Node C loses 5% to new distribution
- Node D loses 5% 
- Node A loses 5%

Result: Massive data migration across ALL nodes!
```

#### **Problem 3: Heterogeneous Hardware**
if certain node/server has a higher CPU/memory capabaility, just using physical nodes doesnt fully utilise this adv cuz its just 1 node (whereas if u use vnodes u can assign more virtual
nodes to this highly-performing server)

```
Node A: 64GB RAM, 16 cores  ← Can handle more load
Node B: 32GB RAM, 8 cores   ← Should handle less
Node C: 64GB RAM, 16 cores
Node D: 32GB RAM, 8 cores

But physical nodes = equal distribution = underutilized powerful machines!
```

---

## Solution: Virtual Nodes (VNodes)

### What Are Virtual Nodes?

**Each physical node has MANY virtual nodes (typically 128-256 vnodes per physical node)**
The higher the vnodes, the less the standard deviation, so the less the load distribution is required when removing/adding nodes.

```
Physical Node A → [vnode-A1, vnode-A2, vnode-A3, ..., vnode-A128]
Physical Node B → [vnode-B1, vnode-B2, vnode-B3, ..., vnode-B128]
Physical Node C → [vnode-C1, vnode-C2, vnode-C3, ..., vnode-C128]
Physical Node D → [vnode-D1, vnode-D2, vnode-D3, ..., vnode-D128]

Total: 512 virtual nodes distributed around the hash ring
```

### Hash Ring with Virtual Nodes

```
            vnode-A15
                ●
    vnode-D8 ●     ● vnode-B22
          ●           ●
    vnode-C3 ●     ● vnode-A87
              ●   ●
        vnode-B5   vnode-D91
                ●
            vnode-C44
            
(512 vnodes scattered around the ring)
```

---

## Benefits of Virtual Nodes

### **Benefit 1: Much Better Load Distribution**

**Without VNodes (4 physical nodes):**
```
Node A: 15% ← Bad
Node B: 35% ← Bad (overloaded)
Node C: 28%
Node D: 22%

Standard Deviation: High
```

**With VNodes (4 physical nodes × 128 vnodes = 512 vnodes):**
```
Node A: 24.8% ← Good!
Node B: 25.3% ← Good!
Node C: 24.9% ← Good!
Node D: 25.0% ← Good!

Standard Deviation: Very low
```

**Why?** Law of large numbers - more vnodes = more chances for even distribution.

---

### **Benefit 2: Minimal Data Movement When Scaling**

**Adding Node E with VNodes:**

**Before:**
```
512 vnodes distributed across Nodes A, B, C, D
Each node: 128 vnodes
```

**After adding Node E:**
```
640 vnodes total (128 per physical node × 5 nodes)

Node E gets 128 new vnodes scattered around ring
These vnodes steal data from nearby vnodes:
- ~26 vnodes from Node A
- ~26 vnodes from Node B  
- ~26 vnodes from Node C
- ~26 vnodes from Node D

Result: Only 20% of data moves (instead of 80%!)
AND it moves from ALL nodes evenly (no single node overloaded during rebalancing)
```

**Visual Example:**
```
Removing vnode-A15 from Node A → moved to new vnode-E3 on Node E
Removing vnode-B22 from Node B → moved to new vnode-E7 on Node E
Removing vnode-C44 from Node C → moved to new vnode-E11 on Node E
...

Data movement is distributed across ALL nodes
No single node becomes a bottleneck during migration
```

---

### **Benefit 3: Heterogeneous Hardware Support**

**Scenario:** Different server capacities

```
Node A: 64GB RAM, 16 cores  ← Powerful
Node B: 32GB RAM, 8 cores   ← Standard
Node C: 64GB RAM, 16 cores  ← Powerful
Node D: 32GB RAM, 8 cores   ← Standard
```

**Solution: Assign more vnodes to powerful servers**

```
Node A: 256 vnodes (double)  ← Handles 2x load
Node B: 128 vnodes (standard)
Node C: 256 vnodes (double)  ← Handles 2x load
Node D: 128 vnodes (standard)

Total: 768 vnodes

Distribution:
Node A: 33.3% of data ← Uses full capacity
Node B: 16.7% of data ← Appropriate for hardware
Node C: 33.3% of data ← Uses full capacity
Node D: 16.7% of data ← Appropriate for hardware
```

**Result:** Efficient hardware utilization!

---

### **Benefit 4: Faster Recovery from Failures**

**Without VNodes (Physical Node B fails):**
```
Node A: Takes all of Node B's traffic (25% → 50%)  ← Overloaded!
Node C: No change (25%)
Node D: No change (25%)

Problem: Node A becomes bottleneck, might crash too (cascading failure)
```

**With VNodes (Physical Node B with 128 vnodes fails):**
```
Node B's 128 vnodes distributed to replicas:
- 43 vnodes → Node A (19% → 27%)  ← Manageable
- 42 vnodes → Node C (19% → 27%)  ← Manageable
- 43 vnodes → Node D (19% → 27%)  ← Manageable

Load increase per node: Only ~8%
All nodes can handle it easily
```

**Recovery is also faster:**
- Move 128 small vnode ranges (parallel)
- vs. Move 1 huge data range (sequential)

### benefit 5 - decrease the prob of hotspot 
cuz const hashing just uses a contiguous large range per node

---

## Implementation Details

### Configuration Example (Cassandra-style)

```yaml
cluster_config:
  physical_nodes: 4
  vnodes_per_node: 256
  replication_factor: 3
  
nodes:
  node_a:
    capacity_weight: 2.0    # 2x vnodes (powerful server)
    vnodes: 512
    
  node_b:
    capacity_weight: 1.0    # Standard vnodes
    vnodes: 256
    
  node_c:
    capacity_weight: 2.0    # 2x vnodes (powerful server)
    vnodes: 512
    
  node_d:
    capacity_weight: 1.0    # Standard vnodes
    vnodes: 256
```

### Token Assignment Algorithm

```python
def assign_vnodes(node_id, num_vnodes, total_nodes):
    """
    Assign vnodes to a physical node
    """
    vnodes = []
    ring_size = 2**64  # Hash ring size
    
    for i in range(num_vnodes):
        # Generate token for each vnode
        # Use node_id and vnode_index for uniqueness
        token = hash(f"{node_id}:{i}") % ring_size
        vnodes.append({
            'vnode_id': f"{node_id}-vnode-{i}",
            'token': token,
            'physical_node': node_id
        })
    
    return sorted(vnodes, key=lambda x: x['token'])


def route_request(key, vnode_ring):
    """
    Route a request to the correct vnode/physical node
    """
    key_hash = hash(key) % (2**64)
    
    # Binary search to find next vnode clockwise
    for vnode in vnode_ring:
        if vnode['token'] >= key_hash:
            return vnode['physical_node']
    
    # Wrap around to first vnode
    return vnode_ring[0]['physical_node']
```

### Data Structure

```python
# Global hash ring with all vnodes
hash_ring = [
    {'vnode_id': 'A-vnode-0', 'token': 123456, 'physical_node': 'node_a'},
    {'vnode_id': 'B-vnode-0', 'token': 234567, 'physical_node': 'node_b'},
    {'vnode_id': 'C-vnode-0', 'token': 345678, 'physical_node': 'node_c'},
    {'vnode_id': 'A-vnode-1', 'token': 456789, 'physical_node': 'node_a'},
    {'vnode_id': 'D-vnode-0', 'token': 567890, 'physical_node': 'node_d'},
    # ... 512 vnodes total
]

# Lookup example
user_id = "user_12345"
user_hash = hash(user_id)  # e.g., 400000
# Binary search finds vnode at token 456789
# Routes to physical node_a
```

---

## Real-World Usage

### Amazon DynamoDB
- Uses virtual nodes internally
- Automatically rebalances when adding capacity
- Users don't see vnodes, but benefit from even distribution

### Apache Cassandra
- Default: 256 vnodes per node
- Configurable via `num_tokens` parameter
- Community found 256 is sweet spot for most workloads

### Redis Cluster
- Uses 16,384 hash slots (essentially vnodes)
- Each physical node owns multiple slots
- Can move individual slots for fine-grained rebalancing

### Riak
- Default: 64 vnodes per node
- Lower number than Cassandra due to different architecture
- Still provides good distribution

---

## Choosing Number of VNodes

### Guidelines:

**Too Few VNodes (e.g., 8 per node):**
- ❌ Uneven distribution
- ❌ Large data movement when scaling
- ❌ Slow rebalancing

**Too Many VNodes (e.g., 1024 per node):**
- ❌ High metadata overhead
- ❌ Complex coordination
- ❌ Slower routing lookups

**Sweet Spot (128-256 vnodes per node):**
- ✅ Good distribution (< 5% variance)
- ✅ Manageable metadata
- ✅ Fast rebalancing
- ✅ Flexible for heterogeneous hardware

## increase FA

This means: **Each physical node stores copies (replicas) of data that "belongs" to other nodes.**

**How it works:**

1. **Primary storage**: Node A is responsible for keys that hash to range 100-200
2. **Replication**: Nodes B and C also store copies of that same data (keys 100-200)
3. So the data with hash 150 exists on nodes A, B, and C simultaneously

**Why?**
- **Fault tolerance**: If node A crashes, nodes B and C still have the data
- Typically Dynamo uses **replication factor N=3** (3 copies of everything)

**Combined with Vnodes:**

Since Vnodes are scattered and non-contiguous:
- Physical Node 1 might have Vnodes responsible for ranges: 10-20, 150-160, 500-510
- Physical Node 2 stores **replicas** of some of Node 1's ranges (for backup)
- But Node 2 also has its own primary Vnodes with different ranges
- And Node 1 stores replicas of some of Node 2's data

**Result**: Every node is both:
- **Primary** for some data (its own Vnodes)
- **Replica** for other nodes' data (backup copies)

This creates a mesh of redundancy across the cluster.
