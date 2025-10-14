## Memory bus
Its a collection of electrical pahtways that conenct the CPU and RAM, enabling data transfer between processor and system memory.

### Components of memory bus
1) Address bus - carries memory address from CPU to RAM unidirectionally, width determines max addressable memory
2) Data bus - carries data from CPU to RAM bidirectionally, width affects performance
3) Control bus - carries control signals (read/write commands, timing signals), coordinates when and how data transfer occurs

### Flow
When the CPU needs data:

1) CPU puts the memory address on the address bus
2) CPU sends a "read" signal on the control bus
3) RAM locates the data at that address
4) RAM puts the data on the data bus
5) CPU reads the data from the data bus

For writes, the process is similar but data flows from CPU to RAM.

### Reading from RAM (Most Common Operation)

```
Step 1: CPU → Address Bus → RAM
        "I want data from address 0x1000"
        
Step 2: CPU ← Data Bus ← RAM
        "Here's the data: 0x42"
```

**Address bus carries:** The **request** (which address)  
**Direction:** CPU → RAM (unidirectional)

---

## Why It Seems Backwards

You might be thinking:

> "If I'm **reading FROM RAM**, shouldn't the address come FROM RAM?"

**No!** Think of it like asking a question:

- **You ask:** "What's in locker #42?" (CPU → Address Bus → RAM)
- **RAM answers:** "Here's what's inside" (RAM → Data Bus → CPU)

The **address is the question**, not the answer!

---

## Both Operations

### READ Operation
```
CPU: "Give me data from address 0x1000"
     ──address 0x1000──→ [RAM]
     
     ←──data: 0x42───── [RAM]
```

### WRITE Operation
```
CPU: "Store this at address 0x1000"
     ──address 0x1000──→ [RAM]
     ──data: 0x99──────→ [RAM]
```

**In both cases:**
- Address bus: CPU → RAM (unidirectional)
- Data bus: CPU ↔ RAM (bidirectional)
- Control bus: Signals read/write

---

## Why Address Bus is Unidirectional

**RAM never tells the CPU what address to access!**

- CPU is always the "master" - it decides what to read/write
- RAM is always the "slave" - it responds to requests
- RAM never initiates: "Hey CPU, read from address 0x5000!"

---

## Summary

**You asked:** "Isn't it the opposite direction?"

**Answer:** No - the address bus carries the **location being requested**, not the data itself. The CPU always decides which address to access, so:

- **Address Bus:** CPU → RAM (unidirectional) ✓
- **Data Bus:** CPU ↔ RAM (bidirectional)

The **data** comes back from RAM (on the data bus), but the **address request** always goes from CPU to RAM (on the address bus).

### Metrics
Bandwidth: How much data can transfer per second (GB/s)
Latency: Time delay between request and response
Clock Speed: How fast the bus operates (MHz/GHz)
