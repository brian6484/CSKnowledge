## VM
- Has its own full independent OS on top of a hypervisor
- Hypervisor is **ONLY FOR VM** not Docker, which creates and manages full virtualised hardware (like gates) for each guest OS

## Docker
- It virtualises the OS by sharing the hosts's OS. SO they only contain application code and dependencies 
- No Hypervisor, Uses a container runtime to run applications directly onto hosts' OS (kernel). So instead of virtualising the
hardware, containers actually use the host OS kernel and its features so its lightweight
