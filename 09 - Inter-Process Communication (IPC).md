
## Overview

This lecture covers Inter-Process Communication (IPC) mechanisms that allow processes to communicate and synchronize with each other. We explore two main local IPC methods: **Shared Memory** and **Message Passing**, followed by distributed communication methods.

---

## 1. Introduction to IPC

### What is IPC?

Inter-Process Communication (IPC) refers to mechanisms that allow processes to:

- Exchange data and information
- Synchronize their activities
- Coordinate their execution

### Why is IPC Needed?

- Processes have separate address spaces by default
- Cannot directly access each other's variables or memory
- Need structured ways to share information and coordinate

---

## 2. Shared Memory Approach

### Concept

- Creates a shared memory region accessible by multiple processes
- Processes can directly read/write to this shared region using pointers
- Fast method since kernel involvement is minimal after setup

### Characteristics

- **Fast**: Direct memory access without system calls for each operation
- **Requires synchronization**: Risk of race conditions when multiple processes access same data
- **Local only**: Only works for processes on the same machine

### Producer-Consumer Problem Example

A classic problem solved using shared memory:

- **Producer process**: Generates data and puts it in shared memory
- **Consumer process**: Reads and processes data from shared memory
- Real-world analogy: Web server (producer) and web browser (consumer)

### Advantages and Disadvantages

**Advantages:**

- Very fast data exchange
- Minimal kernel overhead after setup

**Disadvantages:**

- Complex synchronization required
- Risk of race conditions
- Requires careful programming to avoid conflicts

---

## 3. Message Passing Approach

### Concept

- Processes communicate by sending/receiving messages through the kernel
- No shared variables needed
- Kernel provides a logical communication link between processes

### How It Works

1. **Process A** wants to send data to **Process B**
2. Process A calls `send()` system call, giving message to kernel
3. Kernel buffers the message
4. Process B calls `receive()` system call to get the message
5. Message is transferred from kernel to Process B

### Basic Operations

- **send(message, destination)**: Send a message to another process
- **receive(message, source)**: Receive a message from another process

### Characteristics

- **Safer**: No race conditions since kernel manages access
- **Slower**: Every message requires system calls
- **Easier to program**: No need to handle synchronization manually

---

## 4. Message Passing Design Issues

### 4.1 Naming (How to identify recipient)

#### Direct Communication

- Processes explicitly name each other
- Example: `send(message, ProcessP)` - directly specify recipient
- **Properties:**
    - Links established automatically
    - One link per pair of processes
    - Links can be unidirectional or bidirectional

#### Indirect Communication

- Messages sent to/received from **mailboxes** (also called ports or message queues)
- Example: `send(message, MailboxA)` - send to mailbox, not specific process
- **Properties:**
    - Link established only if processes share a mailbox
    - Multiple processes can use same mailbox
    - Processes can share multiple mailboxes

### 4.2 Synchronization (Blocking vs Non-blocking)

#### Blocking (Synchronous)

**Blocking Send:**

- Sender waits until message is successfully delivered
- If no buffer: wait until receiver gets the message
- If bounded buffer: wait if buffer is full

**Blocking Receive:**

- Receiver waits until a message arrives
- Like `scanf()` - blocks until input is available

#### Non-blocking (Asynchronous)

**Non-blocking Send:**

- Returns immediately with success/failure indication
- Programmer must check return value
- If failed, must retry later

**Non-blocking Receive:**

- Returns immediately
- Either gets message (success) or gets nothing (failure)
- Programmer must handle both cases

### 4.3 Buffering (How many messages to store)

#### Zero Capacity

- No buffering in kernel
- Sender must wait for receiver (rendezvous)
- Direct handoff of messages

#### Bounded Capacity

- Finite buffer size (e.g., 10 messages)
- Sender blocks when buffer is full
- Most common in real systems

#### Unbounded Capacity

- Infinite buffer (theoretical only)
- Sender never blocks
- Not practical in real systems

---

## 5. Implementation Examples

### 5.1 UNIX/Linux Shared Memory APIs

#### System V API

```c
// Create shared memory
int shmid = shmget(key, size, IPC_CREAT | 0666);

// Attach to shared memory
char *ptr = (char*) shmat(shmid, NULL, 0);

// Use shared memory
sprintf(ptr, "Hello from process!");

// Detach from shared memory
shmdt(ptr);
```

#### POSIX API

```c
// Create/open shared memory
int fd = shm_open(name, O_CREAT | O_RDWR, 0666);

// Set size
ftruncate(fd, size);

// Map to address space
char *ptr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);

// Use shared memory
sprintf(ptr, "Hello from process!");
```

### 5.2 Message Passing Examples

#### Mailbox Issues

When multiple processes share a mailbox:

- **Problem**: If P1 sends message, should P2 or P3 receive it?
- **Solutions:**
    - Restrict to at most 2 processes per mailbox
    - Synchronize receive operations (first caller gets message)
    - Arbitrary selection with sender notification

---

## 6. Distributed Communication

For processes running on **different machines**, we need different approaches:

### 6.1 Sockets

- Network communication endpoints
- Can work locally or across networks
- Stream (TCP) or datagram (UDP) based

### 6.2 Remote Procedure Calls (RPC)

- Makes remote function calls appear local
- Handles network communication transparently
- Client-server model

### 6.3 Remote Method Invocation (RMI)

- Object-oriented version of RPC
- Invoke methods on remote objects
- Used in distributed object systems

**Note**: These distributed methods can also be used for local communication between processes on the same machine.

---

## 7. Comparison: Shared Memory vs Message Passing

|Aspect|Shared Memory|Message Passing|
|---|---|---|
|**Speed**|Fast (direct memory access)|Slower (system calls required)|
|**Synchronization**|Manual (risk of race conditions)|Automatic (kernel managed)|
|**Programming Complexity**|More complex|Easier|
|**Kernel Involvement**|Minimal (after setup)|High (every message)|
|**Scope**|Local processes only|Local or distributed|
|**Buffer Management**|Programmer responsibility|Kernel responsibility|

---

## 8. Key Takeaways

1. **IPC is essential** for process coordination and data sharing
2. **Shared Memory** is fast but requires careful synchronization
3. **Message Passing** is safer but has higher overhead
4. **Design choices** in message passing affect system behavior:
    - Direct vs indirect naming
    - Blocking vs non-blocking operations
    - Buffer size and management
5. **Distributed communication** requires special mechanisms like sockets, RPC, or RMI
6. **Choice of IPC method** depends on:
    - Performance requirements
    - Complexity tolerance
    - Local vs distributed needs
    - Synchronization requirements

---

## 9. Real-World Applications

- **Web servers and browsers**: Producer-consumer pattern
- **Database systems**: Multiple processes accessing shared data
- **Distributed systems**: Microservices communication
- **Operating system components**: Kernel and user space communication
- **Multimedia applications**: Audio/video streaming between processes

---

_This completes the coverage of Inter-Process Communication mechanisms in operating systems. Understanding these concepts is crucial for designing efficient multi-process applications and distributed systems._