## Strat
So theres a few ways

1) Rolling restart
First deploy the new model/server patch to S3 shared storage. Restart workers 1 by 1 or in batches and while updating, LB redirects traffic to the healthy servers not the servers that are
updating

```
1. Upload model_v1.1.h5 to S3
2. Update config: CURRENT_MODEL = "model_v1.1.h5"
3. Restart worker-1 → downloads v1.1, loads into memory
4. Wait for worker-1 healthy → Restart worker-2
5. Repeat until all workers updated
```
Simple and no downtime but there might be inconsistency with the models (models running simultaneously) and takes a lot of time 

2) Blue green deployment
We run 2 complete sets of workers where blue = old, green = new. And we switch traffic at once
```
Blue (v1.0) - 100 workers running
    ↓
Spin up Green (v1.1) - 100 NEW workers with new model
    ↓
Test Green workers
    ↓
Switch Kafka consumer group to Green
    ↓
Shut down Blue after drain period
```

Adv is we can switch back to old blue deployment if anything goes wrong (easy rollback) and can test new model with prod traffic before making swtich. But we need 2x resources.

3) Canary Deployment (best for prod)
Roll out to small percentage first, monitor, then full rollout

```
1. Deploy v1.1 to 5% of workers (5 out of 100)
2. Monitor for 1 hour:
   - Error rates
   - False positive/negative rates
   - Latency
3. If good → Deploy to 25%, then 50%, then 100%
4. If bad → Rollback just the 5%
```

## So which to use?
Blue-Green for major updates and Rolling Restart for minor updates

Why BG for major update? It has high risk where we need be able to roll back the changes and can do full testing with prod traffic first.

Rolling restart is cost efficient as it doesnt need 2x resources like BG and there is acceptable inconsistency as slight diff in consistency might not affect users that badly
