## Course Overview

### Learning Objectives

1. Understand operating system concepts and components
2. Gain hands-on programming experience through projects
3. Learn low-level programming with operating systems
4. Study real operating system examples (primarily Linux)
5. Understand OS design decisions and implementation

### Course Topics Schedule

1. **Week 1**: Introduction to Operating Systems (Chapters 1-2)
2. **Week 2+**: Processes and Threads (Chapter 3)
3. CPU Scheduling
4. Synchronization Problems
5. Deadlocks
6. Main Memory Management
7. Virtual Memory
8. File Systems
9. Mass Storage Structure and Management
10. Input/Output Management
11. Protection Issues
12. Distributed Systems (brief coverage)

## What is an Operating System?

### Definition

An operating system is a **program** (piece of software) that acts as an **intermediary** between:

- Users of a computer
- Application programs
- Computer hardware

### Goals of an Operating System

1. **User Convenience**: Makes computer system convenient and easy to use
2. **Hardware Management**: Utilizes computer hardware efficiently
3. **Resource Management**: Manages and allocates system resources
4. **Process Control**: Controls program execution to prevent interference

### Components of a Computer System

#### 1. Hardware

- **Basic Resources**: CPU, memory, I/O devices
- **Function**: Provides basic computing resources
- **Examples**: Keyboard, monitor, mouse, CPU, RAM, hard disk

#### 2. Operating System

- **Function**: Controls and coordinates hardware use among applications and users
- **Role**: System software that manages hardware resources

#### 3. Application Programs

- **Function**: Define ways system resources are used to solve user problems
- **Examples**: Word processors, compilers, web browsers, database systems, video games
- **Categories**:
    - System programs (compilers, assemblers, text editors)
    - Application programs (databases, web browsers, word processors)

#### 4. Users

- **Types**: People, machines, other computers
- **Function**: Entities that utilize the computer system

### System Architecture

```
    Users
      ↓
Application Programs & System Programs
      ↓
   Operating System
      ↓
Computer Hardware
```

## Operating System Roles

### 1. Resource Allocator

- Manages all hardware and software resources
- Decides resource allocation among competing processes
- Resolves conflicts for efficient and fair resource use
- **Resources**: Printer, keyboard, hard disk (hardware); files (software)

### 2. Control Program

- Controls program execution
- Prevents programs from interfering with each other
- Manages concurrent execution of multiple applications
- Ensures proper sharing of CPU, memory, and I/O devices
- Handles all I/O device access

## Kernel vs Operating System

### Kernel Definition

- **Core Component**: The one program running at all times on the computer
- **Location**: Single executable in OS installation media
- **Function**: Implements all core OS functionality (processes, files, etc.)
- **Relationship**: Kernel = Operating System (in this course context)

### System Startup Process

1. **Power On**: Computer starts
2. **Bootstrap Program**:
    - Stored in ROM (firmware)
    - First program to run
    - Initializes system components
3. **Kernel Loading**: Bootstrap loads OS kernel from hard disk to memory
4. **System Ready**: Kernel provides user interface (GUI/command line)
5. **Application Execution**: Users can run programs that utilize OS services

## Computer System Organization

### Hardware Components

#### Basic Structure

- **CPU**: One or more processors
- **Memory**: Main system memory (RAM)
- **I/O Devices**: Keyboard, printer, monitor, hard disk, network card

#### Device Controllers

- **Purpose**: Interface between CPU and devices
- **Function**: CPU interacts with device controllers, not devices directly
- **Capabilities**:
    - Processing capability (special-purpose processor or hardware logic)
    - Local buffer memory (size varies by device type)
    - Examples: 16 bytes for serial port, kilobytes for hard disk

#### System Bus Architecture

- **Connection**: CPUs and device controllers connected via common bus
- **Memory Access**: All components compete for memory access
- **Concurrent Execution**: CPU and I/O controllers can operate simultaneously

### I/O Operations

#### Data Movement Process

1. **CPU Role**: Moves data between main memory and controller buffers
2. **I/O Process**: Data moves from device to controller buffer, then to main memory
3. **Example Operations**:
    - **Read**: Device → Controller Buffer → Main Memory
    - **Write**: Main Memory → Controller Buffer → Device

#### Interrupt Mechanism

- **Purpose**: Device controllers inform CPU when I/O operations complete
- **Process**:
    1. Device controller sends interrupt to CPU
    2. CPU suspends current application
    3. Interrupt service routine executes
    4. Application resumes after interrupt handling

## Operating System Execution

### When OS Code Runs

Operating system is **interrupt-driven** - loaded in memory always but executes only when needed:

#### 1. Hardware Interrupts

- **Source**: I/O device controllers
- **Trigger**: I/O operation completion
- **Action**: CPU executes interrupt service routine (part of OS)

#### 2. Software Interrupts (Traps)

- **System Calls**: Applications request OS services
- **Exceptions**: Illegal operations (e.g., divide by zero)
- **Action**: CPU switches to OS code to handle request/error

### Interrupt Handling Process

1. **Interrupt Occurs**: Hardware or software interrupt
2. **Application Suspended**: Current program stops execution
3. **Vector Lookup**: Interrupt vector table provides service routine address
4. **OS Execution**: Interrupt service routine runs in CPU
5. **Resume Application**: Original program continues after interrupt handling

### System Call Mechanism

- **Purpose**: Applications cannot directly access hardware
- **Process**: Applications call OS functions (e.g., printf() triggers OS routine)
- **Result**: OS handles actual hardware interaction
- **Examples**: File I/O, network communication, device access

## Key Concepts Summary

### Operating System Characteristics

- **Always Present**: Loaded in memory when computer powers on
- **Interrupt Driven**: Executes only when interrupts occur or services requested
- **Resource Manager**: Controls all system resources
- **Hardware Abstraction**: Provides easy-to-use interface for applications

### System Integration

- **Layered Design**: Hardware → OS → Applications → Users
- **Service Provider**: OS provides services to applications
- **Protection**: Prevents applications from interfering with each other
- **Efficiency**: Enables multiple programs to share single CPU and memory

### Important Notes for Students

1. **Attendance**: Important for understanding concepts
2. **Programming Practice**: Essential for course success
3. **Debugging Skills**: Be persistent, use forums for help
4. **Linux Familiarity**: Install and practice using Linux
5. **C Programming**: Refresh C/C++ knowledge immediately

---

_Next Lecture: Continue with computer system organization details and begin covering processes and threads (Chapter 3)_
