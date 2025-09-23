## System call
It is interface between kernel and user programs. Its a *controlled* way for programs to request kernel services and is the **only way** for programs to access hardware, files, network
from user space.

## CPU Protection ring
Protection ring is hardware-enforced security mechanism built into the CPU that control what the code can do based on its privilege level. Its like airport security clearance level
for software.

### The Ring Structure
```
┌─────────────────────────────────────┐
│           Ring 0 (Kernel)           │  ← Highest Privilege
│  ┌───────────────────────────────┐  │
│  │        Ring 1 (Unused)        │  │
│  │  ┌─────────────────────────┐  │  │
│  │  │     Ring 2 (Unused)     │  │  │
│  │  │  ┌─────────────────┐    │  │  │
│  │  │  │ Ring 3 (User)   │    │  │  │  ← Lowest Privilege
│  │  │  │                 │    │  │  │
│  │  │  └─────────────────┘    │  │  │
│  │  └─────────────────────────┘  │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

### ring privilege levels
#### Ring 0 (Kernel Mode/Supervisor Mode)
Its highest privilege level where OS kernel and device drivers run. BTW device drivers  
are **specialised software programs** that translate between OS and hardware devices. They
are like translators who can speak both OS and hardware lang
