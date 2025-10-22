## Useful commands
### grep
```
grep -i "checkpoint"
```
when -i is used with grep, it means case-insensitive. 

search multiple stuff with -E
```
ps aux | grep -E "1247|8901"
```

### crontab
check cron jobs 

crontab -l lists scheduled cron jobs for the current user. Cron is a scheduler that runs commands at specific times.
sudo crontab -l lists cron jobs for the root user (since you're using sudo).

```
sudo crontab -l 
# /var/spool/cron/crontabs/root
# m h  dom mon dow   command
0 */2 * * * /opt/app/scripts/bulk_import.sh >> /var/log/bulk_import.log 2>&1
```

to edit cron jobs u can use the -e = edit tag.
```
sudo crontab -e
```
