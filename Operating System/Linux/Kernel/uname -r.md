## uname -r
displays kernel release version of ur OS. -r = kernel release version. Output can be 5.4.0-91-generic.

-s = system name (like Linux)

-n = nodename (name of machine's name on network, specifically the hostname)

-i = hardware platform 

-a = all 

A typical `uname -a` output on a Linux machine might look like this:

`Linux mydesktop 5.4.0-91-generic #102-Ubuntu SMP Fri Apr 16 16:53:42 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux`

**In this example:**

* **Linux**: Kernel Name (`-s`)
* **mydesktop**: Network Node Hostname (`-n`)
* **5.4.0-91-generic**: Kernel Release (`-r`)
* **#102-Ubuntu SMP Fri Apr 16 16:53:42 UTC 2021**: Kernel Version (`-v`)
* **x86_64 x86_64 x86_64**: Machine Hardware, Processor Type, and Hardware Platform (`-m`, `-p`, `-i`)
* **GNU/Linux**: Operating System (`-o`)
