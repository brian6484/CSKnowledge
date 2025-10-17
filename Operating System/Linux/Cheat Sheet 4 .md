## Common directories
/var/log: stores both system and application logs. But nowadyas on modern linux, we use systemd journal. The Journal's data is typically stored in a binary format under /var/log/journal so cant just cat. We need to use journalctl commands. 

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


