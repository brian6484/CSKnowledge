## Script connecting to 100 hosts, checking process, sending email
```
#!/bin/bash
hosts=("host1" "host2" ... "host100")
for h in "${hosts[@]}"; do
    ssh $h "pgrep process_name" &>/dev/null
    if [ $? -eq 0 ]; then
        echo "$h: process running" >> report.txt
    else
        echo "$h: process not running" >> report.txt
    fi
done
mail -s "Process Report" you@example.com < report.txt
```
Theres list of hosts and we loop each host via ssh and run
```
pgrep process_name
```
to see if process is running. 

If $?, which stores exit status of last command, is equal to 0 then we rite result to report.txt.

Finally, we send email
