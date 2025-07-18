## Overview

This lecture covers interrupt-driven I/O processing, Direct Memory Access (DMA), device classification, and application interfaces for I/O operations in operating systems.

## 1. Interrupt-Driven I/O Processing

### Basic Process Flow

1. **I/O Initiation**
    
    - Device driver initiates I/O operation
    - Writes command to I/O control register
    - Specifies operation type (read/write) and data size
    - I/O controller receives instructions
2. **Hardware Operation**
    
    - Controller executes I/O with device
    - For disk: moves head to correct cylinder, waits for sector
    - For serial port: handles incoming/outgoing data
    - Operation time varies (microseconds to seconds)
3. **CPU During I/O**
    
    - CPU doesn't wait for I/O completion
    - Initiating process may sleep (blocking system call)
    - Kernel schedules other processes
    - Multiple processes can run during I/O operation
4. **Interrupt Handling**
    
    - Device sends interrupt when operation completes
    - CPU suspends current process
    - Interrupt handler executes
    - Data transferred to memory location
    - Waiting process state changed to ready
    - Scheduler may resume interrupted process or select another

### Interrupt Types and Handling

#### Hardware Interrupts

- Come from external devices (keyboard, disk, network)
- Each device has specific interrupt handler
- Handlers registered in interrupt vector table

#### Software Exceptions

- Generated by CPU during execution
- Examples:
    - **Segmentation fault**: Illegal memory access
    - **Page fault**: Access to non-resident page
- Handled by kernel exception handlers

#### Interrupt Vector Table

- **Entries 0-31**: Exception handlers (CPU-generated)
- **Entries 32-255**: Maskable interrupts (device-generated)
- Contains addresses of handler routines
- Kernel consults table when interrupt occurs

## 2. Direct Memory Access (DMA)

### Problem with Programmed I/O

- CPU manually copies data word-by-word
- For large blocks (4KB), CPU performs thousands of operations
- CPU busy with simple copying task
- Inefficient use of CPU resources

### DMA Solution

- **Purpose**: Relieve CPU from data transfer operations
- **Components**: DMA controller chip
- **Operation**: Direct data transfer between device and memory

### DMA Process

1. **Setup**
    
    - CPU instructs DMA controller
    - Specifies: source, destination, transfer size
    - Example: Transfer 4KB from disk controller to memory buffer
2. **Transfer Operation**
    
    - DMA coordinates with device controller
    - Data flows directly between device and memory
    - Bypasses CPU for actual transfer
3. **CPU Parallel Operation**
    
    - CPU executes application code during transfer
    - CPU and DMA share memory bus (cycle stealing)
    - Both can operate simultaneously

### Memory Bus Sharing

- **Cycle Stealing**: DMA "steals" memory bus cycles
- CPU and DMA compete for memory access
- Parallel execution possible despite shared resources

## 3. Application Interface

### System Call Abstraction

- OS provides high-level interface to applications
- Applications don't handle hardware details directly
- Standard library functions (printf, scanf) use system calls
- printf → write system call, scanf → read system call

### Device Classification

Operating systems classify devices to provide consistent interfaces:

#### By Data Transfer

- **Block-oriented**: Hard disks, CD-ROMs (transfer in blocks)
- **Character-oriented**: Keyboard, serial ports (transfer byte-by-byte)

#### By Access Method

- **Sequential**: Tape, keyboard, printer
- **Random Access**: Hard disk, CD-ROM

#### By Sharing

- **Shareable**: Keyboard (multiple processes)
- **Dedicated**: Tape drive (single process)

#### By Direction

- **Read-only**: CD-ROM, scanner
- **Write-only**: Printer, graphics controller
- **Read-write**: Hard disk

## 4. System Architecture Layers

### Hardware Layer

- Physical devices (mouse, keyboard, hard disk)
- Device controllers (hardware chips)
- Each device has corresponding controller

### Software Layers (Bottom to Top)

#### 1. Device Driver Layer

- **Purpose**: Hide hardware differences from OS
- **Functions**: Direct hardware access, register manipulation
- **Specific drivers**: Disk driver, keyboard driver, etc.
- Each driver knows specific controller interface

#### 2. I/O Subsystem

- **Functions**: Buffering, caching, error checking
- **Interface**: Calls device driver functions
- Doesn't directly touch hardware

#### 3. System Call Interface

- **Purpose**: Provide application interface
- **Functions**: Standard I/O operations (read, write, open, close)
- Applications use this layer

## 5. System Calls by Device Type

### Block-Oriented Devices

**System Calls:**

- `read()`: Get data from device
- `write()`: Send data to device
- `seek()`: Random access to specific block

**Access Modes:**

- **File System Mode**: Through files (open, read, write, close)
- **Raw Mode**: Direct block access (used in advanced projects)
- **Memory Mapping**: Map file to virtual address space

### Character-Oriented Devices

**System Calls:**

- `get()`: Read single character
- `put()`: Write single character
- No `seek()` - sequential access only

### Special Device Categories

#### Network Devices

- Not block or character-oriented
- **Packet-based communication**
- Special network API (sockets)
- Functions: open connections, send/receive data

#### Clocks and Timers

**Real-time Clock:**

- Maintains current date/time
- Persists when computer powered off
- System reads time at boot

**Programmable Interval Timer (PIT):**

- Generates periodic interrupts
- Used for: round-robin scheduling, time slicing
- Configurable interval period

**Special System Calls:**

- `get_current_time()`
- `set_timer_interval()`
- `ioctl()`: General-purpose device control

### ioctl System Call

- **Purpose**: General device control interface
- **Parameters:**
    1. File descriptor (device)
    2. Command code
    3. Pointer to data structure
- **Uses**: Clock access, timer configuration, special device commands

## 6. I/O Programming Models

### 1. Blocking (Synchronous) I/O

**Characteristics:**

- Process suspends until I/O completes
- Example: `scanf()` waits for user input
- **Advantages**: Simple programming model
- **Disadvantages**: Can't handle multiple I/O sources simultaneously

**Process:**

1. Application calls system call
2. Process blocks
3. Device driver initiates I/O
4. Hardware performs operation
5. Data transferred to application
6. Process resumes

### 2. Non-blocking I/O

**Characteristics:**

- System calls return immediately
- Returns available data or error condition
- Application must repeatedly check for data
- **Advantages**: Can handle multiple I/O sources
- **Disadvantages**: More complex programming

**Process:**

1. Application calls system call
2. If data available: return data immediately
3. If no data: return error (no blocking)
4. Application continues execution
5. Must retry later for more data

### 3. Asynchronous I/O

**Characteristics:**

- Initiate I/O and continue execution
- Kernel notifies when operation completes
- Uses signals or callbacks
- **Advantages**: Efficient for multiple I/O operations
- **Disadvantages**: Complex programming, race conditions

**Process:**

1. Application initiates I/O
2. Application continues other work
3. Hardware performs I/O in parallel
4. Kernel signals completion
5. Registered handler processes data

### Multi-threading Alternative

- Use separate threads for different I/O sources
- One thread per I/O operation
- Each thread can use blocking I/O
- Simpler than asynchronous programming

## 7. Kernel I/O Subsystem Functions

The lecture mentions several functions performed by the I/O subsystem above the device driver layer:

### Core Functions

- **Buffering**: Temporary data storage
- **Caching**: Frequently accessed data storage
- **Scheduling**: I/O request ordering
- **Error Checking**: Data integrity verification

_Note: Detailed coverage of these functions promised for next lecture_

## Key Takeaways

1. **Interrupt-driven I/O** allows efficient multitasking during I/O operations
2. **DMA** frees CPU from data transfer tasks for better performance
3. **Device classification** enables consistent application interfaces
4. **Layered architecture** separates concerns and provides abstraction
5. **Multiple I/O models** offer different trade-offs between simplicity and efficiency
6. **System calls** provide hardware-independent interface for applications

## Important Concepts for Understanding

- **Interrupt Vector Table**: Maps interrupts to handler functions
- **Cycle Stealing**: DMA and CPU sharing memory bus
- **Device Abstraction**: Hiding hardware details through software layers
- **Blocking vs Non-blocking**: Different approaches to I/O waiting
- **Synchronous vs Asynchronous**: Different timing models for I/O completion