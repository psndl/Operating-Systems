
## 1. Interrupt Handling

### What Are Interrupts?

The operating system executes in the CPU when specific events occur:

- **Hardware interrupts**: Signals from hardware devices
- **Software interrupts (traps)**: Result from application requests or illegal operations
    - System calls (requesting OS services like I/O)
    - Illegal operations (accessing invalid memory, division by zero)

### Interrupt Processing Steps

1. **Suspend Current Application**: When interrupt arrives, currently running application is suspended
2. **Save CPU State**: OS preserves the application's state by storing:
    - CPU registers
    - Program counter
    - Other processor state information
3. **Execute Interrupt Handler**: OS runs appropriate interrupt service routine
4. **Restore State**: After handling interrupt, application's CPU state is restored
5. **Resume Execution**: Application continues as if nothing happened

### Interrupt Vector Table

- Hardware determines interrupt type and obtains interrupt number
- Interrupt number serves as index into **interrupt vector table**
- Table contains addresses of interrupt service routines
- Different interrupt types have separate handler routines (keyboard, disk, etc.)

### Timeline Example: I/O Operation

1. Application requests I/O operation
2. I/O device performs operation in parallel with CPU execution
3. When I/O completes, device controller interrupts CPU
4. CPU suspends current application
5. Interrupt service routine handles the interrupt (e.g., copying data from device controller to memory)
6. CPU resumes executing the suspended application

---

## 2. System Calls and I/O Management

### Application-OS Interaction

- Applications cannot directly access I/O devices
- Applications use high-level functions (printf, scanf) for I/O
- These functions trigger **system calls** - services provided by the OS

### System Call Process

1. Application calls I/O function
2. Function triggers OS system call routine
3. OS routine executes in CPU (kernel mode)
4. OS may call device driver routines
5. Device controller communicates with actual device
6. OS maintains device status table (one entry per device)

### Blocking vs Non-blocking Calls

- **Blocking call**: Application waits for operation to complete
- **Non-blocking call**: Application continues execution while operation proceeds

### Direct Memory Access (DMA)

- **Without DMA**: CPU manually copies data from device controller to memory
- **With DMA**: Specialized hardware handles data transfer without CPU intervention
- **Benefits**:
    - Speeds up data movement
    - Frees CPU for other tasks
    - Allows CPU to run application code during transfers

---

## 3. Computer System Organization

### Basic Components

- **CPU**: Processing unit
- **Memory**: Primary storage for programs and data
- **Devices**: Non-volatile storage and I/O (keyboard, disk, etc.)

### Program Execution Process

1. **System Boot**: OS kernel loaded into memory first
2. **Program Loading**: Program loaded from disk to memory before execution
3. **Program Structure**: Sequence of instructions + data (variables, arrays)
4. **Thread of Execution**: Sequential execution of instructions
5. **I/O Operations**: Handled through OS system calls

---

## 4. Storage Hierarchy

### Storage Types

- **Primary Storage (Main Memory)**:
    - Large enough to hold programs
    - Directly accessible by CPU
    - Volatile (data lost when power off)
- **Secondary Storage (Hard Disk)**:
    - Non-volatile storage
    - Larger capacity than main memory
    - Slower access than main memory
    - Extension of main memory

### Storage Hierarchy Characteristics

From top to bottom (registers → cache → memory → disk → optical/tape):

- **Size**: Increases as you go down
- **Speed**: Decreases as you go down
- **Cost per bit**: Decreases as you go down
- **Volatility**: Generally decreases as you go down

### Caching Principle

- **Purpose**: Speed up access to frequently used data
- **Method**: Copy frequently accessed data to faster storage
- **Application**: Used at multiple levels (registers-memory, memory-cache, disk-memory)
- **Key Decisions**:
    - Cache size
    - Placement policy
    - Replacement policy when cache is full

---

## 5. Multiprocessor Systems

### Definition

Computer system with multiple CPUs sharing:

- Same main memory (RAM)
- Same storage devices
- Same I/O devices

### Advantages

1. **Increased Throughput**: More work done per unit time
2. **Economy of Scale**: Cheaper than multiple separate computers
3. **Increased Reliability**: System continues operating if one CPU fails

### Types of Multiprocessing

#### Asymmetric Multiprocessing

- One processor acts as **master**
- Other processors are **slaves**
- Master controls task assignment to slaves

#### Symmetric Multiprocessing (SMP)

- All processors have **equal roles and capabilities**
- OS decides which CPU runs which task
- Common in modern dual-core/multi-core systems
- No special master processor

### Multi-core Design

- Multiple CPUs implemented on **single physical chip**
- Each core has its own registers and usually its own cache
- Cores share main memory
- Common in modern computers

---

## 6. Clustered Systems

### Definition

Multiple complete computer systems working together, connected via network

### Characteristics

- Each system may have single or multiple processors
- Systems connected via fast local area network
- Often share storage via Storage Area Network (SAN)
- More expensive than multiprocessor systems

### Types of Clustering

#### Asymmetric Clustering

- One system operates in **hot standby mode**
- Standby system takes over if active system fails
- Provides high availability

#### Symmetric Clustering

- Multiple systems run applications simultaneously
- Systems monitor each other's availability
- If one fails, others take over its applications

---

## 7. Multiprogramming

### Concept

Operating system capability to run multiple programs simultaneously on same hardware

### Historical Context

- **Past**: Systems could run only one program at a time
- **Present**: Can run multiple applications concurrently (web browser, email, etc.)

### Benefits

1. **User Convenience**: Can run multiple applications
2. **System Efficiency**: Better CPU utilization

### Efficiency Explanation

- **Single Program**: When program waits for I/O, CPU sits idle
- **Multiple Programs**: While one program waits for I/O, CPU can run another program
- **Result**: Better CPU utilization and higher throughput

### Implementation Requirements

- Multiple programs loaded in memory simultaneously
- Memory partitioning and management required
- CPU scheduling to switch between programs

---

## 8. Time Sharing (Multitasking)

### Definition

Logical extension of multiprogramming with rapid CPU switching between programs

### Purpose

Enable **interactive computing** with fast response times

### Key Characteristics

- CPU switches between jobs very frequently (e.g., every 100 milliseconds)
- Users can interact with programs while they're running
- Response time typically less than 1 second for acceptable user experience

### Comparison with Multiprogramming

- **Multiprogramming**: Good for non-interactive applications, slower switching
- **Time Sharing**: Required for interactive applications, rapid switching

### Memory Layout in Multitasking System

- OS always resident in memory
- Multiple user programs loaded simultaneously
- **Space multiplexing**: Memory divided among processes
- **Time multiplexing**: CPU time divided among processes

---

## 9. System Protection

### Dual Mode Operation

#### Purpose

Protect OS from user programs and user programs from each other

#### Hardware Support Required

- **Mode bit** in CPU register
    - 0 = Kernel mode (system mode)
    - 1 = User mode

#### Instruction Types

- **Non-privileged instructions**: Can execute in both modes
- **Privileged instructions**: Can execute only in kernel mode
    - Examples: I/O operations, memory management operations

#### Mode Transitions

- **User to Kernel**:
    - Triggered by system call (trap instruction)
    - Hardware automatically sets mode bit to 0
    - OS code begins execution
- **Kernel to User**:
    - When OS finishes, sets mode bit to 1
    - Returns control to user program

### Timer Mechanism

#### Purpose

Prevent processes from running indefinitely and monopolizing CPU

#### Operation

1. OS sets timer to interrupt after specified time (e.g., 100ms)
2. Gives CPU control to user process
3. Timer generates hardware interrupt when time expires
4. Hardware automatically suspends current process
5. OS timer handler gains CPU control
6. OS can schedule same or different process
7. Process repeats with timer reset

#### Benefits

- Prevents infinite loops from hanging system
- Ensures fair CPU sharing among processes
- Enables time-sharing implementation

---

## 10. Key Concepts Summary

### Virtual Memory

- Extends physical memory capacity
- Allows running programs larger than available RAM
- Details covered in later lectures

### CPU Scheduling

- Algorithms to decide which process runs next
- Important for multitasking systems
- Various algorithms with different properties

### Protection Mechanisms

- Dual mode operation prevents unauthorized access
- Timer prevents CPU monopolization
- Memory protection prevents process interference

### System Efficiency

- Multiprogramming improves CPU utilization
- Time sharing enables interactive computing
- Caching improves access speed across storage hierarchy

---

## Next Lecture Preview

- Basic OS functions and components
- Detailed exploration of OS structures
- Process management concepts