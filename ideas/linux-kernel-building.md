---
title: Ideas- linux-kernel-building
date: March 11, 2025
tags: [linux, kernel, build]
---

# TOP View of linux building 
-------
- make compiles and links the kernel image. This is a single file named vmlinuz.
- make modules compiles individual files for each question you answered M during kernel config. The object code is linked against freshly built kernel. 
- make install installs your built kernel to /vmlinuz.
- make modules_install installs your kernel modules to /lib/modules or /lib/modules/<version>.

# Compilation Process
-------
 - Compiler: The compiler takes source code, such as .cc, and lowers it down to a .s file, an assembly file which textually describes machine code for a 
   specific architecture.
 - Assembler: Another toolchain program, the assembler, assembles each .s into a .o file, an object file4. An assembly file is merely a textual 
   representation of an object file; assemblers are not particularly interesting programs. 
 - Linker: links all of your object files into a final executable or binary, traditionally given the name a.out
