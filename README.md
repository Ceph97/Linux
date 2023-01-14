# Linux Operating System

##### Table of Contents  

- [DEADLOCK](#deadlock)
    - [TYPES OF RESOURCES](#types-of-resources)
    - [CONDITION FOR RESOURCE DEADLOCK](#condition-for-resource-deadlock)
    - [DEADLOCK HANDLING](#deadlock-handling)
    - [DEADLOCK PREVENTION](#deadlock-prevention)
    - [TWO-PHASE LOCKING](#two-phase-locking)
    - [COMMUNICATION DEADLOCK](#communication-deadlock)
    - [LIVELOCK](#livelock)

- [PROCESSES AND THREADS](#processes-threads)
    - [DAEMON PROCESS](#daemon-process)
       - [CHILD PROCESS](#child-process)
    - [PROCESS TERMINATION](#process-termination)
    - [PROCESS STATES](#process-states)
    - [IMPLEMENTATION OF PROCESSES](#implementation-of-processes)
    - [INTERRUPT VECTORS](#interrupt-vectors)
       - [INTERRUPTS](#interrupts)

- [THREADS](#threads)
    - [FINITE STATE MACHINE](#finite-state-machine)
    - [MULTI-THREADING](#multi-threading)
    - [CONS OF MULTI-THREADS](#cons-of-multi-threading)
    - [THREAD STATES](#thread-states)
    - [POSIX THREADS](#posix-threads)
    - [IMPLEMENTATION OF THREADS](#implementation-of-threads)
        - [USER LEVEL THREADS](#user-level-threads)
        - [KERNEL LEVEL THREADS](#kernel-level-threads)
        - [HYBRID THREADS](#hybrid-threads)
        - [POP-UP THREADS](#pop-up-threads)
        - [UPCALLS AND DOWNCALLS](#upcalls-downcalls)
        - [STACK MANAGEMENT](#stack-management)

- [INTERPROCESS COMMUNICATION](#interprocess-communication)


    


# DEADLOCK <a name="deadlock"></a>
- A scenario where 2 processes are asking for resources that the other process has, and neither process is willing to give up the resource it has. This results in both processes waiting forever.

    - It can occur with 2 or more processes.
    - It can occur with Database locking.
    - It can occur with file locking.
    - It is not limited to physical resources. It can occur with virtual software resources as well.

- <h3>TYPES OF RESOURCES</h3> <a name="types-of-resources"></a>

    - Preemptive Resources
        - Resources that can be taken away from a process by the operating system to get out of a deadlock.
        - Examples: CPU, Memory, I/O devices.

    - Non-Preemptive Resources
        - Resources that cannot be taken away from a process by the operating system until a process realeases it.
        - Examples: Files, Database records, printer, etc.
        - Make sure process has all non-preemptive resources before it starts executing to avoid deadlock.

- <h3>CONDITION FOR RESOURCE DEADLOCK</h3> <a name="condition-for-resource-deadlock"></a>
    
    - Mutual Exclusion
        - Only one process can use a resource at a time.
        - Example: Only one process can use a printer at a time.

    - Hold and Wait
        - A process is holding at least one resource and waiting for additional resources held by other processes.

    - No Preemption
        - A resource cannot be taken away from a process until it releases the resource.

    - Circular Wait
        - There is a circular chain of waiting for resources.
        - Example: Process P1 is waiting for a resource R1 that is held by process P2. Process P2 is waiting for a resource R2 that is held by process P3. Process P3 is waiting for a resource R3 that is held by process P1.

All these 4 must be true for a resource deadlock to occur.

- <h3>DEADLOCK HANDLING</h3> <a name="deadlock-handling"></a>

    - Deadlock Prevention
        - Avoiding the conditions that lead to deadlock.
        - Example: Avoiding circular wait.
        - System should not allow a process to request a resource that is already held by another process.
        - This is possible only if the system knows the resource allocation graph in advance.
        - Banker's Algorithm is an example of deadlock prevention.
            - Banker's Algorithm is used to determine if a request for resources will lead to a safe state or not.
            - Same way a bank determines if a loan can be given to a customer or not based on the customer's credit history and cash flow.

    - Deadlock Avoidance
        - Detecting the conditions that lead to deadlock and preventing the system from entering a deadlock state.
        - Example: Banker's Algorithm.

    - Deadlock Detection and Recovery
        - Detecting the deadlock and recovering from it.
        - System does not prevent deadlock, but it can detect it and recover from it.
        - Example: Killing the process that is causing the deadlock.
        - Recovery through pre-emption for example:
            - If a process is holding a resource that is not needed, the operating system can take the resource away from the process.
            - If a process is waiting for a resource that is not needed, the operating system can give the resource to the process.
            - Since it is dependent on another process, it is not easy to implement.

    - Deadlock Ignorance - Ostrich Algorithm
        - Ignoring the deadlock and hoping that it will not occur.
        - Only works if the probability of deadlock is very low. for example if Deadlock occurs once in 100 years, not ideal to pour resources into handling it.
        - Example: Linux does not handle deadlock.

-  <h3>DEADLOCK PREVENTION</h3> <a name="deadlock-prevention"></a>

    - Mutual Exclusion
        - Avoiding the condition where only one process can use a resource at a time.
        - Example: Having a daemon process that manages the printer and allows multiple processes to print at the same time. by using a queue for the print jobs.

    - Attacking the Hold and Wait condition
        - All processes must request all resources at once before they start executing.
        - Example: A process that needs to read a file and write to a file must request both the resources at once before it starts executing.
        - This is not practical because it is not known in advance which resources a process will need.

    - Attacking the No Preemption condition
        - Spooling printer output to a file and then printing the file and allowing only printer daemon to access the printer.

    - Attacking the Circular Wait condition
        - If one process is requesting more than 2 resources, it must request the resources in a fixed order.
        - Numbering the resources and requesting them in increasing order.

- <h3>TWO-PHASE LOCKING</h3> <a name="two-phase-locking"></a>

    - A protocol for avoiding deadlock.
    - A process must acquire all the locks it needs in a fixed order.
    - Example: If a process needs to access a file and a database record, it must acquire the file lock first and then the database record lock.
    - If a process needs to access a database record and a file, it must acquire the database record lock first and then the file lock.
    - This is not practical because it is not known in advance which resources a process will need.

- <h3>COMMUNICATION DEADLOCK</h3> <a name="communication-deadlock"></a>

    - A scenario where 2 processes are waiting for each other to send a message via a communication channel.
    - Example: Process P1 is waiting for a message from process P2. Process P2 is waiting for a message from process P1.
    - This can be avoided by using timeouts.

- <h3>LIVELOCK</h3> <a name="livelock"></a>

    - A scenario where 2 processes are giving up resources to each other.
    - Example: Process P1 is giving up a resource R1 to process P2. Process P2 is giving up a resource R2 to process P1.
    - Like 2 people politely giving each other way on a road. will lead to no progress.
    - This can be avoided by using timeouts.

- <h3>STARVATION</h3> <a name="starvation"></a>

    - Printer printing jobs in ascending order of file size, if a large file is submitted, it will be printed last.
    - This can read to starvation of larger files if small files are submitted frequently.
    - This can be avoided by using FIFO queue.



# PROCESSES AND THREADS <a name="processes-and-threads"></a>

- A process is an instance of a program in execution. It is a program in execution.
- A program is a set of instructions/algorithms that are stored on disk.
- A thread is a unit of execution within a process. It is a light weight process.
- CPU works on one process at a time and switches between processes within a second. Ths is called ```context switching``` and it gives an illusion of parallerism.
- ```Multi-processor```: is a computer with more than one CPU.
- ```Multi programming```: is the ability of a CPU to execute more than one process at a time.
- ```Multi-core```: is a CPU with more than one core, common with new systems.
- The rate at which a process or thread is executed is not reproducible if a rerun is done.

#### DAEMON PROCESS <a name="daemon-process"></a>

- A process that runs in the background and provides services to other processes. for example: a process that manages the printer, a process that manages the disk, a process that manages the network.
- A daemon process is started by the operating system when the system boots up.

#### FORK <a name="fork"></a>

- A process can create a new process by calling the ```fork()``` system call.
- The ```fork()``` system call creates a new process by duplicating the calling process.
- The new process is called the child process.
- The calling process is called the parent process.


    CHILD PROCESS <a name="child-process"></a>

    - The child process is an exact copy of the parent process.
    - The child process has its own writtable memory space, file descriptors, and other resources.
    - fork() creates a child process that differs from the parent process only in its PID and PPID, and in the fact that resource utilizations are set to 0. File locks and pending signals are not inherited.
    - The child process has its own process ID. but it has the same parent process ID as the parent process.
    - The child process has its own stack, but it has the same code and data segments as the parent process.
    - Under Linux, fork() is implemented using copy-on-write pages, so the only penalty that it incurs is the time and memory required to duplicate the parent’s page tables, and to create a unique task structure for the child.

    - The ```fork()``` system call returns 2 values:
        - 0 to the child process.
        - The process ID of the child process to the parent process.
        - On failure, a -1 will be returned in the parent’s context, no child process will be created, and errno will be set appropriately.
    
#### PROCESS TERMINATION <a name="process-termination"></a>

- Normal termination
    - The process calls the ```exit()``` system call.
    - The process calls the ```_exit()``` system call.
    - The process returns from the ```main()``` function.
- Error exit(voluntary)
    - Bad input from user.
    - The process calls the ```exit()``` system call.
    - The process returns from the ```main()``` function.
- Fatal Error
    - The process calls the ```abort()``` system call.
    - The process calls the ```_exit()``` system call.
    - The process returns from the ```main()``` function.

- Killed by another process
    - The process calls the ```kill()``` system call.
    - The process calls the ```abort()``` system call.
    - The process calls the ```_exit()``` system call.
    - The process returns from the ```main()``` function.

- If a parent is killed, all forked children are not killed. They become orphan processes.
- The orphan processes are adopted by the init process.
- The init process is the first process that is started by the kernel when the system boots up.
- The init process is the parent of all orphan processes.
- The init process checks periodically for new children, and waits for them (thus freeing resources that are allocated by their return value).

#### PROCESS HIERARCHY <a name="process-hierarchy"></a>

- The process hierarchy is a tree structure.
- The root of the tree is the init process.
- The init process is the parent of all processes.

#### PROCESS STATES <a name="process-states"></a>

- A process can be in one of the following states:
    - Running:
        - The process is currently execution.
    - Ready:
        - The process is waiting to be executed.
        - Example: The process is waiting for the CPU to be free.
    - Blocked:
        - The process is waiting for an event to occur.
        - Example: The process is waiting for a disk read to complete.
        - This can not run even if the CPU is free/iddle.
        - When a process is reading a pipe, and the pipe is empty, the process is blocked.

#### IMPLEMENTATION OF PROCESSES <a name="implementation-of-processes"></a>

- [Difference between PCB and Process Table](https://gateoverflow.in/90236/difference-between-process-table-and-pcb)
- OS maintains a table/Data structure called ```PROCESS TABLE``` which is an array, and each entry in a proces table can be considered as ```PCB's```.
- The process is implemented using a data structure called the ```PCB``` which is maintained by the process itself.
- PCB stands for ```Process Control Block```
- A process control block (pcb) contains information about the process, I.e. registers, quantum, priority, etc. The process table is an array of pcb's.
- The PCB contains the following information:
    - Process ID;
        - The process ID is a unique number that is assigned to a process by the operating system.
    - Process state;
        - The process state is the state of the process, I.e. running, ready, blocked, etc.
    - Program counter;
        - The program counter is the address of the next instruction to be executed.
    - CPU registers;
        - The CPU registers are the CPU registers that are used by the process.
    - CPU scheduling information;
        - The CPU scheduling information is the information that is used by the CPU scheduler to schedule the process.
    - Memory management information;
        - The memory management information is the information that is used by the memory manager to manage the memory that is used by the process.
    - Accounting information;
        - The accounting information is the information that is used by the accounting system to account for the resources that are used by the process.
    - I/O status information;
        - The I/O status information is the information that is used by the I/O system to manage the I/O devices that are used by the process.

#### INTERRUPT VECTOR <a name="interrupt-vector"></a>

- An interrupt vector is a table that contains the addresses of the interrupt service routines.

INTERRUPTS <a name="interrupts"></a>

- An interrupt is an event that causes the processor to stop executing the current program and to execute an interrupt service routine.
- The interrupt service routine is a program that is executed when an interrupt occurs.
- The interrupt service routine is executed in the kernel mode.

# THREADS <a name="threads"></a>

- A thread is a small set of instructions designed to be scheduled and executed by the CPU independently of the parent process. 
- A thread is a lightweight process. Hence easier to create and destroy.
- For example, a program may have an open thread waiting for a specific event to occur or running a separate job, allowing the main program to perform other tasks.
- If a program was single-threaded, it would have to wait for the event to occur before it could continue. blocking system calls like ```read()``` and ```write()``` would not be possible to run in parallel.

Process vs Thread
    - A process is a program in execution.
    - A thread is a subset of a process. somewhat like a light weight process.

#### FINITE STATE MACHINE <a name="finite-state-machine"></a>

- A finite state machine is a model of computation that can be in exactly one of a finite number of states at any given time.
- The state machine can change from one state to another in response to some external inputs.
- Example: A vending machine can be in one of the following states:
    - Idle
    - Waiting for money
    - Waiting for selection
    - Waiting for change
    - Dispensing product
    - Dispensing change

<h3>MULTI-THREADING</h3> <a name="multi-threading"></a>

- Multi-threading is the ability of a process to execute multiple threads simultaneously.
- Threads take advantage of the fact that the CPU can switch between threads very quickly, giving the illusion that the threads are executing simultaneously.
- Threads are used to implement multitasking in a program.

<h4> CONS OF MULTI-THREADING </h4> <a name="cons-of-multi-threading"></a>

- No protection between threads as they share the same address space. Hence one, thread can modify, erase, write another thread's stack.

- Issues can arise if one thread is closing a file, and another thread is reading from the same file.

#### THREAD STATES <a name="thread-states"></a>

- Like a process a thread can also be in one of the following states:
    - Running
    - Ready
    - Blocked
    - Terminated

- Each thread has its own stack, but all threads share the same code and data segments.

- Process usually starts with one thread, this thread has the ability to create/launch more threads per need using the following system calls:
   
    - ```thread_create()```: Creates a new thread.
    - ```thread_exit()```: Terminates the current thread.
    - ```thread_join()```: Waits for a thread to terminate.
    - ```thread_yield()```: Yields the CPU to another thread.

#### POSIX THREADS <a name="posix-threads"></a>
- POSIX threads are a standard for multi-threading in the UNIX operating system.
- POSIX threads are implemented using the following system calls:
    - ```pthread_create()```: Creates a new thread.
    - ```pthread_exit()```: Terminates the current thread.
    - ```pthread_join()```: Waits for a thread to terminate.
    - ```pthread_yield()```: Yields the CPU to another thread.

#### IMPLEMENTATION OF THREADS <a name="implementation-of-threads"></a>

- <h4>USER SPACE THREADS</h4> <a name="user-space-threads"></a>
    - The threads are implemented in the user space and the kernel is not aware of the threads.
    - Each process has its own thread table, which contains the information about the threads that are created by the process.
    - The thread table is an array of thread control blocks (TCB).
    - The TCB contains the following information:
        - Thread ID;
            - The thread ID is a unique number that is assigned to a thread by the operating system.
        - Thread state;
            - The thread state is the state of the thread, I.e. running, ready, blocked, etc.
        - Thread priority;
            - The thread priority is the priority of the thread over other threads.
        - CPU registers;
            - The CPU registers are the CPU registers that are used by the thread.
        - CPU scheduling information;
        - Pointer to process that created the thread;
        - Pointer to other threads that were created by this thread;
    - The context switch is done by the runtime procedure.
    - It allows custom scheduling algorithms to be implemented.
    - Process can have own garbage collector.

    - Disadvantages;
        - Blocking system calls like ```read()``` and ```write()``` cannot be executed in parallel as the kernel is not aware of the threads, it only sees the process.
        - lack of cordination between kernel and user space.
        - OS can make poor decisions like:
            - scheduling a process with idle threads
            - blocking a process due to a blocking thread even though the process has other threads that can run
            - Giving a process as a whole one time slice irrespective of whether the process has 1 or 1000 threads
            - unschedule a process with a thread holding a lock.



    

- <h4>KERNEL SPACE THREADS</h4> <a name="kernel-space-threads"></a>

    - The threads are implemented in the kernel space and the kernel is aware of the threads.
    - The kernel maintains a thread table that contains the information about all the threads that are created by all the processes in the system.
    - Thread re-cycle is possible.
        - When a thread terminates, the kernel can re-use the thread ID of the terminated thread for a new thread.
        - It is also possible to re-cycle user space threads, however there is no point in doing so as processes only manage a small number of threads.
    
    - Disadvantages;
        - Signals

- <h4>HYBRID THREADS </h4> <a name="hybrid-threads"></a>

    - Hybrid threads are a combination of user space threads and kernel space threads.
    - The kernel is aware of the threads that are created in kernel space only.
    - This offers the advantages of both user space threads and kernel space threads.
    - Critical section of the code is executed in kernel space, while the rest of the code is executed in user space.

- <h4>POP UP THREADS </h4> <a name="pop-up-threads"></a>

    - In a distributed system, a new thread can be created on to handle incoming messages.
    - The thread is created when a message is received and is destroyed when the message is processed.
    - Since they are brand new they have no history,registers,stacks,etc.
    - In simpler terms, Pop-up threads is just an academic terms for multithreaded server in which every incoming requests will be handled by a separate thread.
    - This separate thread can be one that newly created or can be the one that reused from existing threads. The mechanism is handled by a Thread Pool.

**UPCALLS AND DOWNCALLS** <a name="upcalls-and-downcalls"></a>

- ***Upcalls:*** 
    - Are the calls made from the kernel to the user space.
    - An upcall is a mechanism that allows the kernel to execute a function
      in userspace, and potentially be returned information as a result.
    - An upcall is like a signal, except that the kernel may use it at any
      time, for any purpose, including in an interrupt handler.
    - [Upcalls in Linux](https://lkml.iu.edu/hypermail/linux/kernel/9809.3/0922.html)

- ***Downcalls:***
    - Are the calls made from the user space to the kernel.
    - A downcall is a mechanism that allows userspace to execute a function
      in the kernel, and potentially be returned information as a result.<br>
      

| **Upcall** | **Downcall** |
|------------|--------------|
| Kernel to user space | User space to kernel |
| Kernel can call at any time | User space can call at any time |
| Unprivileged code enters user mode | privileged code enters kernel mode |
| Implemented using IPC | Implemented via TRAP |
| Implemented using signals | Implemented using system calls |

**STACK MANAGEMENT** <a name="stack-management"></a>

- The stack is a region of memory that is used to store the local variables of a function.
- The stack is a LIFO (Last In First Out) data structure.
- If process runs out of stack space, the system will assign more stack space to the process.
- If a process is multi-threaded, each thread has its own stack.
- The flip side if multi-threaded process runs out of stack space, the system will assign more stack space to the process, but only to the thread that is running out of stack space.

<h4> INTERPROCESS COMMUNICATION </h4> <a name="interprocess-communication"></a>

- Interprocess communication (IPC) is the mechanism that allows processes to communicate with each other.

**ISSUES WITH IPC** <a name="issues-with-ipc"></a>
- Race conditions:
    - If two processes are accessing the same resource, then the result of the operation depends on the order in which the processes access the resource.

**CRITICAL SECTION/REGION** <a name="critical-section"></a>

- A critical section/region is a section of code where processes will ultimately access a shared resource.
- The critical section/region must be protected from other processes. To have a good solution, the following conditions must be met:
    - Mutual exclusion: Only one process can be in the critical section at a time.
    - Progress: If no process is in the critical section, then a process that wants to enter the critical section will eventually be able to do so.
    - Bounded waiting: If a process wants to enter the critical section, then it will eventually enter the critical section.

**PROCESS DESCRIPTORS IN LINUX** <a name="process-descriptors"></a>

- In Linux, a process descriptor is an instance of type struct ```task_struct``` defined in ```<linux/sched.h>```. 

- It is one of the central data structures, and contains all the attributes, identification details, and resource allocation entries that a process holds. Looking at struct task_struct is like a peek into the window of what the kernel sees or works with to manage and schedule a process.

- Refer to the [Process descriptors](https://subscription.packtpub.com/book/application-development/9781785883057/1/ch01lvl1sec9/process-descriptors) for more information.

***CONTENTS OF PROCESS DESCRIPTOR*** <a name="contents-of-process-descriptor"></a>

1. **PROCESS ATTRIBUTES**

    - **PID**:

        - The process ID of the process.
        - This field contains a unique process identifier referred to as PID. PIDs in Linux are of the type ```pid_t ```(integer). 
        - Though a PID is an integer, the default ***maximum number PIDs*** is ```32,768``` specified through the ```/proc/sys/kernel/pid_max interface```.
        - The value in this file can be set to any value up to 2^22 (PID_MAX_LIMIT, approximately 4 million).
        - To manage PIDs, the kernel uses a bitmap. This bitmap allows the kernel to keep track of PIDs in use and assign a unique PID for new processes.
        - Bits with value 1 in the bitmap indicate that the corresponding PIDs are in use, and those with value 0 indicate free PIDs.
        - Whenever the kernel needs to assign a unique PID, it looks for the first unset bit and sets it to 1, and conversely to free a PID, it toggles the corresponding bit from 1 to 0.

    - **tgid**:

        - The thread group ID of the process.
        - This field contains the thread group ID (tgid) of the process. 
        -  let's say when a new process is created, its ```PID``` and ```TGID``` are the same, as the process happens to be the only thread
        - When the process spawns a new thread, the new child gets a unique PID but inherits the TGID from the parent, as it belongs to the same thread group.
        - The tgid is also used to identify the process that created the thread group.
        - The TGID is primarily used to support multi-threaded process. We will delve into further details in the threads section of this chapter.
    
    - **state**:
        - A process right from the time it is spawned until it exits may exist in various states, referred to as process states--they define the process’s current state:

2. **PROCESS RELATIONS**

    Every process can be related to a parent process, establishing a parent-child relationship. Similarly, multiple processes spawned by the same process are called siblings. These fields establish how the current process relates to another process.

    - **real_parent and parent**
        - These are pointers to the parent's task structure. For a normal process, both these pointers refer to the same ```task_struct```; they only differ for multi-thread processes, implemented using posix threads. 
        - For such cases, **real_parent** refers to the parent thread task structure and parent refers the process task structure to which SIGCHLD is delivered.
    - **children**
        - This is a pointer to a list of child task structures.
    - **sibling**
        - This is a pointer to the sibling task structure.
    - **group_leader**
        - This is a pointer to the task structure of the process that created the thread group.

3. **SCEDULING ATTRIBUTES**
    All contending processes must be given fair CPU time, and this calls for scheduling based on time slices and process priorities. 
    
    These attributes contain necessary information that the scheduler uses when deciding on which process gets priority when contending.

4. **FILE DESCRIPTOR TABLE**

    During the lifetime of a process, it may access various resource files to get its task done. 
    
    This results in the process opening, closing, reading, and writing to these files. The system must keep track of these activities; 
    
    file descriptor elements help the system know which files the process holds.
    
    - **files**
        - The file descriptor table contains pointers to all the files that a process opens to perform various operations. The files field contains a pointer, which points to this file descriptor table.

    - **fs**
        - Filesystem information is stored in this field.

5. **SIGNAL DESCRIPTOR**

    For processes to handle signals, the task structure has various elements that determine how the signals must be handled.

    - **signal**
       - This is of type struct ```signal_struct```, which contains information on all the signals associated with the process.
    - **sighand**
         - This is of type struct ```sighand_struct```, which contains information on how the signals must be handled by the process.
    - **sigset_t blocked, real_blocked**
          - These elements identify signals that are currently masked or blocked by the process.
    - **pending**
          - This is of type struct ```sigpending```, which identifies signals which are generated but not yet delivered.
    

































    




    








