
## Thread Implementation Models

### 1. Many-to-One Model (User-Level Threads)

In this model, multiple user-level threads are mapped to a single kernel thread. The thread library is implemented entirely in user space.

#### Characteristics:

- Kernel is unaware of user-level threads
- Only one kernel thread per process
- Thread management done by user-space library

#### Advantages:

- **Fast thread switching**: No kernel involvement required
    - Thread switching occurs within the same address space
    - No mode changes or kernel code execution needed
- **Fast thread creation**:
    - Simply call a library function
    - No system calls required
    - Allocate TCB (Thread Control Block) in user space

#### Disadvantages:

- **Blocking system calls**: When one thread blocks, entire process blocks
- **No true parallelism**: Cannot utilize multiple processors
- **No preemption**: Threads must voluntarily yield CPU (cooperative scheduling)

### 2. One-to-One Model (Kernel-Level Threads)

Each user thread is mapped directly to a kernel thread. The kernel is fully aware of all threads.

#### Characteristics:

- Kernel manages all threads
- Each thread has its own kernel thread
- Modern systems (Windows NT/XP/2000, Linux) use this model

#### Advantages:

- **Better concurrency**: When one thread blocks, others can continue
- **Multiprocessor utilization**: Different threads can run on different CPUs
- **Preemptive scheduling**: Kernel can interrupt long-running threads
- **No blocking issues**: Blocking system calls only affect the calling thread

#### Disadvantages:

- **Higher overhead**: Thread creation requires system calls
- **Slower thread operations**: All thread operations involve kernel

### 3. Many-to-Many Model

Multiple user threads are mapped to multiple (but fewer) kernel threads.

#### Purpose:

- Limit kernel resource consumption
- Balance between user-level and kernel-level advantages
- Example: 100 user threads mapped to 5 kernel threads

### 4. Two-Level Model

Combination of many-to-many and one-to-one models:

- Some user threads mapped to multiple kernel threads
- Some user threads have dedicated kernel threads

## Thread APIs and Programming

### POSIX Threads (pthreads) API

Standard API for thread programming on Unix-like systems (Linux, Solaris).

#### Key Functions:

- `pthread_create()`: Create a new thread
- `pthread_join()`: Wait for thread termination
- `pthread_exit()`: Terminate calling thread
- `pthread_yield()`: Voluntarily give up CPU

#### Basic Program Structure:

```c
#include <pthread.h>
#include <stdio.h>

// Global variable shared between threads
int sum;

// Thread function prototype
void *runner(void *param);

int main(int argc, char *argv[]) {
    pthread_t tid;                    // Thread ID
    pthread_attr_t attr;              // Thread attributes
    
    // Initialize thread attributes
    pthread_attr_init(&attr);
    
    // Create thread
    pthread_create(&tid, &attr, runner, argv[1]);
    
    // Wait for thread completion
    pthread_join(tid, NULL);
    
    // Use computed result
    printf("Sum = %d\n", sum);
    
    return 0;
}

// Thread function implementation
void *runner(void *param) {
    int upper = atoi(param);
    int i;
    
    sum = 0;
    for (i = 1; i <= upper; i++) {
        sum += i;
    }
    
    pthread_exit(0);
}
```

#### Program Flow:

1. Main thread starts execution
2. `pthread_create()` creates new thread running `runner()` function
3. Both threads run pseudo-parallel
4. Main thread calls `pthread_join()` and blocks
5. Worker thread computes sum and terminates
6. Main thread resumes and prints result

#### Compilation:

```bash
gcc -o program program.c -lpthread
```

## Thread-Related Issues

### 1. Fork and Exec Semantics

**Problem**: When a multithreaded process calls `fork()`, should the child process:

- Have only one thread (the calling thread)?
- Have all threads from the parent?

**Solutions**:

- **If `exec()` follows `fork()`**: Create single-threaded child (threads will be replaced anyway)
- **If no `exec()` after `fork()`**: Duplicate all parent threads in child

### 2. Thread Cancellation

**Problem**: How to safely terminate a thread before it completes?

**Approaches**:

- **Asynchronous cancellation**: Terminate immediately (dangerous - may leave data inconsistent)
- **Deferred cancellation**: Thread checks for cancellation requests at safe points

### 3. Signal Handling

**Problem**: In multithreaded processes, which thread should handle signals?

**Options**:

1. Thread to which signal applies
2. All threads in the process
3. Specific subset of threads
4. Dedicated signal-handling thread

#### Signal Basics:

- **Signal**: Notification sent to a process
- **Examples**:
    - SIGINT (Ctrl+C): Interrupt signal
    - SIGILL: Illegal instruction
    - SIGKILL: Terminate process

#### Signal Handling Example:

```c
#include <signal.h>
#include <stdio.h>

// Signal handler function
void handler(int sig) {
    printf("Received SIGINT signal\n");
    fflush(stdout);
    // Could exit here or continue
}

int main() {
    // Register signal handler
    signal(SIGINT, handler);
    
    // Infinite loop
    while(1) {
        // Program continues running
    }
    
    return 0;
}
```

#### Signal Process:

1. Event occurs (e.g., Ctrl+C pressed)
2. Kernel detects event and generates signal
3. Signal delivered to target process
4. Process handles signal:
    - Default action (usually terminate)
    - Custom handler function (if registered)

#### Using kill Command:

```bash
# Send SIGTERM to process with PID 3405
kill -TERM 3405

# Send SIGKILL (cannot be caught)
kill -9 3405
```

## Key Concepts Summary

### Thread Execution Model:

- **Pseudo-parallel execution**: On single CPU, threads take turns
- **True parallel execution**: On multicore systems, threads can run simultaneously
- **Thread scheduling**: Kernel decides which thread runs when

### Synchronization Needs:

- **Shared data**: Global variables accessible by all threads
- **Race conditions**: Multiple threads accessing shared data simultaneously
- **Synchronization primitives**: Needed to coordinate thread access

### Thread vs Process:

- **Threads**: Share memory space, faster creation/switching
- **Processes**: Separate memory spaces, more isolation, higher overhead

## Important Notes:

1. **Always synchronize**: Use `pthread_join()` to wait for thread completion before using shared data
2. **Handle signals carefully**: In multithreaded programs, signal delivery can be complex
3. **Resource management**: Be careful with thread cancellation to avoid resource leaks
4. **Compiler flags**: Remember to link with pthread library (`-lpthread`)

## Next Topics:

- Thread synchronization mechanisms
- Mutex and semaphores
- Deadlock prevention and detection