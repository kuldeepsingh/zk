---
title: Ideas - multithreading
date: May 16, 2025
tags: []
---
# IPC in Linux (https://github.com/shake0/IPC-demo)

Inter-process communication (IPC) refers to the mechanisms by which processes in an operating system communicate with each other. In Linux, there are several IPC mechanisms available, each with its own advantages and disadvantages.

Here are some common IPC mechanisms available in Linux and when to use them:

1. Pipes
---------
- Named - unrelated processes. 
- Unnamed - Between the related threads of the same parent

Pipes are a simple form of IPC that allow two related processes to communicate. Pipes can be either named or unnamed. Unnamed pipes are created with the pipe() system call and can only be used between related processes, such as a parent and child process. Named pipes, also known as FIFOs, are created with the mkfifo() system call and can be used between unrelated processes.

Pipes are useful when you need to transfer a small amount of data between two related processes. Since pipes are implemented using the kernel's memory, they are fast and efficient. Pipes can also be used to chain commands together in a shell script.

How pipes are implemented at the kernel level:

Pipes are implemented as a pair of file descriptors in the kernel. When a process creates a pipe using the pipe() system call, the kernel creates a pair of file descriptors, one for reading and one for writing. The two file descriptors are connected by a pipe, which acts as a buffer for data that is written by one process and read by another.

Security:

From a security perspective, pipes can be vulnerable to eavesdropping by other processes with access to the same file system. To mitigate this risk, it is important to ensure that the permissions on the pipe are set appropriately.

2. Message Queues
---------

Message queues are a form of IPC that allows multiple processes to communicate. Message queues are created with the msgget() system call and are accessed using functions such as msgsnd() and msgrcv(). Message queues are useful when you need to transfer a larger amount of data between processes, as message queues can hold more data than pipes.

Message queues are also useful when you need to transfer data asynchronously. A process can write a message to a message queue and then continue with other tasks, while another process can read the message from the queue when it is ready.

How MQs are implemented at the kernel level: 

Message queues are implemented as a system resource in the kernel. When a process creates a message queue using the msgget() system call, the kernel allocates memory to hold the messages that will be exchanged through the queue. The process can then use functions such as msgsnd() and msgrcv() to send and receive messages from the queue.

Security: 

From a security perspective, message queues can be vulnerable to attacks such as message spoofing or message tampering. To mitigate these risks, it is important to use appropriate authentication and encryption mechanisms to ensure that messages are being sent and received only by authorized parties. 

3. Shared Memory
---------

Shared memory is a form of IPC that allows multiple processes to share a portion of memory. Shared memory is created with the shmget() system call and is accessed using functions such as shmat() and shmdt(). Shared memory is useful when you need to transfer a large amount of data between processes quickly, as the data is not copied between processes.

Shared memory is also useful when you need to synchronize data between processes. Since multiple processes can access the shared memory simultaneously, synchronization techniques such as semaphores or mutexes can be used to prevent race conditions.

How shared memory are implemented at the kernel level: 

Shared memory is implemented as a system resource in the kernel. When a process creates a shared memory segment using the shmget() system call, the kernel allocates a block of memory that can be accessed by multiple processes. The process can then attach to the shared memory segment using the shmat() system call and read and write data directly to the shared memory.

Security: 

From a security perspective, shared memory can be vulnerable to attacks such as buffer overflow or privilege escalation if appropriate precautions are not taken. It is important to ensure that the shared memory segment is properly initialized and that access to the segment is controlled using appropriate permissions. 

4. Sockets
---------

Sockets are a form of IPC that allow processes on different machines to communicate over a network. Sockets are created with the socket() system call and are accessed using functions such as bind(), listen(), accept(), connect(), send() and recv(). Sockets are useful when you need to transfer data between processes on different machines, or when you need to transfer data over a network.

Sockets are also useful when you need to transfer data asynchronously. A process can write data to a socket and then continue with other tasks, while another process can read the data from the socket when it is ready.

How sockets are implemented at the kernel level: 

Sockets are implemented as a system resource in the kernel. When a process creates a socket using the socket() system call, the kernel allocates memory to hold the socket data structures. The process can then use functions such as bind(), listen(), accept(), connect(), send() and recv() to send and receive data through the socket.

Security:  

From a security perspective, sockets can be vulnerable to attacks such as man-in-the-middle attacks or data injection if appropriate security measures are not in place. It is important to use appropriate authentication and encryption mechanisms to ensure that data is being sent and received only by authorized parties.

In addition to these considerations, it is also important to ensure that processes have appropriate permissions to access IPC mechanisms, and that the operating system is configured to limit access to these mechanisms to only authorized users and processes. 

5. Semaphores:
------------

Semaphores are another type of IPC mechanism in Linux that allow processes to synchronize and coordinate their access to shared resources. Semaphores are implemented as a system resource in the kernel, and they can be used to control access to shared memory segments, files, and other resources.

sem_t semaphore;
sem_init(&semaphore, 0, 4); //Allows 4 thread 
sem_wait(&semaphore);  // Will do --semaphore
sem_post(&semaphore);  // Will do ++ semaphore
sem_destroy(&semaphore); // Destroy Semaphore

How semaphores are implemented at the kernel level:  

When a process creates a semaphore using the semget() system call, the kernel allocates a semaphore data structure and returns a unique identifier that can be used to access the semaphore. The process can then use functions such as semop() to perform semaphore operations such as wait and signal, which can be used to synchronize access to shared resources.

Semaphores can be used in a variety of scenarios, such as controlling access to shared memory segments, coordinating access to critical sections of code, or managing the flow of data between processes.

Security: 

From a security perspective, semaphores can be vulnerable to attacks such as denial-of-service attacks, where a malicious process repeatedly performs semaphore operations to prevent other processes from accessing shared resources. To mitigate this risk, it is important to use appropriate authentication and access control mechanisms to limit access to semaphores to authorized processes and users.

In addition, it is important to ensure that semaphores are properly initialized and that semaphore operations are performed correctly, to avoid issues such as deadlocks or race conditions that can lead to incorrect behavior or security vulnerabilities. Overall, semaphores can be a powerful tool for coordinating access to shared resources in Linux, but they require careful attention to security and correct usage to ensure that they are used effectively and safely. 

6. File locking 
---------

File locking is a synchronization mechanism used to prevent multiple processes from modifying the same file simultaneously. It is implemented at the kernel level in Linux and allows processes to acquire and release locks on files.

There are two types of locks that can be applied to a file: shared locks and exclusive locks. A shared lock allows multiple processes to read from a file simultaneously, but prevents any process from writing to the file until the lock is released. An exclusive lock, on the other hand, allows only one process to modify a file at any given time, preventing other processes from reading from or writing to the file until the lock is released.

In Linux, file locking is implemented using the fcntl() system call. When a process wants to acquire a lock on a file, it calls fcntl() with the F_SETLK or F_SETLKW command and a structure specifying the type of lock and the region of the file to lock. If the lock cannot be acquired immediately (in the case of F_SETLK), the process can either block until the lock becomes available or return an error.

File locking is often used in scenarios where multiple processes need to access the same file, such as a database or log file. It can also be used to prevent race conditions when multiple processes are modifying the same data in memory and periodically flushing the data to disk.

How File locking are implemented at the kernel level: 

File locking in Linux is implemented at the kernel level using a combination of file descriptors, inode structures, and the fcntl() system call.

When a process wants to acquire a lock on a file, it first creates a file descriptor using the open() system call. This file descriptor represents the file in the process's file descriptor table and is used to perform I/O operations on the file.

Next, the process calls the fcntl() system call with the file descriptor and a command indicating that it wants to acquire a lock on the file. The fcntl() system call takes a structure as an argument that specifies the type of lock and the region of the file to lock.

When a lock is acquired on a file, the kernel associates the lock with the inode structure for the file. The inode structure contains information about the file, including the file's type, owner, permissions, and location on disk.

To prevent multiple processes from acquiring conflicting locks on the same file, the kernel maintains a list of processes that currently hold locks on the file. If a process attempts to acquire a lock on a file that is already locked, the kernel checks the list of processes holding locks on the file and either blocks the requesting process until the lock is released or returns an error.

When a process releases a lock on a file, it calls the fcntl() system call with the F_UNLCK command to indicate that it wants to release the lock. The kernel then removes the lock from the list of locks associated with the inode structure for the file. 

Security:

In terms of security, file locking can be used to prevent unauthorized access to files. However, it is important to note that file locking does not provide any protection against attacks by malicious processes that have already gained access to the system. Additionally, file locking can be vulnerable to denial-of-service attacks if a malicious process repeatedly tries to acquire locks on files it does not intend to use. 



7. Signals 
Signals are a mechanism used in Linux to notify a process of an event or condition that has occurred. Signals can be generated by the kernel or by other processes, and are delivered to a specific process or group of processes.

When a process receives a signal from another process, it can use a signal handler to perform some action in response to the signal. The signal handler can be used to initiate some form of communication or synchronization between the two processes, such as by updating a shared memory region or sending a message through a pipe.

However, it's important to note that signals have limitations as an IPC mechanism. For example, signals are limited in the amount of data that can be transmitted between processes and the types of data that can be transmitted. Additionally, signals are not reliable or guaranteed to be delivered, and can be lost or delivered out of order.

Signals are often used in scenarios where a process needs to respond to external events, such as the arrival of data on a socket or the expiration of a timer. Signals can also be used for inter-process communication, allowing one process to notify another process of an event or condition. 

How Signals are implemented at the kernel level :

Signals are implemented at the kernel level using a combination of data structures and system calls. When a signal is generated, the kernel stores information about the signal in a signal queue associated with the target process. The kernel then sends a signal interrupt to the target process, causing the process to interrupt its current operation and execute a signal handler.

Signal handlers are functions that are registered by a process to handle specific signals. When a signal is delivered to a process, the kernel calls the appropriate signal handler for the signal. The signal handler can then perform any necessary actions, such as logging information or terminating the process.

Security:

In terms of security, signals can be used to implement basic access control mechanisms, such as restricting access to certain resources based on the identity of the process sending the signal. However, it is important to note that signals can also be used maliciously, such as by sending signals to cause a denial-of-service attack or to manipulate the behavior of a process in unintended ways. 

When to Use What?
The choice of IPC mechanism depends on the specific requirements of the application. Here are some general guidelines:

Use pipes when you need to transfer a small amount of data between related processes.
Use message queues when you need to transfer a larger amount of data between processes, or when you need to transfer data asynchronously.
Use shared memory when you need to transfer a large amount of data quickly, or when you need to synchronize data between processes.
Use sockets when you need to transfer data between processes on different machines, or when you need to transfer data over a network.
Use file locking where multiple processes need to access the same file, such as a database or log file. It can also be used to prevent race conditions when multiple processes are modifying the same data in memory and periodically flushing the data to disk. 
Use Signals Signals where a process needs to respond to external events, such as the arrival of data on a socket or the expiration of a timer. Signals can also be used for inter-process communication, allowing one process to notify another process of an event or condition 

In addition to these mechanisms, there are also other IPC mechanisms available in Linux, such as semaphores and signals. The choice of IPC mechanism ultimately depends on the specific requirements of the application.

Thanks for reading till end, please comment if you know any other IPC mechanism. 


Pthreads 

pthread_create()
pthread_mutex_init()
pthread_mutex_lock()
pthread_mutex_unlock()
pthread_mutex_destroy()
pthread_rwlock_t // multiple read and single write



Mutex 
-----
Binary implemetation ( "test and set" OR "compare and swap")
Synchronization mechanism, with blocking for critical section 
If Mutex is not available, threads goes to sleep means context switching. 

Spinlock
----
If the resource is not available, CPU spins in tight loop. Used across the CPUs.
