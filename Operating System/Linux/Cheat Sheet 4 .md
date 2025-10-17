## File
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
