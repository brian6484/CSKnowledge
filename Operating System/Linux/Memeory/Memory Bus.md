## Memory bus
Its a collection of electrical pahtways that conenct the CPU and RAM, enabling data transfer between processor and system memory.

### Components of memory bus
1) Address bus - carries memory address from CPU to RAM unidirectionally, width determines max addressable memory
2) Data bus - carries data from CPU to RAM bidirectionally, width affects performance
3) Control bus - carries control signals (read/write commands, timing signals), coordinates when and how data transfer occurs

### Flow
When the CPU needs data:

1) CPU puts the memory address on the address bus
2) CPU sends a "read" signal on the control bus
3) RAM locates the data at that address
4) RAM puts the data on the data bus
5) CPU reads the data from the data bus

For writes, the process is similar but data flows from CPU to RAM.

### Metrics
Bandwidth: How much data can transfer per second (GB/s)
Latency: Time delay between request and response
Clock Speed: How fast the bus operates (MHz/GHz)
