---
title: Ideas - linux-vfs
date: March 21, 2025
tags: [vfs]
---

# VFS common file model
   open(), close(), read() and write() will call vfs calls sys_read() and sys_write(). These internally will call the actual file system.
Terminology
  - inode 
       Represents a file or the Directory
  - Dentry
  - Superblock object
    - Stores information concerning a mounted file system.
    - Holds things like device, blocksize, dirty flags, list of dirty inodes etc.
    - Super operations -> like read/write/delete/clear inode etc.
    - Gives pointer to the root inode of this FS
    - Superblock manipulators: mount/umount
  - file object
    - Stores information about the interaction between an open file and a process.
    - File pointer points to the current position in the file from which the next operation will take place.
  - inode object
    - stores general information about a specific file.
    - Linux keeps a cache of active and recently used inodes.
    - All inodes within a file system are accessed by file-name.
    - Linux's VFS layer maintains a cache of currently active and recently used names, called dcache 
  - dcache
    - structured in memory as a tree.
    - each entry or node in tree (dentry) points to an inode.
    - it is not a complete copy of a file tree
    - Note : If any node of the file tree is in the cache then every ancestor of that node is also in the cache.
