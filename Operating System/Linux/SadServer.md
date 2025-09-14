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

# Medium
## 1
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

that should work
  
