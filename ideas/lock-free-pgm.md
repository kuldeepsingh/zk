---
title: Ideas - lock-free-programming
date: May 16, 2025
tags: [lockfree]
---

Lock free programming need to take care of 

 - Hazard Pointer ( Hazard pointer list is maintained) 
     In lock-free structures, a node might be freed by one thread while another thread is still accessing it → this causes use-after-free bugs.

 - ABA probelm (Done used tagged Pointers )
   In CAS-based loops:
        Thread A reads pointer A.
        Thread B removes A, frees it, and reuses the same memory for new node B, which also happens to be at address A.
        Thread A sees the same pointer and CAS succeeds... but it’s not the same logical object anymore!


