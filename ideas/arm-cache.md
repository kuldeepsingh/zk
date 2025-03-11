---
title: Ideas - arm-cache
date: March 11, 2025
tags: []
---
- Cache hierarchy
- Seperate Icahe and Dcache
- Cache hit or miss 

- Cache Coherence
  - In ARM-based SoCs (System on Chips), cache coherence is a critical feature that ensures that all processor cores in a multi-core system see a 
    consistent view of memory, despite each core having its own local cache. Cache coherence protocols ensure that when one core writes to a memory 
    location, other cores that may have cached that same location are updated or [[invalidated]] to avoid inconsistencies.
  - 1)  Cache Coherence Protocol
      ARM SoCs use a protocol known as the MESI (Modified, Exclusive, Shared, Invalid) protocol
      -  Modified (M): The data is only in this cache, and it has been modified from the main memory.
      -  Exclusive (E): The data is only in this cache, and it has not been modified.
      -  Shared (S): The data is in more than one cache, and it is not modified.
      -  Invalid (I): The data in the cache is invalid and must be fetched again.

  - 2)  AMBA Coherency Extensions (ACE)
      The AMBA ACE protocol is used to handle cache coherence in multi-core ARM SoCs. ACE defines a set of transactions that help maintain consistency:
      -  Read/Write Transactions: ACE enables coherency by propagating read and write operations across all cores that may hold copies of a cache line.
      -  Invalidate and Update Transactions: When one core writes to a memory location, ACE makes sure that other cores' caches that 
         hold that line are either invalidated or updated to reflect the new value.
      -  Exclusive and Shared States: It allows a core to request an exclusive copy of data or to update data while ensuring that all 
         other caches in the system remain consistent.

  - 3)  Cache Coherent Interconnects
     ARM SoCs use cache coherent interconnects, such as the AMBA 5 AArch64 interconnect (like Cortex-M or Cortex-A processors), which implements the 
     ACE protocol across the interconnect network. This ensures that all cores in the system maintain coherence when they access shared memory or
     peripheral devices.

      - The Global Interrupt Controller (GIC) and Memory Management Unit (MMU) work together to ensure that the memory access is properly
        translated and maintained coherent.

  - 4)  Multi-Level Caches and Snoop Control
     ARM SoCs typically implement multi-level caching (L1, L2, and sometimes L3). For multi-core systems, a Snoop Control Unit (SCU) or 
     Coherent Interconnect is used to manage coherence by snooping on transactions between cores and memory.

     When a processor core tries to write to a location in its cache, the SCU will check if any other cores have a copy of that location in 
     their cache. If any core has a copy, the SCU will enforce cache coherence by invalidating or updating those caches.
     The SCU can also ensure that reads and writes are properly serialized and that memory consistency is maintained across all processors.

  - 5) Cache Coherence in ARM’s Multi-core Processors
     For ARM multi-core processors (like those in the Cortex-A series), ARM uses a Cache Coherent Interconnect (CCI) or AMBA 4 ACE to handle memory
     coherence between the cores. The interconnect enables the cores to be part of a coherent memory system by allowing each core's cache to remain 
     synchronized.

    -  L2 and L3 Cache Coherence: The interconnect handles coherence between L2 caches, and, in more advanced systems, may also handle coherence 
       with L3 cache. This is particularly useful in server-grade or high-performance chips.
    -  Global Memory Consistency: The memory controller ensures that there is a global consistency of memory across all cores. It coordinates the 
       read and write requests to the global memory to avoid race conditions and maintain synchronization.

  - 6) Coherence with Non-CPU Components
     ARM SoCs also typically manage coherence between CPUs and non-CPU components like GPUs, accelerators, and DMA controllers. This is handled 
     via mechanisms like Coherent Memory Regions and the ACE-Lite protocol, which can be used by non-CPU components to request coherency, ensuring 
     that the peripheral devices don't access stale or inconsistent data.

  - 7) Hardware and Software Cooperation
     ARM processors and SoCs typically involve a mix of hardware mechanisms and software (like operating systems and hypervisors) to ensure that 
     cache coherence is maintained efficiently. The hardware manages cache invalidation and update, while the software (such as the operating system's 
     memory management unit or a hypervisor in virtualized environments) makes sure that memory regions are properly mapped and that processes are 
     synchronized.

