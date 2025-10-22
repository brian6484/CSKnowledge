## Start
So this goes deeper to application-level.

## systemd service
### Check systemd service's log
Rmb journalctl is the systemd logging service. **ALL systemd services log to journal** and journalctl is the tool to read those queries.
```
systemd = Init system & service manager
   ↓
systemd-journald = Logging daemon (part of systemd)
   ↓
journalctl = Command to read systemd-journald logs
```

We can check the log via
```
sudo journalctl -u <systemd service name like nginx,gunicorn,etc> -n 100 | grep -i error

Oct 12 15:45:12 gunicorn[1547]: WARNING: Metrics endpoint generating 247k metrics
Oct 12 15:45:34 gunicorn[1547]: WARNING: Database query for user_activity_metrics took 2.3s
Oct 12 15:46:01 gunicorn[1547]: ERROR: Metrics generation timeout - too many database queries
Oct 12 15:46:23 gunicorn[1547]: WARNING: Metrics cardinality explosion detected
```

### check systemd app directory (where application files are like the folder containing ur code)
we can check via ps aux and seeing if config file path is visible. Else, we can do sudo systemctl status gunicorn to see the service file, which will tell us the [Working Directory]

```bash
# Method 1: Check service file
systemctl cat myapp

# Look for these lines:
# WorkingDirectory=/path/to/app
# ExecStart=/path/to/app/script.sh

# Method 2: Check running process
ps aux | grep myapp

# Method 3: If it's a Python app
sudo lsof -p $(pgrep -f myapp) | grep cwd
```

**Example service file:**
```ini
[Unit]
Description=My App

[Service]
Type=notify
User=www-data
WorkingDirectory=/opt/myapp        ← App directory
ExecStart=/opt/myapp/venv/bin/gunicorn app:app  ← Executable path
Restart=always

[Install]
WantedBy=multi-user.target
```

furthermore to find the config file in app directory, u should go to the app directory and find. Maybe the metrics app logic is weird and u wanna check 

## TL;DR:

- **`systemctl`** = Control services (start/stop/restart)
- **`journalctl`** = Read service logs
- **App directory** = Check `systemctl cat <service>` for `WorkingDirectory` or `ExecStart` paths

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

can check why via
```
cat /etc/postgresql/14/main/postgresql.conf | grep -i "checkpoint"
#checkpoint_timeout = 5min                  # range 30s to 1d
checkpoint_timeout = 30s
#checkpoint_completion_target = 0.9         # default
checkpoint_completion_target = 0.5
#max_wal_size = 1GB
max_wal_size = 256MB
#min_wal_size = 80MB
min_wal_size = 50MB
```

#### show active queries
stat activity shows whats happening inside the db
```
sudo -u postgres psql
SELECT pid, query, state FROM pg_stat_activity;
```

## check remote drives
wrong command is to use lsblk, which lists **block devices**(physical/virtual disks like HHD, SSD, USB drives). But network filesystems (nfs) arent block devices. 
```
mount | grep <name of remote drive file>

## or
df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           1.6G  2.1M  1.6G   1% /run
/dev/nvme0n1p2  234G   89G  133G  41% /
tmpfs           7.8G  145M  7.6G   2% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
/dev/nvme0n1p1  511M  6.1M  505M   2% /boot/efi
tmpfs           1.6G  120K  1.6G   1% /run/user/1000
```
