
1. [Process Representation in Linux](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#process-representation-in-linux)
2. [Process Control Block (PCB) Structure](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#process-control-block-pcb-structure)
3. [Context Switching](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#context-switching)
4. [Process Creation Fundamentals](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#process-creation-fundamentals)
5. [Parent-Child Process Relationships](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#parent-child-process-relationships)
6. [Resource Sharing Options](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#resource-sharing-options)
7. [Address Space Alternatives](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#address-space-alternatives)
8. [Fork System Call](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#fork-system-call)
9. [Exec System Call](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#exec-system-call)
10. [Complete Example Walkthrough](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#complete-example-walkthrough)
11. [Memory Layout During Process Creation](https://claude.ai/chat/a50a2fec-9977-4808-b59f-09f3f0d8ba7a#memory-layout-during-process-creation)

---

## Process Representation in Linux

### PCB in Linux Kernel

- Located in `include/linux/sched.h` in kernel source code
- Defined as a structure containing all process information
- The kernel maintains one PCB for each process in the system

### Key Components of Linux PCB Structure

```c
struct task_struct {
    int state;                    // Process state
    pid_t pid;                   // Process identifier
    int time_slice;              // Scheduling time slice
    struct file *files;          // Pointer to open files list
    struct mm_struct *mm;        // Address space information
    // ... many other fields (30-50 total)
};
```

---

## Process Control Block (PCB) Structure

### Essential PCB Fields

#### 1. Process State

- Tracks current state of the process
- States: Running, Ready, Waiting, Terminated

#### 2. Process Identifier (PID)

- **Type**: `pid_t` (essentially an integer)
- **Purpose**: Unique identifier for each process
- **Range**: Typically 0 to 65,000
- **Recycling**: PIDs can be reused after process termination
- **Uniqueness**: All currently running processes have unique PIDs

#### 3. Scheduling Information

- **Time Slice**: Maximum CPU time before suspension
- **Example**: 200 milliseconds maximum execution time
- **Purpose**: Enables fair CPU sharing among processes

#### 4. File Information

- **Structure**: Pointer to list of `struct file` structures
- **Purpose**: Maintains information about open files
- **File Position**: Each open file has a position pointer
- **Behavior**: Position advances after read operations
- **NULL Case**: Points to NULL if no files are open

#### 5. Address Space Information

- **Structure**: Pointer to `struct mm_struct`
- **Content**: Information about process memory segments
- **Includes**: Instructions, data, variables, stack
- **Attributes**: Size, beginning address, end address for each segment

---

## Context Switching

### Definition

- **Context Switch**: Switching CPU execution between processes
- **Overhead**: Time when kernel code runs but no useful application work is done
- **Goal**: Minimize context switch overhead

### Hardware Dependency

- Context switch time depends on hardware support
- Better hardware support = faster context switching
- Operating system must leverage available hardware features

---

## Process Creation Fundamentals

### Process Hierarchy

- **Rule**: Every process is created by another process
- **Exception**: `init` process created by kernel at system startup
- **Examples**:
    - Shell creates processes for user commands
    - Word processor created by shell when user types command name

### Parent-Child Relationships

- **Parent Process**: Creates one or more child processes
- **Child Process**: Created by parent, can create its own children
- **Tree Structure**: Forms a hierarchical tree of processes
- **Unique Parent**: Each process has exactly one parent
- **Parent Changes**: If parent terminates, child may be assigned new parent (often `init`)

### Process Identifiers

- **Assignment**: Kernel assigns unique PID to each new process
- **Uniqueness**: All currently running processes have unique PIDs
- **Recycling**: PIDs can be reused after process termination
- **Range**: Typically 0 to 65,000

---

## Resource Sharing Options

### Sharing Alternatives

1. **Complete Sharing**: Parent and child share all resources
    
    - Text (instructions)
    - Variables and global variables
    - Open files
2. **Partial Sharing**: Share only specific resources
    
    - Example: Share open files but not variables
    - Most common approach
3. **No Sharing**: Completely separate resources
    
    - Each process has independent address space
    - Example: Linux `fork()` system call

### Execution Alternatives

1. **Concurrent Execution**: Parent and child run simultaneously
2. **Sequential Execution**: Parent waits for child to complete

---

## Address Space Alternatives

### Option 1: Duplicate Parent's Address Space

- **Content**: Exact copy of parent's memory
- **Includes**: Instructions, global variables, stack state
- **Independence**: Modifications in one process don't affect the other
- **Memory**: Separate physical memory regions

### Option 2: Load New Program

- **Content**: Completely new program loaded
- **Source**: Different executable file
- **Result**: Different instructions, variables, memory layout
- **Independence**: Completely different execution context

---

## Fork System Call

### Purpose

- Creates new process with duplicate address space
- Does not load new program (use `exec` for that)

### Fork Behavior

```c
pid_t pid = fork();
```

### Return Values

- **Parent Process**: Receives PID of child process
- **Child Process**: Receives 0
- **Error Case**: Returns -1 (negative value)

### Post-Fork Execution

- Both processes continue from the point after `fork()`
- Both processes execute the same code initially
- Use return value to differentiate execution paths

### Example Usage Pattern

```c
pid_t pid = fork();

if (pid < 0) {
    // Error handling
    fprintf(stderr, "Fork failed\n");
    exit(1);
} else if (pid == 0) {
    // Child process code
    printf("I am the child\n");
} else {
    // Parent process code (pid > 0)
    printf("I am the parent, child PID is %d\n", pid);
}
```

---

## Exec System Call

### Purpose

- Replaces current process's address space with new program
- Does not create new process
- Loads executable file into current process's memory

### Common Variants

- `execl()`, `execp()`, `execv()`, `execlp()`, etc.
- Example: `execlp("ls", "ls", NULL);`

### Exec Behavior

1. **Address Space Replacement**: Current memory image is wiped out
2. **Program Loading**: New executable loaded from disk
3. **Execution Start**: Begins execution from new program's entry point
4. **No Return**: If successful, `exec` never returns to calling program

---

## Complete Example Walkthrough

### Sample Program

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    
    pid = fork();
    
    if (pid < 0) {
        fprintf(stderr, "Fork failed\n");
        exit(1);
    } else if (pid == 0) {
        // Child process
        execlp("ls", "ls", NULL);
    } else {
        // Parent process
        wait(NULL);
        printf("Child completed\n");
    }
    
    return 0;
}
```

### Execution Trace

#### Before Fork

- **System State**: One process running
- **Variable `pid`**: Uninitialized
- **Process ID**: Assigned by kernel (assume PID = X)

#### After Fork

- **System State**: Two processes
- **Parent Process**:
    - Variable `pid` = Y (child's PID)
    - Continues execution after fork
- **Child Process**:
    - Variable `pid` = 0
    - Continues execution after fork

#### Conditional Execution

- **Parent Path**: `pid > 0`, executes `wait()` and `printf()`
- **Child Path**: `pid == 0`, executes `execlp()`

#### Wait System Call

- **Purpose**: Parent waits for child to terminate
- **State**: Parent enters waiting state
- **Resume**: Parent continues after child terminates

#### Child Execution

1. **Exec Call**: `execlp("ls", "ls", NULL)`
2. **Address Space**: Replaced with `ls` program
3. **Execution**: Runs `ls` command (lists directory contents)
4. **Termination**: `ls` program terminates

#### Parent Resumption

1. **Notification**: Kernel notifies parent of child termination
2. **State Change**: Parent changes from waiting to ready
3. **Continuation**: Executes `printf("Child completed\n")`
4. **Termination**: Program ends with `return 0`

---

## Memory Layout During Process Creation

### Initial State

```
Physical Memory:
┌─────────────────┐
│     Kernel      │
│   - Text        │
│   - Data        │
│   - Structures  │
├─────────────────┤
│   Parent Proc   │
│   - Text        │
│   - Data        │
│   - Stack       │
│     ├─ int pid  │
└─────────────────┘
```

### After Fork

```
Physical Memory:
┌─────────────────┐
│     Kernel      │
│   - PCB Parent  │
│   - PCB Child   │
├─────────────────┤
│   Parent Proc   │
│   - pid = Y     │
├─────────────────┤
│   Child Proc    │
│   - pid = 0     │
│   (copy of      │
│    parent)      │
└─────────────────┘
```

### After Exec (in child)

```
Physical Memory:
┌─────────────────┐
│     Kernel      │
├─────────────────┤
│   Parent Proc   │
│   (waiting)     │
├─────────────────┤
│   Child Proc    │
│   - New Program │
│   - Different   │
│     memory      │
│     layout      │
└─────────────────┘
```

---

## Key Concepts Summary

### Process Creation Steps

1. **Fork**: Create new process with duplicate address space
2. **Conditional Logic**: Use return value to differentiate parent/child
3. **Exec** (optional): Load new program in child
4. **Wait** (optional): Parent waits for child completion

### Important Notes

- **Single Program**: One program file creates parent and child behavior
- **Differentiation**: Use `fork()` return value for different execution paths
- **Resource Management**: Proper error checking and process synchronization
- **Memory Independence**: After fork, processes have separate address spaces
- **PID Management**: Kernel ensures unique PIDs for all running processes

### Common Patterns

- **Shell Command Execution**: Fork + Exec + Wait
- **Concurrent Processing**: Fork without immediate exec
- **Process Trees**: Multiple forks create hierarchical structures
- **Error Handling**: Always check system call return values

---

## Practical Applications

### Shell Implementation

- Shell forks for each command
- Child process execs the command
- Parent (shell) waits for completion

### Server Applications

- Web server forks for each client connection
- Database systems create worker processes
- Multi-process applications for parallel processing

### System Services

- Init process creates system daemons
- Service management through process hierarchy
- Resource isolation through separate address spaces