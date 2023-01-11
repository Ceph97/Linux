## Linux

##### Table of Contents  

- [DEADLOCK](#deadlock)
    - [TYPES OF RESOURCES](#types-of-resources)
    - [CONDITION FOR RESOURCE DEADLOCK](#condition-for-resource-deadlock)
    - [DEADLOCK HANDLING](#deadlock-handling)
    - [DEADLOCK PREVENTION](#deadlock-prevention)
    - [TWO-PHASE LOCKING](#two-phase-locking)
    - [COMMUNICATION DEADLOCK](#communication-deadlock)
    - [LIVELOCK](#livelock)

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


