## 1. Multi-Level Queue Scheduling

### Overview

- **Traditional approach**: Single ready queue with one scheduling algorithm
- **Multi-level approach**: Multiple queues, each with different scheduling algorithms
- Ready queue is partitioned into separate queues based on process types

### Queue Organization

- **Foreground processes** (interactive): One queue
- **Background processes** (batch): Another queue
- Each queue can use different scheduling algorithms:
    - Foreground: Round Robin (for good response time)
    - Background: FCFS (response time less critical)

### Inter-Queue Scheduling

Two approaches for scheduling between queues:

#### 1. Fixed Priority Scheduling

- Higher priority queue served first
- Lower priority queue served only when higher priority queue is empty
- **Problem**: Starvation possible if higher priority queue always has processes

#### 2. Time Slicing

- Each queue gets a time slice (like Round Robin among queues)
- Example: 80% time for foreground, 20% for background
- **Advantage**: Prevents starvation

### Characteristics

- Once assigned to a queue, process remains there
- Priority remains static
- Can have multiple queues (system, interactive, batch, student processes, etc.)

## 2. Multi-Level Feedback Queue Scheduling

### Key Innovation

- **Processes can move between queues**
- **Dynamic priorities** - priorities can change over time
- **Aging implementation** - processes can be promoted to higher priority queues

### Parameters to Define

1. **Number of queues**
2. **Scheduling algorithm for each queue**
3. **Rules for promoting processes** (increasing priority)
4. **Rules for demoting processes** (decreasing priority)
5. **Method to determine initial queue assignment**

### Example Implementation

**Three-queue system:**

- **Q0**: Round Robin, time quantum = 8ms (highest priority)
- **Q1**: Round Robin, time quantum = 16ms (medium priority)
- **Q2**: FCFS (lowest priority)

**Movement Rules:**

- New processes start in Q0
- If process uses full time quantum in Q0 → moved to Q1
- If process uses full time quantum in Q1 → moved to Q2
- If process doesn't use full quantum → stays in same queue

**Logic:**

- I/O bound processes (interactive) stay in Q0 (highest priority)
- CPU-bound processes gradually move to lower priority queues
- Provides good response time for interactive processes

## 3. Thread Scheduling

### Basic Principle

- **Same algorithms apply** to both processes and threads
- Threads are scheduled entities just like processes
- When thread consumes time quantum → preempted for another thread

### Contention Scope

#### Process Contention Scope (PCS)

- Thread scheduling within a single process
- User-level thread library handles scheduling
- Competition among threads of same process

#### System Contention Scope (SCS)

- Kernel schedules threads
- Competition among all threads in the system

### pthread API Functions

```c
pthread_attr_setscope() // Set scheduling scope
pthread_attr_setschedpolicy() // Set scheduling policy
```

**Scheduling Policies:**

- `SCHED_OTHER`: Normal/interactive processes
- `SCHED_FIFO`: FCFS for batch processes
- `SCHED_RR`: Real-time processes (higher priority)

## 4. Multiprocessor Scheduling

### Types of Multiprocessing

#### Asymmetric Multiprocessing

- **One master processor** handles all scheduling decisions
- Master runs kernel code, scheduling, system calls
- Other processors execute user processes
- Master assigns processes to other processors

#### Symmetric Multiprocessing (SMP)

- **All processors equal** - no master/slave relationship
- Each processor can run kernel and user code
- **Self-scheduling**: Each processor runs its own scheduler
- Common in desktop computers (dual-core, quad-core)

### Processor Affinity

#### Soft Affinity

- Kernel **tries** to keep process on same processor
- **Reason**: Avoid cache invalidation overhead
- May move process if load balancing needed
- **Default behavior**

#### Hard Affinity

- Process **guaranteed** to stay on assigned processor
- Must be explicitly specified at process creation
- Process never moved to different processor

### NUMA (Non-Uniform Memory Access)

- Multiple CPU boards, each with local memory
- Accessing local memory is faster than remote memory
- Kernel should allocate memory from local memory when possible
- **Memory allocation strategy**: Prefer local memory for better performance

### Hyperthreading/Hardware Threading

- **Multiple threads per core** (typically 2)
- **Concept**: While one thread waits for memory, another can compute
- Single ALU with multiple register sets
- Better utilization of CPU and memory resources
- OS sees single core as multiple CPUs

## 5. Linux Scheduler Example

### Key Features

- **O(1) scheduler**: Constant time complexity
- Scheduling decision time independent of ready queue size
- **Priority-based** with **round-robin** and **multi-level queues**
- **Dynamic priorities**: Priorities can change based on behavior

### Priority Ranges

- **Real-time processes**: 0-99 (0 = highest priority)
- **Normal processes**: 100-139
- **Lower numbers = higher priority**

### Time Quantum Assignment

- **Higher priority** → **Larger time quantum**
- Lowest priority: ~10ms
- Highest priority: ~200ms

### Active/Expired Arrays

- **Active array**: Currently schedulable processes
- **Expired array**: Processes that used up their time quantum
- **Strict priority**: Higher priority queues served first
- When process expires time quantum → moved to expired array
- When active array empty → swap with expired array

### Priority Adjustment

- **I/O bound processes**: Priority increased (more sleeping)
- **CPU bound processes**: Priority decreased (less sleeping)
- **Interactive processes**: Higher priority for better response time

## 6. Algorithm Evaluation Methods

### 1. Deterministic Modeling

- **Gantt charts** with known process CPU times
- Calculate metrics (waiting time, turnaround time) for specific scenario
- **Limitation**: May not reflect real-world variability

### 2. Simulation

- Implement algorithms in simulation program
- **Input**: Workload file with process behaviors
- Can use deterministic or random workloads
- **Advantage**: More flexible than deterministic modeling

### 3. Queuing Models

- **Mathematical models** using probability distributions
- Assume distributions for:
    - CPU burst lengths (e.g., exponential)
    - Process inter-arrival times
- **Queuing theory**: Formulas relate arrival rate, service rate to performance metrics

### 4. Implementation

- **Real system implementation** (e.g., modify Linux kernel)
- **Most accurate** but also most difficult
- Requires kernel modification and user evaluation

## Key Takeaways

1. **Multi-level queues** allow different scheduling for different process types
2. **Feedback queues** enable dynamic priority adjustment and aging
3. **Thread scheduling** uses same principles as process scheduling
4. **Multiprocessor scheduling** adds complexity with affinity and load balancing
5. **Real systems** combine multiple scheduling techniques
6. **Algorithm evaluation** requires careful methodology selection based on goals

---

_Next lecture: Chapter 6 - Process Synchronization_