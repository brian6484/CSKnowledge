## wats index?
index = a fast lookup data structure like book's index

### primary index
```
CREATE TABLE game_history (
    game_id UUID PRIMARY KEY  -- This creates an index automatically!
);

```


### Secondary Index vs Composite Index
diff is composite index is a subset of secondary index. Secondary index can have single column index AND composite (multi column) indexes.

| | Secondary Index | Composite Index |
|---|-----------------|-----------------|
| **Definition** | Any index that's NOT the primary key | Index on **multiple columns** together |
| **Can be...** | Single column OR multiple columns | Always multiple columns |

---

### Examples:

```sql
CREATE TABLE game_history (
    game_id UUID PRIMARY KEY,           -- Primary key (not secondary)
    player1_id UUID,
    player2_id UUID,
    played_at TIMESTAMP
);

-- Secondary index (single column):
CREATE INDEX idx_player1 ON game_history(player1_id);
                                        └─ 1 column

-- Secondary index (composite - multiple columns):
CREATE INDEX idx_player1_time ON game_history(player1_id, played_at);
                                              └───────┬────────┘
                                              2 columns together
```

---

### Venn Diagram:

```
┌─────────────────────────────────────┐
│     All Indexes                     │
│                                     │
│  ┌──────────────────────────────┐  │
│  │ Secondary Indexes            │  │
│  │ (non-primary key)            │  │
│  │                              │  │
│  │  ┌────────────────────────┐  │  │
│  │  │ Composite Indexes      │  │  │
│  │  │ (multiple columns)     │  │  │
│  │  └────────────────────────┘  │  │
│  │                              │  │
│  │  Single-column indexes       │  │
│  │  (e.g., idx_player1)         │  │
│  └──────────────────────────────┘  │
│                                     │
│  Primary Key Index                  │
└─────────────────────────────────────┘
```

---

### Classification:

```sql
-- Primary key index:
game_id PRIMARY KEY                    ← Primary, Single-column

-- Secondary, single-column:
CREATE INDEX idx_player1 ON game_history(player1_id);
                                       ← Secondary, Single-column

-- Secondary, composite:
CREATE INDEX idx_player1_time ON game_history(player1_id, played_at);
                                              ← Secondary, Composite
```

---

### Key Takeaway:

**"Secondary"** = relationship to primary key (it's NOT the PK)  
**"Composite"** = number of columns (2 or more)

**A composite index IS a secondary index** (if it's not the PK)  
**But a secondary index might NOT be composite** (could be single-column)

---

**TL;DR:**
- **Secondary** = not primary key
- **Composite** = multiple columns
- They overlap but aren't the same!

Clear? 🎯
