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
