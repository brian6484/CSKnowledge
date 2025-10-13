## Embed
For lightweight models (text,image processor) that dont take up too much memory like up to a few Gb, we can directly embed these models into the servers. Embed means loading the models directly into the process's
memory upon startup so that we dont have to make extra service and make extra externall call. This helps minimise latency and simplify architecture.

But if its huge, like video processing ML model that is liek 10 Gb, then prob we need a dedicated inference service that workers call via gRPC instead of HTTP for better performance.

## Then how do we update these embedded models
More about it [here]()

