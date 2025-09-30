A ZSET is fundamentally a two-column structure for its data entries: Member and Score. The third term, the Key, is just the identifier for the whole set itself.

Yes, in the original example provided by Server C, there are **two** distinct Sorted Sets (ZSETs) being used as secondary indexes:

1.  **`posts:by_reactions`**: A Sorted Set where the **score** is the reaction count (e.g., $8$ likes). This set is used to get the posts ranked by **popularity**.
2.  **`posts:by_time`**: A Sorted Set where the **score** is the Unix timestamp (e.g., $1706178600$). This set is used to get the posts ranked by **recency**.

***

### Summary of Server C

Server C is essentially running a small indexing service using these two data structures to answer two common questions efficiently:

| Index Key | Redis Type | Score Represents | Query Purpose |
| :--- | :--- | :--- | :--- |
| **`posts:by_reactions`** | **ZSET** | Total Likes/Reactions | "What are the **most popular** posts?" |
| **`posts:by_time`** | **ZSET** | Unix Timestamp | "What are the **most recent** posts?" |

Using two separate ZSETs is the standard way to support multiple sorting criteria for the same underlying data (the posts).
