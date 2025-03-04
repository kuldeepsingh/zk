---
title: Ideas - linux-mm
date: March 03, 2025
tags: [mm, memory, linux]
---
In the latest Linux kernel (as of Linux 5.x series), the memory is organized into several regions, each with specific roles. These regions are defined at both the architecture level (e.g., x86_64, ARM) and the kernel configuration. Here's an overview of the key memory regions in modern Linux kernels:

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

### Important Data-structure
The vmap_area structure is heavily tied to the kernel's memory management subsystem and the page tables. The virtual memory areas represented by vmap_area structures are mapped into the kernel’s page tables, and the kernel's memory management system uses these areas for efficient address space management.
When a virtual memory region is mapped using vmap(), a corresponding vmap_area structure is created to represent the mapping. The kernel maintains a list of such vmap_area structures to keep track of all the non-contiguous regions mapped into the kernel virtual space.

vmap_structure has order which is used to maintain the 2**X order of pages per vmap_structure is a buddy allocator.

