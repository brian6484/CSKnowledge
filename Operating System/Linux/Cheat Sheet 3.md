## Start
So this goes deeper to application-level.

## I/O
### Postgres DB
#### checkpoint
```
**iotop (top 5 processes):**
Total DISK READ:        12.34 M/s | Total DISK WRITE:      128.56 M/s
  PID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN     IO>    COMMAND
18492  be/4  postgres    0.00 B/s  124.34 M/s  0.00 %  45.23 % postgres: checkpoint
22341  be/4  root        0.00 B/s    2.12 M/s  0.00 %   8.34 % java -jar app.jar
31245  be/4  syslog      1.23 M/s    0.34 M/s  0.00 %   5.67 % rsyslog
```

this checkpoint in postgres is when **db writes all dirty pages to disk** to ensure durability. It can be heavy i/o if thers lots of data
to flush.

#### show active queries
stat activity shows whats happening inside the db
```
sudo -u postgres psql
SELECT pid, query, state FROM pg_stat_activity;
```


