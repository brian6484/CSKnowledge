## Common directories
/var/log: stores both system and application logs. It stores *variable data*, files that might change. But nowadyas on modern linux, we use systemd journal. The Journal's data is typically stored in a binary format under /var/log/journal so cant just cat. We need to use journalctl commands. 

/etc: stores system and application **config files**.

/var/lib: applications store their variable data like db files, indexes, configs, logs. It stores the application state basically

## File
### Viewing directory and FILE PERMISSION
ls -la and u can add -h to show the memory too. But this command is useful not only to see what files there are in directory but if u use
it on a specific file, u can view file permission
```
ls -la
```

## Log
### Finding Postgres Log
It could be in /var/log or in postgres data directory or in the systemd journal for Postgres.

Going to $\text{systemd}$ Journal: When Postgres runs as a service managed by $\text{systemd}$ (the common init system on modern Linux distributions), $\text{systemd}$ captures the process's $\text{stdout}$ and $\text{stderr}$ and forwards them into its central logging system, the Journal.

For systemd journal, -u = unit, speicifying we only want postgres 
```
sudo journalctl -u postgresql.service

# if that doesnt work, use this and it will give the exact unit name to use in the above command 
sudo systemctl list-units | grep postgres
```

We can also use postgres itself
```
sudo -u postgres psql -c "SHOW data_directory;"
       data_directory
------------------------
 /var/lib/postgresql/14/main

# u can cd there and find log file or
sudo -u postgres psql -c "SHOW log_directory;"
log_directory
---------------
 pg_log
(1 row)
```
so it show the log file is at /var/lib/postgresql/14/main/pg_log. 

## Config
### Postgres config
-name "*.conf* finds files matching the pattern.

2>/dev/null redirects error messages (file descriptor 2 =stderr) to /dev/null (throw them away). 
```
find /etc/postgresql -name "*.conf" 2>/dev/null
/etc/postgresql/14/main/postgresql.conf
/etc/postgresql/14/main/pg_hba.conf
/etc/postgresql/14/main/pg_ident.conf
```

### nginx config
**You run:**
```bash
sudo tail -100 /var/log/nginx/access.log | grep 10.0.5.142
```

**Result:**
```
10.0.5.142 - - [12/Oct/2025:15:47:23 +0000] "GET /metrics HTTP/1.1" 200 45231 "-" "Prometheus/2.45.0"
10.0.5.142 - - [12/Oct/2025:15:47:23 +0000] "GET /metrics HTTP/1.1" 200 45234 "-" "Prometheus/2.45.0"
10.0.5.142 - - [12/Oct/2025:15:47:23 +0000] "GET /metrics HTTP/1.1" 200 45229 "-" "Prometheus/2.45.0"
10.0.5.142 - - [12/Oct/2025:15:47:23 +0000] "GET /metrics HTTP/1.1" 200 45236 "-" "Prometheus/2.45.0"
10.0.5.142 - - [12/Oct/2025:15:47:23 +0000] "GET /metrics HTTP/1.1" 200 45228 "-" "Prometheus/2.45.0"
10.0.5.142 - - [12/Oct/2025:15:47:24 +0000] "GET /metrics HTTP/1.1" 200 45232 "-" "Prometheus/2.45.0"
10.0.5.142 - - [12/Oct/2025:15:47:24 +0000] "GET /metrics HTTP/1.1" 200 45235 "-" "Prometheus/2.45.0"
... (93 more identical lines)
```

😱 **ALL hitting `/metrics` endpoint!** Every single request at almost the same timestamp (15:47:23-24).

**Let's see how fast these are coming:**
the grep is setting the time itnerval to the second 
```bash
sudo tail -1000 /var/log/nginx/access.log | grep 10.0.5.142 | grep "15:47:2[0-9]" | wc -l
```

**Result:**
```
847
```

**847 requests in one second!** That's insane for a metrics endpoint.

**Let's check how long the /metrics endpoint takes to respond:**
```bash
curl -w "\nTime: %{time_total}s\n" http://localhost/metrics -o /dev/null -s
```

**Result:**
```
Time: 3.452s
```

