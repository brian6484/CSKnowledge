# Easy
## 1
1. You can use ps to list all processes and see if you see something related, for example with: ps auxf.
Diff between ps aux and auxf is that ps auxf shows in tree format.

2. but instead of ps auxf there is this `fuser` command to quickly find the offending process:
fuser /var/log/bad.log. (lsof is another option but very slow cuz it lists **ALL OPEN FILES IN THE SYSTEM**

4. Solution: Using the PID found, terminate (kill) the process with kill -9 PID, for example: kill -9 7.

```
## check log is being filled up with 1s each
tail -f /var/log/bad.log

## get the exact pid that is writing to this log
fuser /var/log/bad.log

## kill that process
kill -9 <pid>
```

## 2
so this we need to process the log file to get the first column (ip adress). We can use this `awk` tool. awk splits each line into columns
via **whitespace**.

awk '{print $1}' - Extract first field (IP address) from each line
sort - Sort IPs alphabetically
uniq -c - Count consecutive duplicate lines
sort -nr - Sort by count/freq in descending order (numeric, reverse)
head -1 - Get the top result
awk '{print $2}' - Extract just the IP (second field after count)

awk '{print $1}' produces: 192.168.1.1, 10.0.0.5, 192.168.1.1, 10.0.0.5, 192.168.1.1
sort produces: 10.0.0.5, 10.0.0.5, 192.168.1.1, 192.168.1.1, 192.168.1.1
uniq -c produces: 2 10.0.0.5, 3 192.168.1.1
(notice uniq -c puts the freq in the first column)
sort -nr produces: 3 192.168.1.1, 2 10.0.0.5
head -1 produces: 3 192.168.1.1
awk '{print $2}' produces: 192.168.1.1

```
awk '{print $1}' /home/admin/access.log | sort | uniq -c | sort -nr | head -1 | awk '{print $2}' > /home/admin/highestip.txt

## or
awk '{print $1}' /home/admin/access.log | sort | uniq -c | sort -nr | head -1 | awk '{print $2}'
## this should give us the ip adress, then we write to the log file via **echo**

echo "192.168.1.1" > /home/admin/highestip.txt
```

## 3

## 4 "Oaxaca": Close an Open File

So we need to close an open file **without killing the process**. Lets first get the PID and the file decriptor number.
```
# Find which process has the file open and get the file descriptor number
lsof /home/admin/somefile

COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
vim     1234 admin   3w   REG    8,1      123  456 /home/admin/somefile
```

### Whats file descriptor?
If u look at FD column thats file descriptor. The number 3 in this case is the FD number, which is kinda like a hotel room key.
Think of it like hotel room keys:

Hotel A gives you "Room 3" key → opens Room 3 in Hotel A
Hotel B gives you "Room 3" key → opens Room 3 in Hotel B
Same number (3), but different rooms in different hotels

File descriptors work the same way:

**Process A's "FD 3" is completely separate from Process B's "FD 3"**
Each process has its own private "hotel" of file descriptors

w=write,r=read,u=both.

So anyway it means process 1234's file descriptor #3 points to that /home/admin/somefile file. 

if that file is opened by vim like that sample output, its harder cuz its not bash but an external process like vim that is opening
this file. If such we need to use gdb
```
# Attach GDB to the process (replace 1234 with actual PID)
gdb -p 1234

# In GDB, call the close() system call on the file descriptor
(gdb) call close(3)

# Detach from the process
(gdb) detach

# Exit GDB
(gdb) quit
```

but in this case we see that
```
admin@ip-10-1-12-156:/$ lsof /home/admin/somefile
COMMAND PID  USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
bash    802 admin   77w   REG  259,1        0 272875 /home/admin/somefile
```
its the bash that has FD 77 open for writing. We can close it directly with
```
exec 77>&-
```

this command:
exec = bash command that changes FD for current shell
77= FD number
>& = redirect FD
and the dash(-) means close it.

other examples are
```
exec 77> /tmp/test    # Connect FD 77 to /tmp/test for writing
exec 77< /tmp/test    # Connect FD 77 to /tmp/test for reading  
exec 77>&-            # Close/disconnect FD 77 completely
```

another way to see that bash's FD is thru
```
admin@ip-10-1-12-156:/$ ls -la /proc/802/fd/
total 0
dr-x------ 2 admin admin  0 Sep 16 05:27 .
dr-xr-xr-x 9 admin admin  0 Sep 16 05:27 ..
lrwx------ 1 admin admin 64 Sep 16 05:27 0 -> /dev/pts/0
lrwx------ 1 admin admin 64 Sep 16 05:27 1 -> /dev/pts/0
lrwx------ 1 admin admin 64 Sep 16 05:27 2 -> /dev/pts/0
lrwx------ 1 admin admin 64 Sep 16 05:27 255 -> /dev/pts/0
l-wx------ 1 admin admin 64 Sep 16 05:27 77 -> /home/admin/somefile
```


# Medium
## 1
Situation: postgre isnt writing to disk

Lets first see if postgre process is running properly
```
ps aux | grep postgre

root       887  0.0  0.1   4832   876 pts/0    S+   01:46   0:00 grep postgre
```

i first thought its running properly but actually its not. grep is also an active process so it displays its own info.

if postgre was running properly
```
postgres  1234  0.0  0.1  25678  1234  ?      S    Jan20   0:00 /usr/lib/postgresql/16/bin/postgres -D /var/lib/postgresql/16/main
postgres  1235  0.0  0.0  25678  1234  ?      S    Jan20   0:00 postgres: 16/main: checkpointer
postgres  1236  0.0  0.0  25678  1234  ?      S    Jan20   0:00 postgres: 16/main: autovacuum launcher
```

so since its not running we should check the status via systemctl status
```
systemctl status postgresql

● postgresql.service - PostgreSQL RDBMS

   Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: ena

   Active: active (exited) since Mon 2025-09-15 01:40:40 UTC; 7min ago

  Process: 671 ExecStart=/bin/true (code=exited, status=0/SUCCESS)

 Main PID: 671 (code=exited, status=0/SUCCESS)


Sep 15 01:40:40 ip-10-1-12-192 systemd[1]: Starting PostgreSQL RDBMS...

Sep 15 01:40:40 ip-10-1-12-192 systemd[1]: Started PostgreSQL RDBMS.
```

so Active: active (exited) means service unit was successfully activated. But exited means its no longer running.
/bin/true line: so systemd service file for postgres is running this /bin/true which is a program that **simply exits with status
code 0**.

even when i tried starting via systemctl start postgresql, its still same. So we should check for error log via journalctl.

### systemd and journalctl
systemd - master manager for ur computer and is **first process to run when pc starts**. It starts and manages services, handles logs and manages system state

journalctl - command to query and display logs from **systemd journal**.

so we can check for logs via 2 commands
```
journalctl -u postgresql

-- Logs begin at Mon 2025-09-15 01:39:44 UTC, end at Mon 2025-09-15 01:53:10 UTC. --

Sep 15 01:40:40 ip-10-1-12-192 systemd[1]: Starting PostgreSQL RDBMS...

Sep 15 01:40:40 ip-10-1-12-192 systemd[1]: Started PostgreSQL RDBMS.
```
its not that useful but 

journalctl -p err this asks for all log entries with priority level of **error** from entire system
```
root@ip-10-1-12-192:/etc/postgresql/14/main# journalctl -p err

-- Logs begin at Mon 2025-09-15 01:39:44 UTC, end at Mon 2025-09-15 01:53:10 UTC. --

Sep 15 01:39:44 ip-10-0-0-22 kernel: ena 0000:00:05.0: LLQ is not supported Fallback t

Sep 15 01:39:44 ip-10-0-0-22 systemd-fstab-generator[210]: Failed to create unit file 

Sep 15 01:39:44 ip-10-0-0-22 systemd[204]: /usr/lib/systemd/system-generators/systemd-

Sep 15 01:40:40 ip-10-1-12-192 systemd-fstab-generator[646]: Failed to create unit fil

Sep 15 01:40:40 ip-10-1-12-192 systemd-fstab-generator[646]: Failed to create unit fil

Sep 15 01:40:40 ip-10-1-12-192 systemd[640]: /usr/lib/systemd/system-generators/system

Sep 15 01:40:40 ip-10-1-12-192 systemd[1]: Failed to start PostgreSQL Cluster 14-main.

Sep 15 01:51:12 ip-10-1-12-192 systemd[1]: Failed to start PostgreSQL Cluster 14-main.

lines 1-9/9 (END)...skipping...

-- Logs begin at Mon 2025-09-15 01:39:44 UTC, end at Mon 2025-09-15 01:53:10 UTC. --

Sep 15 01:39:44 ip-10-0-0-22 kernel: ena 0000:00:05.0: LLQ is not supported Fallback to host mode policy.

Sep 15 01:39:44 ip-10-0-0-22 systemd-fstab-generator[210]: Failed to create unit file /run/systemd/generator/opt-pgdata.mount, as it al

Sep 15 01:39:44 ip-10-0-0-22 systemd[204]: /usr/lib/systemd/system-generators/systemd-fstab-generator failed with exit status 1.

Sep 15 01:40:40 ip-10-1-12-192 systemd-fstab-generator[646]: Failed to create unit file /run/systemd/generator/opt-pgdata.mount, as it 

Sep 15 01:40:40 ip-10-1-12-192 systemd-fstab-generator[646]: Failed to create unit file /run/systemd/generator/opt-pgdata.mount, as it 

Sep 15 01:40:40 ip-10-1-12-192 systemd[640]: /usr/lib/systemd/system-generators/systemd-fstab-generator failed with exit status 1.

Sep 15 01:40:40 ip-10-1-12-192 systemd[1]: Failed to start PostgreSQL Cluster 14-main.

Sep 15 01:51:12 ip-10-1-12-192 systemd[1]: Failed to start PostgreSQL Cluster 14-main.
```
so its saying systemd service for the PostgreSQL database (version 14, in a configuration named main) tried to start but failed. We should thus see why its failing. We can check the system log and grep to only see postgre error. 

BUT WHY? Why isnt journalctl -p err not showing the root cause but /var/log/syslog is showing the root cause? 

This is an excellent question that gets to the heart of how logging works in modern Linux systems. The short answer is that systemd and syslog record different kinds of information.

systemd Journal (journalctl)
The systemd journal is a log of events managed by the systemd service manager. It primarily records information about the status of services themselves. It's concerned with whether a service started, stopped, or failed.

In your case, systemd tried to start the PostgreSQL service. When the PostgreSQL process immediately failed and exited, systemd noted this failure and logged it as:

Failed to start PostgreSQL Cluster 14-main.

It logged a general failure message, but it didn't know the specific, internal reason for that failure. It just knew that its command to start the service didn't succeed.

Syslog (/var/log/syslog)
The syslog file, on the other hand, is a traditional log file that collects messages from the kernel and various running applications. Many programs, including PostgreSQL, are configured to send their detailed, internal log messages to syslog.

When the PostgreSQL program started and immediately encountered the disk space issue, it logged the specific, detailed reason for its failure:

FATAL: could not create lock file "postmaster.pid": No space left on device

This detailed, program-specific message was sent to the syslog file, which is why you found it there.

Analogy
Think of it this way:

systemd is a supervisor. It logs, "Employee X failed to show up for work today."

syslog is a personal log from Employee X. It logs, "I couldn't get to work because my car ran out of gas."

The supervisor (the systemd journal) only knows about the failure, but the internal log (syslog) contains the specific, root cause of the problem.


```
grep postgre /var/log/syslog

Sep 15 01:51:12 ip-10-1-12-192 postgresql@14-main[896]: Error: /usr/lib/postgresql/14/bin/pg_ctl /usr/lib/postgresql/14/bin/pg_ctl start -D /opt/pgdata/main -l /var/log/postgresql/postgresql-14-main.log -s -o  -c config_file="/etc/postgresql/14/main/postgresql.conf"  exited with status 1:

Sep 15 01:51:12 ip-10-1-12-192 postgresql@14-main[896]: 2025-09-15 01:51:11.940 UTC [901] FATAL:  could not create lock file "postmaster.pid": No space left on device

Sep 15 01:51:12 ip-10-1-12-192 postgresql@14-main[896]: pg_ctl: could not start server

Sep 15 01:51:12 ip-10-1-12-192 postgresql@14-main[896]: Examine the log output.

Sep 15 01:51:12 ip-10-1-12-192 systemd[1]: postgresql@14-main.service: Can't open PID file /run/postgresql/14-main.pid (yet?) after start: No such file or directory

Sep 15 01:51:12 ip-10-1-12-192 systemd[1]: postgresql@14-main.service: Failed with result 'protocol'.
```
we can see that [FATAL] No space left on device. so there is no disk space

when we do df -h, it lists all the file system. But notice that from the grep postgre command that we run on system log, we had the 
exact file system that postgre is using
```
/usr/lib/postgresql/14/bin/pg_ctl start **-D /opt/pgdata/main** 
```
so -D stands for **data directory**, and df -h shows that /dev/nvme0n1 is mounted on that /opt/pgdata/main. 

```
root@ip-10-1-12-192:/etc/postgresql/14/main# df -h

Filesystem       Size  Used Avail Use% Mounted on

udev             224M     0  224M   0% /dev

tmpfs             47M  1.5M   46M   4% /run

/dev/nvme1n1p1   7.7G  1.2G  6.1G  17% /

tmpfs            233M     0  233M   0% /dev/shm

tmpfs            5.0M     0  5.0M   0% /run/lock

tmpfs            233M     0  233M   0% /sys/fs/cgroup

/dev/nvme1n1p15  124M  278K  124M   1% /boot/efi

/dev/nvme0n1     8.0G  8.0G   28K 100% /opt/pgdata

root@ip-10-1-12-192:/etc/postgresql/14/main# free -m

              total        used        free      shared  buff/cache   available

Mem:            465          64         118           1         283         387
```

so we needa cut some space like deleting backup logs. Delete /opt/pgdata/file*.bk files and try to restart Postgres.
**impt, we should start the exact service unit that is affected**. So it shouldnt be start postgresql but that postgresql14 service unit.
```
root@ip-10-1-11-59:/# rm /opt/pgdata/file*.bk
sudo systemctl start postgresql@14-main.service
```

## 2
first we should curl and see if http request via curl can get this html data
```
curl 127.0.0.1:80
```

it doesnt work so we can have some doubts like
- if file exists and has the valid content
```
# Check if the file exists and has the right content
cat /var/www/html/index.html

# Check file permissions
ls -la /var/www/html/index.html

# Check directory permissions
ls -la /var/www/html/
```

so when i did that second command i get
```
ls -la /var/www/html/index.html
-rw------- 1 root root 16 Aug  1  2022 /var/www/html/index.html
```
notice that owner and group is root user. Web server normally runs as ```www-data```. Since apache cant read this file it returns nothing.
So we have to change ownership via **chown**. We are changing the user as www-data and there is colon and theres nothing after colon right?
So the group automatically also becomes www-data.

```
sudo chown www-data: /var/www/html/index.html

## or less secure way is to
chmod 644 /var/www/html/index.html
```
this 644 changes file permissions, where 4= read permission, 2=write permission, 1 = execute permission.
So 6 =4+2 = read + write permission.

before
```
-rw------- 1 root root 16 /var/www/html/index.html
 │││   │││
 │││   └┴┴── Others: no permissions (---)
 ││└──────── Group: no permissions (---)
 └┴───────── Owner: read + write (rw-)
```

after
```
-rw-r--r-- 1 root root 16 /var/www/html/index.html
 │││ │││ │││
 │││ │││ └┴┴── Others: read only (r--)
 │││ └┴┴────── Group: read only (r--)
 └┴┴─────────── Owner: read + write (rw-)
```


- if web server is properly running
```
# Check if any web server process is running
ps aux | grep -E "(apache|httpd|nginx|lighttpd)"

# Check what's listening on port 80
netstat -tlnp | grep :80
# or
ss -tlnp | grep :80

# Check if port 80 is being used at all
lsof -i :80
```

- check firewall rules if its allowing traffic into port 80

its possible that even after all this, curl still doesnt respond with html data. In which case we should see the firewall
```
iptables -L | grep :80

```

we see this error that we are blocking all traffic
```
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
DROP       tcp  --  anywhere             anywhere             tcp dpt:http
```

so we can do iptables -F to delete all rules but thats not a good way. Instead we should target the rule for port 80
-D = delete, INPUT = Traffic coming TO this computer from somewhere else, -p tcp = tcp protocol, --dport 80 = destination port 80,
-j DROP = drop the packet. So basically we are deleting this rule that drops this input traffic's packet when it reaches our port 80
```
# Remove specific rule blocking port 80
iptables -D INPUT -p tcp --dport 80 -j DROP
# Or allow port 80 specifically
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

input is literall infront of ur web server like
```
Your Browser          →     Your Web Server
(127.0.0.1:45678)           (127.0.0.1:80)
                      │
                      │ This arrow = INPUT traffic
                      ↓ (going TO the web server)
                [INPUT chain checks this]
```

## 3
so curling cannot connect to web server and question said there is nginx being used.

we first check nginx config and btw the permission error u can ignore cuz we are not using sudo
```
admin@ip-10-1-12-30:/$ nginx -T
nginx: [alert] could not open error log file: open() "/var/log/nginx/error.log" failed (13: Permission denied)
2025/09/15 05:53:47 [warn] 834#834: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:1
2025/09/15 05:53:47 [emerg] 834#834: unexpected ";" in /etc/nginx/sites-enabled/default:1
nginx: configuration file /etc/nginx/nginx.conf test failed
```
we see that there is ; in the first line so we delete via vim. 
press i for INSERT and once u done change, Esc button and write :wq.
```
sudo vim /etc/nginx/sites-enabled/default
```
that should work and i did
```
# Test configuration
sudo nginx -t

# If test passes, reload nginx
sudo systemctl reload nginx

# Check nginx status
sudo systemctl status nginx
```
its status is running bur still curl doesnt work. We have to look error log. We see that the ; error has been solved by 9/11 but when
we see 9/15 error, we see Too many open files error.
```
sudo tail -f /var/log/nginx/error.log
2022/09/11 16:54:26 [emerg] 5931#5931: unexpected ";" in /etc/nginx/sites-enabled/default:1
2022/09/11 16:55:00 [emerg] 5961#5961: unexpected ";" in /etc/nginx/sites-enabled/default:1
2022/09/11 17:02:07 [emerg] 6066#6066: unexpected ";" in /etc/nginx/sites-enabled/default:1
2022/09/11 17:07:03 [emerg] 6146#6146: unexpected ";" in /etc/nginx/sites-enabled/default:1
2025/09/15 07:08:11 [emerg] 577#577: unexpected ";" in /etc/nginx/sites-enabled/default:1
2025/09/15 07:10:19 [alert] 829#829: socketpair() failed while spawning "worker process" (24: Too many open files)
2025/09/15 07:10:19 [emerg] 830#830: eventfd() failed (24: Too many open files)
2025/09/15 07:10:19 [alert] 830#830: socketpair() failed (24: Too many open files)
2025/09/15 07:11:31 [crit] 830#830: *1 open() "/var/www/html/index.nginx-debian.html" failed (24: Too many open files), client: 127.0.0.1, server: , request: "HEAD / HTTP/1.1", host: "127.0.0.1"
2025/09/15 07:11:58 [crit] 830#830: *2 open() "/var/www/html/index.nginx-debian.html" failed (24: Too many open files), client: 127.0.0.1, server: , request: "HEAD / HTTP/1.1", host: "127.0.0.1"

```
What is open file error? So in linux evertyhing is considered as a file - actual files, network connections, sockets, devices, etc.
So each visitor might be incrementing this file count 
so we need to edit the filecount variable

```
sudo vim /etc/systemd/system/nginx.service
[Service]
LimitNOFILE=10

```
change to 1024 and save changes. then restart nginx

## 4 "Paris": Where is my webserver?
So we need to find a password hidden in the server. Lets try curl first

```
# What we know:
admin@ip-10-1-12-85:~$ curl -v localhost:5000
*   Trying 127.0.0.1:5000...
* Connected to localhost (127.0.0.1) port 5000 (#0)
> GET / HTTP/1.1
> Host: localhost:5000
> User-Agent: curl/7.74.0
> Accept: /
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Server: Werkzeug/2.3.7 Python/3.9.2
< Date: Tue, 16 Sep 2025 05:42:57 GMT
< Content-Type: text/html; charset=utf-8
< Content-Length: 12
< Connection: close
< 
* Closing connection 0
```

(not rly sure recheck) when we curl -v we get 200 success code but we dont get the actual content as response. That means theres something wrong.

And when we just do normal curl, it says unauthorised. So this suggests **server is intentiaonnly blocking something, not real error**. So what makes curl unique?

1) user-agent header (most common)
2) ip address
3) request method (get/post/etc)
4) other headers

so lets try curl with a different header
```
# Try changing ONLY the User-Agent
curl -A "NotCurl" localhost:5000
```
and that gave the password as a reponse!

### but why developers block curl in the first place?
1) prevent web bots/scrapers
2) api rate limiting
3) analaytics/security

other ways that we could have diagonsed besides curl is wget and nc.

### wget
it downloads files from web servers. Diff from curl is that it uses a different user-agent header.
```
wget: User-Agent: Wget/1.20.3
curl: User-Agent: curl/7.74.0
```

### nc
its like cat command but not for files but for networks
```
nc localhost 5000
GET /
[press Enter twice]
```
so nc opens a RAW tcp connection to port 5000. We have to manually type the http method. 

(unsure) so its like the bare minimum http request without extra http headers.

## 5 Python web application
when i did vimstat there was nothing that was abnormally high so i checked status of python app and redis but also they are running fine. The key point of this sceneario is that request is taking **approximately 5s**. U will see why this is crucial.

```
admin@i-0283bd2a7fbaa7962:~$ sudo systemctl status slow-app
● slow-app.service - Slow Flask Application
     Loaded: loaded (/etc/systemd/system/slow-app.service; enabled; preset: enabled)
     Active: active (running) since Tue 2025-09-16 05:54:51 UTC; 11min ago
 Invocation: fcda69a04b7d46f7ba0244e529315eca
   Main PID: 886 (python3)
      Tasks: 1 (limit: 503)
     Memory: 25.2M (peak: 30.1M, swap: 1M, swap peak: 1M)
        CPU: 447ms
     CGroup: /system.slice/slow-app.service
             └─886 /usr/bin/python3 /opt/slow_app.py
Sep 16 05:54:51 i-0283bd2a7fbaa7962 systemd[1]: Started slow-app.service - Slow Flask Application.
Sep 16 05:54:52 i-0283bd2a7fbaa7962 python3[886]:  * Serving Flask app 'slow_app'
Sep 16 05:54:52 i-0283bd2a7fbaa7962 python3[886]:  * Debug mode: off
Sep 16 05:54:52 i-0283bd2a7fbaa7962 python3[886]: WARNING: This is a development server. Do not use it in >
Sep 16 05:54:52 i-0283bd2a7fbaa7962 python3[886]:  * Running on all addresses (0.0.0.0)
Sep 16 05:54:52 i-0283bd2a7fbaa7962 python3[886]:  * Running on http://127.0.0.1:5000
Sep 16 05:54:52 i-0283bd2a7fbaa7962 python3[886]:  * Running on http://10.1.12.23:5000
Sep 16 05:54:52 i-0283bd2a7fbaa7962 python3[886]: Press CTRL+C to quit
Sep 16 06:02:44 i-0283bd2a7fbaa7962 systemd[1]: [🡕] /etc/systemd/system/slow-app.service:9: Special user n>
lines 1-20/20 (END)

sudo systemctl status redis-server
● redis-server.service - Advanced key-value store
     Loaded: loaded (/usr/lib/systemd/system/redis-server.service; enabled; preset: enabled)
     Active: active (running) since Tue 2025-09-16 05:54:51 UTC; 12min ago
 Invocation: 05b9189eb5234a25bc89d7c2ae81f826
       Docs: http://redis.io/documentation,
             man:redis-server(1)
   Main PID: 759 (redis-server)
     Status: "Ready to accept connections"
      Tasks: 6 (limit: 503)
     Memory: 7.5M (peak: 9.4M, swap: 4K, swap peak: 4K)
        CPU: 3.038s
     CGroup: /system.slice/redis-server.service
             └─759 "/usr/bin/redis-server 127.0.0.1:6379"
Sep 16 05:54:51 i-0283bd2a7fbaa7962 systemd[1]: Starting redis-server.service - Advanced key-value store...
Sep 16 05:54:51 i-0283bd2a7fbaa7962 systemd[1]: Started redis-server.service - Advanced key-value s
```

so since there is nothing wrong on the system, we should look at the *application*.
```
admin@i-0283bd2a7fbaa7962:~$ cat /opt/slow_app.py
#!/usr/bin/python3
from flask import Flask
import redis
import time
import os
app = Flask(name)
redis_host = os.getenv('REDIS_HOST', '127.0.0.1')
r = redis.Redis(host=redis_host, port=6379, socket_connect_timeout=1)
@app.route('/')
def get_data():
    try:
        r.ping()
        return "Data from FAST cache!"
    except redis.exceptions.ConnectionError:
        time.sleep(5)
        return "Data from SLOW database!"
if name == 'main':
    app.run(host='0.0.0.0', port=5000)
admin@i-0283bd2a7fbaa7962:~$
```

so as u see, if there is redis connection error, app, or more specifically thread will sleep for 5s. So the issue is that **app cant connect to redis EVEN THO redis is running fine**.

solution:
first check if redis-cli responds with pong with my ping. If that works then the envionment variable is the issue. In the q, the app
**doesnt read from /opt/.env file**, so the **systemd file's env variables matter**. Usually py dotenv reads variables from .env file
but q said nope so check systemd env variables.

```
admin@i-0283bd2a7fbaa7962:~$ cat /opt/slow_app.py #!/usr/bin/python3 from flask import Flask import redis import time import os app = Flask(__name__) redis_host = os.getenv('REDIS_HOST', '127.0.0.1') r = redis.Redis(host=redis_host, port=6379, socket_connect_timeout=1) @app.route('/') def get_data(): try: r.ping() return "Data from FAST cache!" except redis.exceptions.ConnectionError: time.sleep(5) return "Data from SLOW database!" if __name__ == '__main__': app.run(host='0.0.0.0', port=5000) admin@i-0283bd2a7fbaa7962:~$ systemctl cat slow-app # /etc/systemd/system/slow-app.service [Unit] Description=Slow Flask Application After=network.target redis-server.service [Service] Environment="REDIS_HOST=127.0.0.2" ExecStart=/usr/bin/python3 /opt/slow_app.py Restart=always User=nobody Group=nogroup [Install] WantedBy=multi-user.target admin@i-0283bd2a7fbaa7962:~$
```

so in the environment, we see redis host is being declared wrongly as 0.2. Using vim we should change to 1 and v importnatly, **reload the daemon**. This is cuz when we edit unit file, systemd doesnt know this change autamatically. So we need to tell systemd to *re-read service unit files from disk. That is what the first command of daemon-reload does. V simply its like updating env variables for systemd to know.

Then we should restart the py app too
```
sudo systemctl daemon-reload
sudo systemctl restart slow-app

```

