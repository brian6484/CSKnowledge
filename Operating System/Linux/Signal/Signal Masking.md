## Masking
It temporarily blocks certain signals from being delivered.
```
Signal arrives
    ↓
Check signal mask (blocked list)
    ↓
If blocked: Signal queued, not delivered yet
If not blocked: Delivered to handler
```

Its normally used to protect critical code sections where signals would cause probs. For example, might need to block sigterm right before a piece
of critical code and then unblock SIGTERM. 
