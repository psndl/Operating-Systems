## Overview

This lecture covers solutions to the critical section problem, focusing on preventing race conditions when multiple processes access shared data. We'll explore both software and hardware-based approaches.

## Critical Section Problem Structure

### Basic Program Structure

Every concurrent process should be structured as follows:

```
do {
    entry section      // Request permission to enter critical section
    critical section   // Access shared data
    exit section      // Signal completion
    remainder section // Non-critical code
} while (true);
```

- **Entry Section**: Code to acquire access (e.g., get a lock)
- **Critical Section**: Code that accesses shared data
- **Exit Section**: Code to release access (e.g., release lock)
- **Remainder Section**: Code that doesn't touch shared data

## Required Properties of Solutions

Any valid solution to the critical section problem must satisfy three properties:

### 1. Mutual Exclusion

- If process Pi is executing in its critical section, no other processes can execute in their critical sections
- Only one process can be in the critical section at any time
- Ensures exclusive access to shared data

### 2. Progress

- If several processes are waiting to enter the critical section, the selection of which process enters next cannot be postponed indefinitely
- Prevents deadlock where no process can ever enter
- Ensures that waiting processes will eventually get access

### 3. Bounded Waiting

- There must be a bound on the number of times other processes can enter their critical sections after a process has requested entry
- Prevents starvation where one process waits indefinitely
- Ensures fairness in access to critical section

### Assumptions

- Processes execute at non-zero speeds
- No assumptions about relative speeds of processes
- No assumptions about process priorities

## Peterson's Solution

### Overview

- **Type**: Pure software solution
- **Scope**: Works only for exactly 2 processes
- **Requirements**: No kernel or hardware support needed
- **Assumption**: Basic machine instructions (load/store) execute atomically

### Shared Variables

Peterson's solution uses two shared variables:

1. **`turn`** (integer): Indicates whose turn it is to enter critical section
    
    - Value 0: Process 0's turn
    - Value 1: Process 1's turn
2. **`flag[2]`** (boolean array): Indicates if a process wants to enter critical section
    
    - `flag[i] = true`: Process i wants to enter critical section
    - `flag[i] = false`: Process i doesn't want to enter critical section

### Algorithm Implementation

#### For Process i (where j = 1-i):

```c
do {
    // Entry Section
    flag[i] = true;        // Indicate interest
    turn = j;              // Give turn to other process
    while (flag[j] && turn == j);  // Wait if other wants access and it's their turn
    
    // Critical Section
    
    // Exit Section  
    flag[i] = false;       // No longer interested
    
    // Remainder Section
    
} while (true);
```

### How Peterson's Solution Works

#### Case 1: Only one process wants to enter

- Process sets its flag to true
- Sets turn to other process (being polite)
- Checks condition: other process flag is false, so condition fails
- Process enters critical section immediately

#### Case 2: Both processes want to enter simultaneously

- Both set their flags to true
- Both try to set turn to the other process
- Only the last assignment to turn matters (turn = 0 or turn = 1)
- Process for whom `turn` equals their ID must wait
- Other process enters critical section
- When exiting, flag is set to false, allowing waiting process to proceed

#### Case 3: Process wants to re-enter immediately

- After exiting, if same process tries to re-enter immediately
- It sets turn to other process ID
- If other process is waiting, the waiting process gets priority
- Prevents one process from monopolizing critical section

### Correctness Analysis

#### Mutual Exclusion ✓

- For both processes to be in critical section, both conditions must be false:
    - `flag[j] && turn == j` must be false for both processes
- Since `turn` can only have one value (0 or 1), at most one condition can be false
- Therefore, at most one process can be in critical section

#### Progress ✓

- If no process is in critical section and some processes want to enter:
- At least one process will have the condition evaluate to false
- That process can enter, ensuring progress

#### Bounded Waiting ✓

- After a process requests entry, it can be bypassed at most once
- The `turn` variable ensures alternation between processes
- Prevents indefinite postponement

## Hardware Solutions

### Motivation for Hardware Support

- Software solutions like Peterson's are limited (only 2 processes)
- Hardware can provide atomic operations to simplify synchronization
- More efficient than complex software protocols

### Interrupt Disabling (Uniprocessor Only)

#### Concept

```c
// Entry section
disable_interrupts();

// Critical section

// Exit section  
enable_interrupts();
```

#### How it works

- Disabling interrupts prevents context switches
- No other process can run during critical section
- Guarantees exclusive access to shared data

#### Limitations

- **Uniprocessor only**: Other CPUs in multiprocessor systems are unaffected
- **Dangerous**: User processes shouldn't disable interrupts
- **Inefficient**: Blocks all interrupts, not just scheduling

### Atomic Hardware Instructions

Modern hardware provides special atomic instructions that perform multiple operations indivisibly.

#### Test-and-Set Instruction

##### Definition (in C-like pseudocode):

```c
boolean test_and_set(boolean *target) {
    boolean rv = *target;  // Read current value
    *target = true;        // Set to true
    return rv;             // Return original value
}
```

##### Solution using Test-and-Set:

```c
do {
    // Entry section
    while (test_and_set(&lock));  // Busy wait until lock is acquired
    
    // Critical section
    
    // Exit section
    lock = false;  // Release lock
    
    // Remainder section
    
} while (true);
```

##### How Test-and-Set Works:

1. **Lock available (lock = false)**:
    
    - `test_and_set(&lock)` returns false, sets lock = true
    - While condition is false, process enters critical section
    - Lock is now set to prevent others from entering
2. **Lock taken (lock = true)**:
    
    - `test_and_set(&lock)` returns true, keeps lock = true
    - While condition is true, process busy waits
    - Continues checking until lock becomes available

##### Assembly Implementation Example:

```assembly
entry_section:
    test_and_set lock    ; Atomic: read lock to register, set lock = 1
    cmp register, 0      ; Compare previous value with 0
    jne entry_section    ; If not equal (was 1), jump back and try again
    ret                  ; If equal (was 0), we got the lock, return

exit_section:
    mov lock, 0          ; Release lock
    ret
```

#### Swap Instruction

##### Definition:

```c
void swap(boolean *a, boolean *b) {
    boolean temp = *a;
    *a = *b;
    *b = temp;
}
```

##### Solution using Swap:

```c
do {
    // Entry section
    key = true;
    while (key == true)
        swap(&lock, &key);  // Try to get lock
    
    // Critical section
    
    // Exit section
    lock = false;  // Release lock
    
    // Remainder section
    
} while (true);
```

## Comparison of Solutions

|Solution|Processes|Hardware Support|Complexity|Efficiency|
|---|---|---|---|---|
|Peterson's|2 only|None|Medium|Good|
|Test-and-Set|Any number|Atomic instruction|Low|Good (busy waiting)|
|Swap|Any number|Atomic instruction|Low|Good (busy waiting)|
|Interrupt Disable|Any number|Privileged instruction|Very Low|Poor (blocks all interrupts)|

## Key Takeaways

1. **Critical Section Problem** is fundamental in concurrent programming
2. **Three essential properties**: Mutual Exclusion, Progress, Bounded Waiting
3. **Peterson's Solution** proves software-only solutions are possible but limited
4. **Hardware support** enables more general and efficient solutions
5. **Atomic instructions** are key to preventing race conditions
6. **Trade-offs exist** between complexity, generality, and efficiency

## Important Notes

- Race conditions can occur even with simple operations like checking and setting variables
- The atomicity of hardware instructions is crucial for correctness
- Busy waiting (spinning) wastes CPU cycles but provides immediate response
- Real systems often use more sophisticated mechanisms (semaphores, mutexes) built on these primitives

## Next Topics

- Semaphores and higher-level synchronization primitives
- Implementation of synchronization in actual operating systems
- Performance considerations and alternatives to busy waiting