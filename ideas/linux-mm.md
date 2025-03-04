---
title: Ideas - linux-mm
date: March 03, 2025
tags: [mm, memory, linux]
---
In the latest Linux kernel (as of Linux 5.x series), the memory is organized into several regions, each with specific roles. These regions are defined at both the architecture level (e.g., x86_64, ARM) and the kernel configuration. 

# An overview of the key memory regions in modern Linux kernels:
----------
- 1. Text Segment (Code Segment)

Purpose: Stores the executable code of the kernel (instructions).
Location: This is typically located in the lower part of the kernel's virtual address space.
Properties: The text segment is typically read-only to prevent accidental modification of the code, ensuring system stability and security.

- 2. Data Segment
Purpose: Stores initialized global variables, static variables, and read-only data (like constants and strings).
Location: This memory is usually found right after the text segment.
Properties: This region is read-write and can be modified by the kernel during execution.

- 3. BSS Segment (Block Started by Symbol)
Purpose: This segment contains uninitialized global and static variables.
Location: It's typically placed right after the data segment.
Properties: The kernel initializes this memory region to zero at startup. It occupies memory but does not take up space in the kernel binary.

- 4. Heap
Purpose: Used for dynamic memory allocation during kernel operation.
Location: The heap grows upwards (towards higher memory addresses) as needed by the kernel.
Properties: It’s used for allocating memory dynamically during kernel execution via functions like kmalloc() or vmalloc(). It can grow based on requirements.

- 5. Stack
Purpose: Used by the kernel for function call stack frames, local variables, and context switching.
Location: The kernel stack is typically located at the high end of the kernel's virtual memory space and grows downward (towards lower addresses).
Properties: Each process or thread has its own stack. The kernel uses a separate stack for handling system calls, interrupts, and exceptions.

- 6. Kernel Space
Purpose: This region is reserved for kernel data structures, code, and other critical kernel operations.
Location: This region typically occupies a large portion of the upper end of the system's address space, above the user space.
Properties: On a 64-bit system, this space usually starts at the address 0xFFFF800000000000 (or equivalent on other architectures) and goes up to the system's maximum virtual address. It is split between:
The kernel text, data, and BSS sections (code, data, etc.).
The kernel heap and kernel stack.
Memory-mapped I/O regions, which allow the kernel to communicate with hardware devices.

- 7. Page Tables (Kernel Page Tables)
Purpose: Store mappings between virtual addresses and physical addresses.
Location: Located in the kernel space. Each process (or kernel itself) has a page table structure that the CPU uses to translate virtual addresses to physical memory addresses.
Properties: These tables are maintained by the kernel for memory management purposes, and the MMU (Memory Management Unit) uses them for virtual-to-physical address translation.

- 8. Interrupt Descriptor Table (IDT)
Purpose: The IDT holds addresses of interrupt service routines (ISRs) and system call handlers.
Location: This structure is located in kernel space and is mapped to a fixed location in memory during kernel startup.
Properties: The IDT is a low-level data structure that the CPU uses to handle interrupts and exceptions.

- 9. I/O Memory Mapped Space (I/O Memory)
Purpose: This region is used for communication between the kernel and hardware devices via memory-mapped I/O (MMIO).
Location: This memory region is mapped into the kernel address space, and it’s used to read from and write to device registers.
Properties: Memory-mapped I/O is typically a part of the system's memory space but is reserved specifically for interacting with devices like graphics cards, network cards, and other peripherals.

- 10. Per-CPU Variables
Purpose: Stores data that is specific to each CPU, such as interrupt handling, per-CPU counters, and thread-local storage.
Location: This memory is usually set aside for each processor in a multi-core system. Each CPU has its own local memory region for these variables.
Properties: These are typically found in a special part of kernel space that is designed to be accessed by a specific CPU without contention.

- 11. Kernel Memory Pools
Purpose: These pools are used by the kernel to manage memory in fixed-size blocks. They are important for managing memory efficiently for small allocations.
Location: Kernel memory pools are managed by specific subsystems like the slab allocator, which is responsible for allocating small memory chunks for kernel objects.
Properties: Memory in pools is used to manage objects like file descriptors, inodes, and other kernel structures. The kernel uses a variety of allocators like slab, slub, and smp for this purpose.

- 12. VMALLOC Space
Purpose: This space is used by the kernel to allocate memory that is not contiguous in physical memory but needs to appear contiguous in virtual memory.
Location: This region is located in the kernel's virtual address space and is mapped to non-contiguous physical memory.
Properties: VMALLOC is used for large allocations that don't need to be contiguous in physical memory (e.g., for large arrays or buffers).

- 13. Memory-Mapped Files (mmap)
Purpose: Kernel space includes mappings for memory-mapped files that the kernel interacts with.
Location: These regions are typically mapped into the kernel's address space, which allows the kernel to read from and write to files via the memory mapping.
Properties: Memory-mapped files provide an efficient mechanism for inter-process communication (IPC) or working with large files.
Key Memory Layout on 64-bit Systems:
Here’s an example layout of a typical 64-bit kernel memory space:

User Space: The lower part of the address space is for user-space applications, typically ranging from 0x0000000000000000 to 0xFFFF800000000000 (depending on architecture).

Kernel Space: The higher part of the address space is used for kernel code, data, and management structures. For instance, it could start at 0xFFFF800000000000 and extend to 0xFFFFFFFFFFFFFFFF.

Virtual Address Space for Kernel: The exact address ranges for kernel space and user space can vary based on the architecture and kernel configuration.

Conclusion:
In the latest Linux kernel, the memory is divided into several key regions to support various purposes, such as executing code, managing data, handling interrupts, managing hardware I/O, and storing system-critical structures. These regions include the text segment, data segment, heap, stack, kernel space, memory-mapped I/O, page tables, and more. Each region plays a specific role in maintaining the kernel's functionality, efficiency, and stability.

The exact layout and memory regions can vary depending on the architecture (x86_64, ARM, etc.) and the specific configuration of the kernel, but this is the general structure that applies across most systems.

======================================================================================================================================================


# Important Data-structure
----------

The vmap_area structure is heavily tied to the kernel's memory management subsystem and the page tables. The virtual memory areas represented by vmap_area structures are mapped into the kernel’s page tables, and the kernel's memory management system uses these areas for efficient address space management.
When a virtual memory region is mapped using vmap(), a corresponding vmap_area structure is created to represent the mapping. The kernel maintains a list of such vmap_area structures to keep track of all the non-contiguous regions mapped into the kernel virtual space.

vmap_structure has order which is used to maintain the 2**X order of pages per vmap_structure is a buddy allocator.

======================================================================================================================================================


# Page Table management
----------

In the **Linux kernel**, **page table management** is a critical component of the memory management subsystem, ensuring that virtual memory addresses used by applications and the kernel are correctly mapped to physical memory addresses. The Linux kernel uses a **multi-level page table system** to manage memory efficiently, particularly on architectures like **x86_64**. The page table hierarchy allows for efficient mapping of virtual memory to physical memory while reducing the size of page tables.

### Basic Concepts

- **Virtual Memory**: The abstraction of memory that allows processes to have the illusion of a large, contiguous block of memory, even though physical memory may be fragmented.
- **Physical Memory**: The actual memory (RAM) that is available for use by processes and the kernel.
- **Page**: A small, fixed-size block of memory. In Linux, a typical page size is 4 KB (although larger page sizes like 2 MB or 1 GB are possible).
- **Page Table**: A data structure used by the kernel to store mappings between virtual addresses and physical addresses.

### Page Table Hierarchy in Linux

Linux uses a **multi-level page table system** to manage memory on 64-bit architectures like **x86_64**. The key components of the page table system are:

1. **Page Global Directory (PGD)**: The top-level page table, containing pointers to the next level (Page Upper Directory or PUD).
2. **Page Upper Directory (PUD)**: This level maps large regions of virtual memory, pointing to the next level (Page Middle Directory).
3. **Page Middle Directory (PMD)**: This level maps even larger regions of memory, pointing to the page table (PT).
4. **Page Table (PT)**: The lowest level in the hierarchy that holds entries for the actual pages in physical memory.

Each level in the page table hierarchy consists of a set of entries that either point to the next level or directly to physical memory pages (in the case of the final level).

### Virtual Address Breakdown (for 64-bit x86_64 architecture)

In a typical **x86_64** architecture, the 64-bit virtual address is divided into several parts for the page table lookup:

1. **PML4 (Page Map Level 4)** – 9 bits (entries for 512 GB of memory)
2. **PDP (Page Directory Pointer)** – 9 bits
3. **PD (Page Directory)** – 9 bits
4. **PT (Page Table)** – 9 bits
5. **Offset** – 12 bits (for a 4 KB page size)

This results in a total of 48 bits of the virtual address being used for addressing, and the rest is used for other purposes (such as flags or addressing modes). Here's a general breakdown of the address translation process in **x86_64**:

```
Virtual Address:
| PML4 | PDP  | PD   | PT   | Offset |
| 9 bits | 9 bits | 9 bits | 9 bits | 12 bits |
```

### Page Table Entries (PTE)

Each entry in the page table holds information about the mapping of a virtual page to a physical page. A page table entry (PTE) contains:
- The **physical address** of the page frame.
- Flags that indicate the page's attributes (such as read/write, user/supervisor, etc.).
- A **valid bit** to indicate whether the entry is valid.
- Other flags such as **dirty bit**, **accessed bit**, and **global bit**.

### Page Table Structure in Linux

For a 64-bit system, the Linux kernel typically uses a **four-level page table** structure, but modern CPUs may have support for larger pages like **2 MB** or **1 GB**, which modifies how page table entries are used.

### Diagram: Page Table Management in Linux

Here is a simplified diagram of how **page table entries (PTEs)** are organized in a multi-level page table structure in the Linux kernel:

```
  +--------------------+            +------------------+            +-------------------+            +-------------------+
  |     PML4 Table     |------------|  PDP Table       |------------|    PD Table       |------------|    PT Table       |
  +--------------------+            +------------------+            +-------------------+            +-------------------+
           |                             |                            |                            |
           V                             V                            V                            V
      0x0000  -> entry[0]         0x0000 -> entry[0]          0x0000 -> entry[0]           0x0000 -> entry[0]
      0x0800  -> entry[1]         0x0800 -> entry[1]          0x0800 -> entry[1]           0x0800 -> entry[1]
        .                               .                            .                             .
        .                               .                            .                             .
      0x1000  -> entry[n]         0x1000 -> entry[n]          0x1000 -> entry[n]           0x1000 -> entry[n]
           |                             |                            |                            |
           V                             V                            V                            V
   +-------------------+           +--------------------+            +------------------+          +------------------+
   |  Physical Pages   |<----------|    Physical Pages |<-----------|  Physical Pages  |<---------|  Physical Pages  |
   +-------------------+           +--------------------+            +------------------+          +------------------+

Each of the tables (PML4, PDP, PD, PT) holds a pointer to the next level. The last level (Page Table) holds the actual **Physical Pages**.
```

### Steps in Virtual Address Translation

1. **Start at the PML4**: The first 9 bits of the virtual address are used to index into the PML4 table.
2. **Move to the PDP**: The next 9 bits are used to index into the PDP table.
3. **Move to the PD**: The next 9 bits are used to index into the PD table.
4. **Move to the PT**: The next 9 bits are used to index into the PT table.
5. **Final Physical Address**: The last 12 bits (offset) specify the exact location within the page, pointing to the physical address.

Each level of the table stores an entry that can either point to the next level (if the address range is large) or directly map to a **physical page** (at the PT level).

### Memory Management and Page Table Operations in Linux

1. **Page Table Creation**: 
   - When a process is created, the kernel allocates memory for its page tables. The process's page table is initially populated with entries for its user-space memory.
   - The kernel uses **functions like `alloc_page()`** to allocate memory pages for page tables.

2. **Page Fault Handling**: 
   - When a process tries to access a page that is not mapped, a page fault occurs. The kernel handles the page fault by checking the page table, allocating a new page if needed, and updating the page table entries.

3. **Page Table Walk**: 
   - The **CPU** performs a **page table walk** to translate virtual addresses to physical addresses, using the PML4, PDP, PD, and PT entries.
   - Linux kernel maintains **in-memory structures** like **`struct mm_struct`** to keep track of a process's memory mappings.

4. **Mapping Memory**: 
   - Linux supports multiple types of memory mapping:
     - **User Space**: The kernel maps user-space memory to physical memory, using regular page table entries.
     - **Kernel Space**: The kernel manages mappings for its own space (e.g., kernel code, kernel heap).
     - **Huge Pages**: For large memory regions, Linux can map **huge pages** (e.g., 2 MB or 1 GB) using the appropriate page table entry.

5. **Swapping and Memory Management**:
   - The kernel uses **swap space** (typically on disk) to back pages that are not frequently used, swapping them in and out as needed. However, **huge pages** generally cannot be swapped out due to their size and contiguity requirements.
   
6. **Page Table Destruction**:
   - When a process terminates, the kernel cleans up the page tables, freeing any associated memory pages and ensuring that the page table structure is no longer needed.


# Conclusion
---------
In Linux, the page table management system is hierarchical and consists of multiple levels of page tables (PML4, PDP, PD, and PT) that map virtual addresses to physical memory. This system provides efficient memory management, reducing overhead when dealing with large amounts of memory, and is essential for virtual memory management on 64-bit systems. The kernel manages this hierarchy and performs operations like page faults, memory mapping, and swapping to ensure that processes can access the memory they need efficiently.

======================================================================================================================================================


# Memory Zones 
----------

In the **Linux kernel**, **memory zones** refer to different areas or types of physical memory that are organized to manage memory efficiently. These zones help the kernel optimize memory allocation and deal with various memory regions in the system based on their characteristics (such as availability, speed, and whether they are reserved for specific purposes like DMA or high memory).

### Memory Zones in Linux Kernel

Linux uses several distinct **memory zones** to categorize different regions of memory. Each zone is used for a specific type of memory allocation, and the kernel’s memory allocator makes decisions based on these zones to optimize memory management. These zones are used primarily for organizing physical memory.

Here are the key **memory zones** in the Linux kernel:

### 1. **ZONE_DMA**
   - **Purpose**: This zone is reserved for Direct Memory Access (DMA) operations.
   - **Use Case**: Some devices (especially older ones) require that the memory they access must be within a specific low physical memory range (usually below 4 GB). The **ZONE_DMA** region contains such memory and is primarily used for **DMA** by devices that are limited to low memory addresses.
   - **Range**: Typically, this zone contains memory below 16 MB (this can vary depending on the architecture). For example, on x86 systems, it's often the first 16 MB of RAM.

   **Example**:
   - A network card or sound card may need to access memory addresses within this range to transfer data between the device and system memory.

### 2. **ZONE_NORMAL**
   - **Purpose**: This is the most common memory zone and is used for allocating general-purpose memory for user space and kernel space.
   - **Use Case**: The **ZONE_NORMAL** region contains the bulk of system memory, and this is where most user-space and kernel allocations occur.
   - **Range**: It includes the majority of physical RAM available on the system, typically above the **ZONE_DMA** region but below a certain address that is reserved for **high memory**.

   **Example**:
   - When user-space applications or the kernel request memory (using `malloc()` or `kmalloc()`), the memory allocator tries to fulfill the request from **ZONE_NORMAL**.

### 3. **ZONE_HIGHMEM (High Memory)**
   - **Purpose**: This zone is used to manage memory that is above the 4 GB limit (on 32-bit systems) or the high end of available memory (on 64-bit systems).
   - **Use Case**: On **32-bit systems**, the **ZONE_HIGHMEM** zone holds memory that cannot be directly accessed by the kernel due to the 4 GB virtual address space limitation. This memory must be mapped into the kernel’s address space before it can be used.
   - **Range**: On 32-bit systems, **ZONE_HIGHMEM** is above 900 MB, or wherever the 4 GB boundary is. On 64-bit systems, **ZONE_HIGHMEM** is used for memory above 4 GB, which is not directly accessible from the kernel's normal address space.

   **Example**:
   - A 32-bit system with 8 GB of RAM: Only the first 4 GB can be directly accessed by the kernel. The remaining 4 GB is in **ZONE_HIGHMEM** and requires special handling.
   - On **64-bit systems**, this zone is used for memory above 4 GB, and it is accessible in a different way than memory below that threshold.

### 4. **ZONE_MOVABLE**
   - **Purpose**: The **ZONE_MOVABLE** zone is a special memory area used by the kernel for movable pages, such as those associated with processes or memory allocations that might need to be moved (for example, during memory compaction or reclaiming).
   - **Use Case**: This zone is typically used for memory pages that may be moved during **memory compaction** (a process where fragmented memory pages are rearranged to create larger contiguous blocks of memory).
   - **Range**: The zone can be defined dynamically by the kernel based on the system's memory configuration.

   **Example**:
   - **ZONE_MOVABLE** is used when pages that were originally allocated from **ZONE_NORMAL** are moved to higher addresses in order to free up lower memory regions for other processes or kernel operations.

### 5. **ZONE_DMA32**
   - **Purpose**: The **ZONE_DMA32** zone is an extension of **ZONE_DMA**, but with a larger addressable range.
   - **Use Case**: This zone is used for systems where the devices can address memory ranges from 0 to 4 GB. It typically applies to systems that support 32-bit DMA but require a larger address space than the traditional **ZONE_DMA**.
   - **Range**: Typically, it includes memory from **16 MB to 4 GB** (on systems that support 32-bit addresses).

   **Example**:
   - On **x86_64 systems**, this zone might include memory from **16 MB to 4 GB**. Devices requiring access to memory in this range use **ZONE_DMA32** for their DMA operations.

### 6. **ZONE_DEVICE**
   - **Purpose**: This zone is used for memory that is specifically allocated for **device-specific memory** regions, like those used by hardware devices, including memory-mapped device memory.
   - **Use Case**: It is typically used for devices that require special allocations that aren’t ordinary user or kernel memory. This might include **frame buffers**, **PCI memory regions**, and other types of hardware device buffers.
   - **Range**: The range of **ZONE_DEVICE** memory depends on the specific hardware configuration and is typically allocated dynamically by the kernel.

   **Example**:
   - Allocating memory for a **GPU framebuffer** or **PCI device** would typically happen within **ZONE_DEVICE**.

---

### Linux Memory Zone Allocation

The memory zones are primarily managed by the **buddy allocator** and the **slab allocator** in the Linux kernel. The **buddy allocator** is used for allocating larger chunks of memory (e.g., for page frame allocations), while the **slab allocator** is used for smaller, more frequent memory allocations (e.g., kernel objects and caches).

- When memory is allocated, the kernel attempts to fulfill the request from the **ZONE_NORMAL** area. If that is exhausted, it may attempt to use **ZONE_DMA**, **ZONE_DMA32**, or **ZONE_HIGHMEM**, depending on the system architecture and allocation requests.
- **High-memory zones** (like **ZONE_HIGHMEM**) can be **mapped into kernel space** via the **vmalloc()** function when needed.

### Summary of Memory Zones

| **Memory Zone**    | **Purpose**                                                | **Range**                                                |
|--------------------|------------------------------------------------------------|----------------------------------------------------------|
| **ZONE_DMA**       | For devices using DMA (Direct Memory Access)               | Typically 0-16 MB (architecture dependent)                |
| **ZONE_NORMAL**    | General-purpose memory used by the kernel and user space   | Typically below the high memory threshold                |
| **ZONE_HIGHMEM**   | Memory above the accessible kernel address (32-bit systems) | Above 4 GB on 64-bit systems, 900 MB to 4 GB on 32-bit    |
| **ZONE_MOVABLE**   | For pages that can be moved during memory compaction       | Dynamically allocated, used for movable memory           |
| **ZONE_DMA32**     | Extended DMA area for devices using 32-bit DMA              | Typically 16 MB - 4 GB                                    |
| **ZONE_DEVICE**    | Memory allocated for device-specific regions               | Dynamically allocated for devices like GPUs and PCI       |

### Diagram: Memory Zones in Linux

```
+------------------------------------------+
|                  ZONE_DMA                |  (Low memory region for devices requiring DMA access)
+------------------------------------------+
|                  ZONE_DMA32              |  (Extended DMA range for devices)
+------------------------------------------+
|                  ZONE_NORMAL             |  (General-purpose memory used by user and kernel space)
+------------------------------------------+
|                  ZONE_HIGHMEM            |  (High memory region, mapped only as needed)
+------------------------------------------+
|                  ZONE_DEVICE             |  (Memory used for device-specific memory, like framebuffers)
+------------------------------------------+
```

### Conclusion

The Linux kernel uses memory zones to organize physical memory into different categories to optimize for different use cases (e.g., DMA, user-space memory, high memory). These zones allow the kernel to efficiently allocate memory based on its intended usage, ensure compatibility with older devices (DMA requirements), and handle systems with large amounts of RAM.

======================================================================================================================================================

# HUGE Pages
---------
Huge pages are usually allocated in chunks of 2 MB (default) or 1 GB on supported architectures. The allocation of huge pages is managed by the kernel through huge page pools and transparent huge pages (THP), which make it easier for applications to take advantage of large pages without needing to explicitly request them.

- Transparent Huge Pages (THP): Linux can automatically promote regular pages to huge pages for certain workloads without the user needing to configure it manually. This is often used for processes that are memory-intensive.

- Static Huge Pages: The kernel can also allocate a fixed number of huge pages at boot time using the hugepages parameter, which specifies how many 2 MB or 1 GB pages should be reserved for use.
======================================================================================================================================================


# Summary of Memory Allocation Methods
---------

#-----------------------------------------------------------------------------------------------------------------------------------------------------
 **Allocator**	   |                     **Use Case**	                                           |             **Memory Type**
#-----------------------------------------------------------------------------------------------------------------------------------------------------
Slab Allocator	   | Allocates small objects and kernel structures (e.g., task structs)	           | Kernel objects, fixed-size allocations
#-----------------------------------------------------------------------------------------------------------------------------------------------------
Buddy Allocator	   | Allocates larger, contiguous blocks of memory,Large contiguous mm allocations | (e.g., for kernel pages)
#-----------------------------------------------------------------------------------------------------------------------------------------------------
vmalloc()          | Allocates large blocks of memory in virtual space	                           | Virtual memory, non-contiguous physical pages
#-----------------------------------------------------------------------------------------------------------------------------------------------------
kmalloc()          | Allocates smaller contiguous memory blocks in kernel space	                   | Small, contiguous kernel memory
#-----------------------------------------------------------------------------------------------------------------------------------------------------
High Memory        | Allocates memory that is not directly accessible in 32-bit systems	           | Memory above 4 GB boundary (on 32-bit systems)
#-----------------------------------------------------------------------------------------------------------------------------------------------------
Memory Pools       | Allocates memory for devices and special use cases	                           | DMA, device memory, movable memory
#-----------------------------------------------------------------------------------------------------------------------------------------------------
Contiguous Memory  | Allocates physically contiguous memory for DMA or I/O operations	           | Contiguous memory for devices requiring DMA
#-----------------------------------------------------------------------------------------------------------------------------------------------------
Page Allocation    | Allocates pages of memory for kernel or user space	                           | Pages (4 KB or larger)
#-----------------------------------------------------------------------------------------------------------------------------------------------------
Huge Pages         | Allocates large pages to optimize memory for large applications	           |Large contiguous pages (e.g., 2 MB or 1 GB)
#-----------------------------------------------------------------------------------------------------------------------------------------------------

Each of these allocation mechanisms serves a specific purpose and is optimized for particular types of memory requests, helping the Linux kernel manage memory efficiently and reduce overhead.




