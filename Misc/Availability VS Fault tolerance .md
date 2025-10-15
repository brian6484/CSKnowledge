## Diff
Avaialability is like out of 365 days, how much is it operational and not down? (System is operational and responsive to requests). From user perspective, "Can I use the service right now?"

FT is when the system is down, is system able to operate despite component failures?

```
Fault Tolerance → enables → High Availability
    (means)                      (outcome)
```

**Example:**
- **Fault Tolerance**: "We have 3 replicas of each index shard. If 1 fails, the other 2 serve requests"
- **Availability**: "Search remains available 99.9% of the time because of this redundancy"

---

## In Your Non-Functional Requirements

Combine them
```
3. Availability & Fault Tolerance:
   - 99.9% uptime
   - No single point of failure
   - Graceful degradation on partial failures
```
```
