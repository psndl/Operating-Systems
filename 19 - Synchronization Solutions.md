## Hardware-Based Synchronization Solutions

### Overview

Hardware solutions for synchronization require support from machine instructions that provide atomic operations. These solutions implement the entry and exit sections of critical regions using special machine instructions.

## The SWAP Instruction

### Definition

- **SWAP** is a machine instruction that atomically exchanges the values of two memory variables
- Takes two memory operands (a and b) and swaps their values
- Corresponds to the "exchange" instruction in Intel x86 architecture

### Key Properties

- **Atomic**: The instruction execution cannot be interrupted until completion
- **Indivisible**: Once started, it finishes completely before the process can be suspended
- Must be programmed in assembly language for entry and exit sections

### SWAP Implementation in C (Conceptual)

```c
void swap(boolean *a, boolean *b) {
    boolean temp = *a;
    *a = *b;
    *b = temp;
}
```

## Critical Section Solution Using SWAP

### Variables Used

- **key**: Local variable to each process (boolean)
- **lock**: Shared variable among all processes (boolean, initially false)

### Entry Section Algorithm

```c
// Entry Section
key = true;
while (key == true) {
    swap(&key, &lock);
}
// Critical Section follows
```

### Exit Section Algorithm

```c
// Exit Section
lock = false;
```

### How It Works

#### Initial State

- `lock = false` (no process in critical section)
- Each process has its own `key` variable

#### First Process Entry

1. Process sets `key = true`
2. Executes `swap(&key, &lock)`
3. Since `lock` was false, after swap: `key = false`, `lock = true`
4. Loop condition becomes false, process enters critical section

#### Subsequent Process Attempts

1. Another process sets `key = true`
2. Executes `swap(&key, &lock)`
3. Since `lock` is now true, after swap: `key = true`, `lock = true`
4. Loop continues (busy waiting)

#### Process Exit

- Exiting process sets `lock = false`
- Waiting processes can now acquire the lock

## Properties Analysis

### ✅ Mutual Exclusion

- **Satisfied**: Only one process can be in the critical section at a time
- The atomic swap operation ensures no race conditions

### ❌ Bounded Waiting

- **Not Satisfied**: A process might wait indefinitely
- No guarantee on how many times other processes can enter before a waiting process gets its turn
- Same process could repeatedly enter and exit the critical section

### Problem Scenario

1. Process A in critical section
2. Process B waiting (busy looping)
3. Process A exits, but scheduler gives CPU back to A
4. Process A can immediately re-enter critical section
5. Process B continues waiting indefinitely

## Improved Solution: Bounded Waiting with Test-and-Set

### Enhanced Algorithm

Uses additional data structures to ensure bounded waiting:

```c
// Shared variables
boolean lock = false;
boolean waiting[n] = {false, false, ..., false}; // n processes

// Process i's entry section
waiting[i] = true;
key = true;
while (waiting[i] && key) {
    key = test_and_set(&lock);
}
waiting[i] = false;

// Critical Section

// Process i's exit section
j = (i + 1) % n;
while ((j != i) && !waiting[j]) {
    j = (j + 1) % n;
}

if (j == i) {
    lock = false;  // No other process waiting
} else {
    waiting[j] = false;  // Wake up next waiting process
}
```

### How Bounded Waiting is Achieved

#### Entry Protocol

1. Process i sets `waiting[i] = true` (indicates it wants to enter)
2. Attempts to acquire lock using test-and-set
3. Continues only when either no one is waiting or it gets the lock

#### Exit Protocol

1. Exiting process searches for next waiting process in circular order
2. If found, directly grants access to that process
3. If no one waiting, releases the lock

#### Bounded Waiting Guarantee

- Once a process starts waiting, it will enter the critical section before any other process enters twice
- Maximum wait time is bounded by (n-1) entries by other processes

## Limitations of Hardware Solutions

### Busy Waiting Problem

- **CPU Wastage**: Waiting processes consume CPU cycles without doing useful work
- **Inefficiency**: Processes loop continuously checking conditions
- **Better Alternative Needed**: Processes should sleep when they can't proceed

### When Busy Waiting Occurs

- Process wants to enter critical section but can't
- Gets CPU time slice but only uses it for checking lock status
- Other processes could use that CPU time for productive work

## Introduction to Semaphores

### Motivation

- Need solutions that avoid busy waiting
- Processes should sleep when blocked, not consume CPU cycles
- Wake up processes only when they can proceed

### What is a Semaphore?

- **Synchronization primitive**: A tool for process synchronization
- **Integer variable**: Maintained by the kernel
- **Shared resource**: Multiple processes can access it
- **Controlled access**: Only through specific operations

### Semaphore Operations

Can only be accessed via two atomic operations:

1. **wait()** (also called P, down)
2. **signal()** (also called V, up)

### Implementation

- Operations implemented as **system calls**
- **Atomic execution** guaranteed by kernel
- **No direct manipulation** of semaphore value allowed

### Advantages Over Hardware Solutions

- **No busy waiting**: Blocked processes sleep instead of looping
- **Efficient CPU usage**: CPU available for other processes
- **Simpler programming**: Less complex entry/exit sections
- **Kernel support**: Atomic operations guaranteed by OS

## Summary

### Hardware Solutions Progression

1. **Basic SWAP/Test-and-Set**: Provides mutual exclusion but not bounded waiting
2. **Enhanced version**: Adds bounded waiting guarantee
3. **Limitation**: Both suffer from busy waiting problem

### Key Takeaways

- Hardware instructions provide atomic operations essential for synchronization
- Mutual exclusion can be achieved but bounded waiting requires careful algorithm design
- Busy waiting wastes CPU resources
- Semaphores offer a more efficient alternative by eliminating busy waiting

### Next Steps

The lecture continues with detailed semaphore semantics and implementation, which will show how to achieve efficient synchronization without the drawbacks of busy waiting solutions.