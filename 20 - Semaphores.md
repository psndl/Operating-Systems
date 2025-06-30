
## 1. Introduction to Semaphores

### Definition

- **Semaphore**: A shared variable that can be accessed by multiple processes
- Used for process synchronization
- Ensures only one process can be in the critical section at a time
- Can be used for other synchronization purposes

### Key Properties

- Shared between processes
- Must be initialized before use
- Only two operations allowed: **wait** and **signal**

---

## 2. Semaphore Operations

### Wait Operation

**Syntax**: `wait(s)` where `s` is the semaphore

**Behavior**:

```
if (s > 0) {
    s = s - 1;
    return immediately;
} else {
    block the calling process;
    put process to sleep;
}
```

**Key Points**:

- If semaphore value is positive: decrement by 1 and return
- If semaphore value is not positive (≤ 0): block the calling process
- Blocked process sleeps until another process signals

### Signal Operation

**Syntax**: `signal(s)` where `s` is the semaphore

**Behavior**:

```
if (one or more processes sleeping on s) {
    select one process;
    wake up that process;
    move it to ready queue;
} else {
    s = s + 1;
}
```

**Key Points**:

- If processes are waiting: wake up one process
- If no processes waiting: increment semaphore value by 1
- Signal operation never blocks the caller

### Atomicity

- Both wait and signal operations are **atomic**
- Kernel ensures no two processes execute these operations simultaneously
- Implemented by disabling interrupts during execution

---

## 3. Types of Semaphores

### Binary Semaphores (Mutex)

- **Initial value**: 0 or 1
- **Purpose**: Mutual exclusion
- **Also called**: Mutex locks (mutual exclusion locks)
- **Use case**: Solving critical section problems

### Counting Semaphores

- **Initial value**: Any positive integer (e.g., 10, 32, 50)
- **Purpose**: Resource allocation, general synchronization
- **Use case**: Managing multiple instances of a resource

---

## 4. Solving the Critical Section Problem

### Setup

```c
semaphore mutex = 1;  // Initialize to 1

// Process structure:
while (true) {
    wait(mutex);      // Entry section
    // CRITICAL SECTION
    signal(mutex);    // Exit section
    // REMAINDER SECTION
}
```

### Example with Two Processes

**Initial state**: `mutex = 1`

**Process 0 execution**:

1. Calls `wait(mutex)` → mutex becomes 0, process enters critical section
2. Process 0 continues in critical section

**Process 1 execution**:

1. Calls `wait(mutex)` → mutex is 0 (not positive)
2. Process 1 gets blocked and goes to sleep
3. When Process 0 calls `signal(mutex)`, Process 1 wakes up
4. Process 1 can now enter critical section

### Key Benefits

- **No busy waiting**: Blocked processes sleep instead of consuming CPU
- **Guaranteed mutual exclusion**: Only one process in critical section
- **First-come-first-served**: Usually FIFO queue prevents starvation

---

## 5. Synchronization Beyond Critical Sections

### Enforcing Execution Order

**Problem**: Ensure statement S2 executes only after S1

**Solution**:

```c
semaphore x = 0;  // Initialize to 0

// Process P1 (contains S1):
S1;
signal(x);

// Process P2 (contains S2):
wait(x);
S2;
```

**How it works**:

- If P2 runs first: `wait(x)` blocks P2 (since x = 0)
- When P1 executes S1 and calls `signal(x)`: P2 wakes up
- P2 can now execute S2, ensuring S1 → S2 order

---

## 6. Resource Allocation with Counting Semaphores

### Example: Managing 5 Resource Instances

```c
semaphore resource = 5;  // 5 available instances

// Process wanting to use resource:
wait(resource);    // Acquire resource
// USE RESOURCE
signal(resource);  // Release resource
```

**Behavior**:

- Each `wait()` decrements available resources
- When resource = 0, next `wait()` blocks the process
- Each `signal()` increments available resources and may wake waiting processes

---

## 7. Producer-Consumer Problem Example

### Using Semaphore for Buffer Management

```c
semaphore full_cells = 0;  // Number of filled buffer slots

// Producer:
// produce item
put_item_in_buffer();
signal(full_cells);  // Increment count of full cells

// Consumer:
wait(full_cells);    // Wait for at least one item
// remove item from buffer
// consume item
```

**Key Points**:

- Consumer sleeps when buffer is empty (full_cells = 0)
- Producer wakes consumer by signaling after adding item
- Not a complete solution (doesn't handle buffer overflow)

---

## 8. Implementation of Semaphores

### Data Structure

```c
typedef struct {
    int value;                    // Semaphore value
    struct process *list;         // Queue of waiting processes
} semaphore;
```

### Wait Implementation

```c
wait(semaphore *S) {
    S->value--;
    if (S->value < 0) {
        // Add this process to S->list
        block();  // Block calling process
    }
}
```

### Signal Implementation

```c
signal(semaphore *S) {
    S->value++;
    if (S->value <= 0) {
        // Remove a process P from S->list
        wakeup(P);  // Wake up process P
    }
}
```

### Key Implementation Details

#### Helper Functions

- **block()**: Changes process state from running to waiting
- **wakeup(P)**: Changes process P state from waiting to ready, adds to ready queue

#### Interrupt Handling

- Interrupts must be disabled during wait/signal execution
- Prevents race conditions on semaphore data structures
- Re-enabled after operation completes

#### Negative Values

- Semaphore value can become negative
- **Absolute value of negative number = number of waiting processes**
- Example: value = -3 means 3 processes are waiting

---

## 9. Important Properties and Behaviors

### Signal Operation Characteristics

- **Never blocks** the calling process
- Always returns immediately
- Either wakes up a waiting process OR increments semaphore value

### Wait Operation Characteristics

- **May block** the calling process
- Blocks when semaphore value ≤ 0
- Returns immediately when semaphore value > 0

### Process Selection

- When multiple processes wait on same semaphore: typically **FIFO** (First-In-First-Out)
- Prevents starvation
- Implementation-dependent but usually fair

---

## 10. Summary of Applications

|Use Case|Semaphore Type|Initial Value|Purpose|
|---|---|---|---|
|**Critical Section**|Binary|1|Mutual exclusion|
|**Execution Order**|Binary|0|Synchronization|
|**Resource Allocation**|Counting|N (# of resources)|Resource management|
|**Producer-Consumer**|Counting|0 (empty buffer)|Buffer management|

---

## 11. Key Takeaways

1. **Semaphores are powerful synchronization primitives** that solve multiple types of coordination problems
2. **Binary semaphores** (mutex) are perfect for critical section problems
3. **Counting semaphores** handle resource allocation and complex synchronization
4. **No busy waiting** - processes sleep when blocked, saving CPU cycles
5. **Atomic operations** ensure thread safety
6. **Proper initialization** is crucial for correct behavior
7. **Implementation requires careful interrupt handling** to maintain atomicity

---

_Note: This lecture covered the fundamental concepts of semaphores. The complete producer-consumer solution requiring multiple semaphores will be covered in subsequent lectures._