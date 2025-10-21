## Difference between journalctl and systemctl

They're **completely different** tools:

### `systemctl` - Service **CONTROL** 
Its like 리모콘 for services 

**Purpose:** Manage services (start, stop, enable, disable)

```bash
# Control services
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx

# Check service status
systemctl status nginx
systemctl is-active nginx
systemctl is-enabled nginx

# Enable/disable on boot
systemctl enable nginx
systemctl disable nginx

# List services
systemctl list-units --type=service

# View service configuration
systemctl cat nginx
systemctl show nginx
```

**Think of it as:** The **remote control** for services 🎮

---

### `journalctl` - Service **LOGS**
Its for logging purposes

**Purpose:** Read logs from services

```bash
# View logs
journalctl -u nginx
journalctl -u nginx -n 100
journalctl -u nginx --since today
journalctl -u nginx -f  # follow/tail

# Filter logs
journalctl -p err  # errors only
journalctl --since "10 minutes ago"

# Search logs
journalctl -u nginx | grep "error"
```

**Think of it as:** The **DVR/recorder** that saves everything services say 📹

---

## Quick comparison:

| Task | Command |
|------|---------|
| Start a service | `systemctl start nginx` |
| Check if running | `systemctl status nginx` |
| View recent logs | `journalctl -u nginx -n 50` |
| Follow live logs | `journalctl -u nginx -f` |
| Restart service | `systemctl restart nginx` |
| See config file | `systemctl cat nginx` |
| See error logs | `journalctl -u nginx -p err` |


Think of it like:
- `systemctl` = Power button
- `journalctl` = Screen that shows what happened 📺
