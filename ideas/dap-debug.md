---
title: Ideas - DAP_debug_NVIM
date: February 12, 2025
tags: []
---

### Compiling C++ Code
   * g++ --debug -std=c++11 -I/opt/homebrew/include -o program km-2-miles_class_files.c

### Debugging C++ code inside the Neovim 
        - choose file km 
        - pass arguments as --kilometers 10
        - ../data.json file

### Debugging C code inside the Neovim 
   * gcc --debug -o program  km-2-miles_class_files.c -I/opt/homebrew/include -L/opt/homebrew/lib -ljansson

### Python code running 
   * Compiler is already configured and user inputs can be passed as from console screen.
      Press "i" to go to insert mode and then type the parameter 


