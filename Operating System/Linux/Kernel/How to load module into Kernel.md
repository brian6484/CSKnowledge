## kernel module?
its a block of compiled code that can be loaded into running kernel on demand, mainly used for device drivers/filesystem support. It **does NOT** require reboot.
We use `modeprobe` to load module like
```
sudo modprobe <module_name>
```

then verify the module is loaded with `lsmod` command, which lists all active kernel modules.
```
lsmod | grep <module_name>
```

we can remove unused module via `rmmod`
