## Register
Its a tiny ulttra-fast storage location built directily into hardward(CPU/devices).

The CPU has many registers:
- Some hold data you're calculating with
- Some hold memory addresses
- **Some hold control information about the CPU's current state**
- 
## Types
1) CPU register
2) Hardware Device register

## Role
It can check the current privilege level (whether its ring 0 or 3). 

One of CPU's control registers (often part of the "code segment" register on x86 CPUs) contains bits that indicate the current **privilege level** (Ring 0, Ring 3, etc.). Every time the CPU executes an instruction, it checks this register to see "Am I allowed to do this operation?"

**Simple analogy**: It's like wearing a security badge. The CPU checks its "badge register" before performing privileged operations like accessing hardware-mapped memory addresses.
