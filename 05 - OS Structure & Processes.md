
## Review of Previous Lectures

### OS Services Access Methods

- **GUI (Graphical User Interface)**: Visual interface for users
- **System Programs**: Utilities for file operations, system management
- **System Calls**: Programming interface to OS services

### System Architecture Overview

```
Users
  ↓
System Programs & Application Programs
  ↓
System Call Interface
  ↓
Operating System Kernel
  ↓
Hardware (CPU, Memory, Devices)
```

**Key Points:**

- Applications use system calls to request OS services
- OS manages I/O operations through device drivers
- When not using OS services, programs execute directly on CPU
- When OS services needed, OS routines run on CPU

---

## Operating System Structure

### 1. Simple Structure (Early Systems)

**Example: MS-DOS (1980s)**

- Single program execution only
- No multiprogramming or process management
- Very simple two-layer structure:
    - **Bottom Layer**: Device drivers
    - **Top Layer**: System programs and kernel (file system)

**Characteristics:**

- Written for maximum functionality in minimum space
- Not modular
- One application runs until termination
- Always resident kernel in memory

### 2. Layered Approach

**Structure:**

- Layer 0: Hardware (bottom)
- Layer 1-N: OS layers
- Layer N: User interface (top)

**Advantages:**

- Better modularity
- Each layer uses services of lower layers only
- Easier to debug and maintain

**Disadvantages:**

- Difficult to classify OS functions into strict layers
- Not always clear which layer a function belongs to
- Example: Relationship between process management and file system unclear

### 3. Monolithic Structure

**Example: UNIX, Linux**

**Components:**

- **Kernel**: Everything below system call interface
    - File system
    - CPU scheduling
    - Memory management
    - All other OS functions
- **System Programs**: Above kernel, use system calls

**Characteristics:**

- Large, single executable file
- All functionality in one layer
- Limited internal structure
- Called "monolithic kernel"

**UNIX System Structure:**

```
Users & Applications
       ↓
System Call Interface
       ↓
     Kernel
  (File System, CPU Scheduling, 
   Virtual Memory, etc.)
       ↓
Device Drivers
       ↓
Hardware
```

### 4. Microkernel Approach

**Philosophy:**

- Minimize kernel functionality
- Move services to user space as separate processes

**Microkernel Contains:**

- Basic inter-process communication
- Minimal essential functions

**User Space Services:**

- Process management
- Memory management
- File system
- Run as separate processes above microkernel

**Advantages:**

- Easier to extend
- Easier to port to new architectures
- More reliable (smaller kernel = fewer bugs)
- More secure

**Disadvantages:**

- Performance overhead due to message passing
- All communication goes through microkernel
- Slower than monolithic systems

**Example:** Windows NT (initially designed as microkernel, later converted to more monolithic)

### 5. Modules Approach

**Modern Approach:** Used by Linux, Solaris

**Concept:**

- Core components as separate loadable modules
- Modules loaded only when needed
- Modules become part of kernel when loaded

**Key Differences from Microkernel:**

- Modules run in kernel mode (not as separate processes)
- Direct function calls between modules (no message passing)
- No performance penalty
- Dynamic loading/unloading

**Example Process:**

1. Boot with core kernel
2. Need file system → Load file system module
3. Module integrates into kernel
4. Other kernel functions can call module functions directly

---

## Process Concept

### What is a Process?

**Definition:** A process is a program in execution

**Key Characteristics:**

- Programs execute sequentially (instruction by instruction)
- Multiple processes can run pseudo-parallel on single CPU
- Same program can create multiple processes
- Each process is independent

### Process Components

#### 1. Address Space (Memory Layout)

```
High Memory
┌─────────────┐
│    Stack    │ ← Function calls, local variables
├─────────────┤
│      ↓      │
│             │
│      ↑      │
├─────────────┤
│    Heap     │ ← Dynamic memory allocation
├─────────────┤
│    Data     │ ← Global variables, arrays
├─────────────┤
│    Text     │ ← Program instructions
└─────────────┘
Low Memory
```

**Sections Explained:**

- **Text/Code Section**: Program instructions
- **Data Section**: Global variables and arrays
- **Heap Section**: Dynamic memory allocation
- **Stack Section**: Function parameters, local variables, return addresses

#### 2. CPU Context/State

- **Program Counter (PC)**: Points to next instruction
- **CPU Registers**: Current values during execution
- **Stack Pointer**: Points to top of stack
- **Program Status Word (PSW)**: CPU flags and status

#### 3. Process Resources

- **Open Files**: Files currently being used
- **I/O Devices**: Devices currently accessed
- **Network Connections**: Active network resources

### Address Space Management

**Logical vs Physical Addresses:**

- **Logical Address Space**: Continuous space from 0 to max
- **Physical Address Space**: Actual location in RAM
- **Address Translation**: Hardware converts logical to physical addresses

### Multiprogramming Context

**Why Save Process State?**

- Multiple processes share single CPU
- Context switching between processes
- Must save state when process suspended
- Restore state when process resumes

**Context Switch Process:**

1. Save current process state (registers, PC, etc.)
2. Load next process state
3. Resume execution of new process
4. Later: restore original process and continue

### Virtual CPU Concept

**Physical Reality:**

- One CPU with one program counter
- Processes take turns using CPU
- Frequent context switches

**Virtual Illusion:**

- Each process appears to have its own CPU
- Each process has its own virtual program counter
- Processes seem to run in parallel

---

## Process States

### Basic Process States

#### 1. **New**

- Process is being created
- Not yet ready to run

#### 2. **Ready**

- Process loaded in memory
- Waiting for CPU assignment
- Can run immediately when CPU available

#### 3. **Running**

- Process currently executing on CPU
- Only ONE process can be running at a time (single CPU)

#### 4. **Waiting (Blocked)**

- Process waiting for event to occur
- Cannot use CPU even if available
- Examples: Waiting for I/O completion, user input

#### 5. **Terminated**

- Process finished execution
- May be kept in system temporarily for cleanup

### State Transitions

```
    [New] ──────────→ [Ready]
                        ↑  ↓
                        │  │ Scheduler Dispatch
           I/O Complete │  │
                        │  ↓
    [Waiting] ←──────── [Running]
                   I/O Request │
                               │
                               ↓
                        [Terminated]
```

**Transition Details:**

1. **New → Ready**: Process creation completed, loaded in memory
2. **Ready → Running**: OS scheduler selects process for execution
3. **Running → Ready**:
    - Timer interrupt (time slice expired)
    - Higher priority process arrives
4. **Running → Waiting**:
    - I/O system call
    - Wait for event
    - Resource not available
5. **Waiting → Ready**:
    - I/O operation completed
    - Event occurred
    - Resource became available
6. **Running → Terminated**:
    - Process completes execution
    - Exit system call
    - Process termination

### Important Questions

**Q: How many processes can be in Running state simultaneously on single CPU?** **A:** Only ONE process can be running at any given time.

**Q: How many processes can be in Ready state?** **A:** Multiple processes can be in Ready state, waiting for CPU.

**Q: How many processes can be in Waiting state?** **A:** Multiple processes can be waiting for different events.

### Real-World Examples

**Example 1: Text Editor**

```
User opens text editor → Process created (New)
↓
Editor loaded in memory → Process ready (Ready)
↓
OS assigns CPU → Process running (Running)
↓
User saves file → I/O operation → Process waiting (Waiting)
↓
File save complete → Process ready (Ready)
↓
Continue editing → Process running (Running)
```

**Example 2: Multiple Processes**

- Web browser (Ready - waiting for CPU)
- Music player (Running - currently playing)
- Download manager (Waiting - waiting for network response)
- Text editor (Ready - waiting for CPU)

---

## Key Takeaways

### OS Structure Evolution

1. **Simple → Layered → Monolithic → Microkernel → Modules**
2. **Trade-offs**: Performance vs. Modularity vs. Reliability
3. **Modern systems**: Mostly monolithic with modular components

### Process Management Fundamentals

1. **Process = Program + Execution Context**
2. **Multiprogramming**: Illusion of parallel execution
3. **Context Switching**: Key to multiprogramming
4. **State Management**: Critical for OS scheduling

### Next Topics

- Process scheduling algorithms
- Process creation and termination
- Inter-process communication
- Process synchronization

---

## Review Questions

1. What are the advantages and disadvantages of microkernel vs monolithic kernel?
2. Explain the difference between Ready and Waiting states.
3. Why do we need to save process context during context switching?
4. What components make up a process address space?
5. How does the modules approach combine benefits of monolithic and microkernel designs?