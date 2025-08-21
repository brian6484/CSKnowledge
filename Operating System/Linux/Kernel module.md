## Kernel module
Code that is loaded into kernel at runtime for upgrade (device drivers, filesystems, etc). Unlike builtin kernel features, modules
can be inserted/removed without rebooting (so convienent!)

## commands
insmod - directly inserts module into kernel but **doesnt handle dependency**, so if that module require other modules u need to
manually download

modprob - better way by **automatically resolving dependencies**

```
sudo insmod mydriver.ko       # Inserts module directly
sudo modprobe mydriver        # Inserts module and its dependencies

sudo rmmod <module>           # Remove a module directly
sudo modprobe -r <module>     # Remove module and unused dependencies
```
