## Part 1: Inter-Process Communication Mechanisms

### 1. Communication Between Processes on Different Machines

When processes need to communicate across different machines connected via a network (like the Internet), several mechanisms are available:

#### A. Sockets

**Definition**: A socket is an endpoint for communication that can be identified by an IP address and port number.

**Socket Identification**:

- Each socket is uniquely identified by: `IP_address:port_number`
- Example: `192.168.1.100:1625`
- Port numbers ensure unique identification when multiple processes run on the same machine

**How Sockets Work**:

1. **Server Side**: Creates a socket and listens on a well-known port
    
    - Web servers typically use port 80
    - FTP servers use port 21
    - Custom applications can use any available port
2. **Client Side**: Creates a socket and connects to the server's socket
    
3. **Connection Establishment**:
    
    - Connection identified by four parameters:
        - Source IP address
        - Source port number
        - Destination IP address
        - Destination port number

**Socket Programming**:

- System provides socket API with functions to:
    - Create sockets
    - Send and receive messages
    - Establish connections
- Programmer works with high-level socket functions
- Underlying network protocols handle message transport

#### B. Remote Procedure Call (RPC)

**Concept**: Allows calling procedures/functions that execute on remote machines as if they were local function calls.

**Key Differences from Sockets**:

- **Sockets**: Explicit message building, sending, receiving, and processing
- **RPC**: Work with function calls and return values (abstracts away messaging)

**RPC Mechanism**:

1. **Client-side proxy**: Intercepts remote procedure calls
2. **Message creation**: Proxy creates message with:
    - Procedure name
    - Parameters
3. **Message transmission**: Sends message to remote machine
4. **Server-side execution**: Remote machine executes the procedure
5. **Result return**: Return value sent back to client
6. **Transparent completion**: Function appears to execute locally

**Parameter Marshaling**:

- Process of packaging parameters into standard format
- Ensures compatibility between different systems
- Uses standardized encoding for cross-platform communication

#### C. Remote Method Invocation (RMI)

**Java-Specific Feature**: Allows Java programs to invoke methods on remote objects.

**Similarities to RPC**:

- Transparent remote execution
- Parameter marshaling required
- Client-server communication model

**Key Characteristics**:

- Object-oriented approach (methods vs. procedures)
- Java-specific implementation
- Enables distributed object systems
- Service-oriented architecture support

## Part 2: Introduction to Threading

### 1. From Single-Threaded to Multi-Threaded Processes

#### Single-Threaded Process Characteristics

**Thread of Control**: A single execution sequence of instructions

- Instructions executed one by one
- Sequential execution flow
- When process blocks (e.g., I/O operation), entire process stops
- CPU cannot be utilized by the process during blocking

**Limitations**:

- Cannot perform multiple activities concurrently
- I/O blocking prevents other computations
- Poor resource utilization
- Limited responsiveness

#### Multi-Threaded Process Benefits

**Concurrent Execution**: Multiple threads of control within same process

- Pseudo-parallel execution (time-sharing on single CPU)
- True parallelism on multi-processor systems
- Better resource utilization

**Example - Word Processor Application**:

- **Thread 1**: Handle keyboard input
- **Thread 2**: Auto-save document to disk
- **Thread 3**: Spell-check and grammar processing
- **Thread 4**: Background printing

### 2. Thread Architecture and Memory Layout

#### Shared Resources (Among All Threads)

- **Code segment**: All threads execute same program
- **Data segment**: Global variables accessible to all threads
- **Open files**: File descriptors shared
- **Process ID**: Same PID for all threads

#### Per-Thread Resources

- **Stack**: Each thread has its own stack for function calls
- **Registers**: CPU state (Program Counter, registers)
- **Thread ID**: Unique identifier within process
- **Thread Control Block (TCB)**: Similar to PCB but for threads

### 3. Thread Creation and Execution Model

#### Thread Creation Example (Pseudocode)

```c
// Main function - creates initial thread
int main() {
    // Main thread starts here
    
    // Create first thread
    thread_create(function1);
    
    // Continue main thread execution
    // ... some code ...
    
    // Create second thread  
    thread_create(function2);
    
    // Create third thread
    thread_create(function1);  // Same function, different thread
    
    return 0;
}

void function1() {
    // Thread execution starts here
    // Can access global variables
    // Has own stack for local variables
}

void function2() {
    // Another thread entry point
}
```

#### Execution Flow

1. **Program Start**: Main thread begins execution
2. **Thread Creation**: New threads created with specified entry points
3. **Concurrent Execution**: Multiple threads run pseudo-parallel
4. **Shared Data Access**: All threads can access global variables
5. **Independent Stacks**: Each thread maintains separate stack

### 4. Thread Scheduling and Management

#### Scheduling Characteristics

- **Schedulable Entities**: Each thread can be scheduled independently
- **Process vs. Thread Scheduling**:
    - Process gets CPU time
    - Within process, threads share allocated time
- **No Execution Order Guarantees**: Cannot assume thread execution order

#### Thread States

Similar to process states:

- **Running**: Currently executing on CPU
- **Ready**: Waiting to be scheduled
- **Blocked**: Waiting for I/O or other resources

### 5. Benefits of Multi-Threading

#### A. Responsiveness

- **Non-blocking Operation**: When one thread blocks, others continue
- **User Interface**: UI remains responsive during background processing
- **Example**: Text editor auto-save while user types

#### B. Resource Sharing

- **Easy Data Sharing**: Global variables provide simple sharing mechanism
- **No Complex IPC**: No need for message queues, shared memory setup
- **Efficient Communication**: Direct memory access between threads

#### C. Economy

- **Fast Creation**: Thread creation much faster than process creation
- **Memory Efficiency**: No duplication of code and data segments
- **Lower Overhead**: Reduced context switching costs

#### D. Scalability

- **Multi-processor Utilization**: Threads can run on different processors
- **Parallel Processing**: True parallelism on multi-core systems
- **Performance Improvement**: Better CPU utilization

### 6. Thread Programming Considerations

#### Shared Data Issues

- **Global Variable Access**: All threads can read/write global variables
- **Data Consistency**: Changes by one thread visible to all others
- **Race Conditions**: Potential for data corruption with concurrent access

#### Synchronization Challenges

- **No Execution Order**: Cannot predict which thread runs first
- **Data Dependencies**: Must synchronize when threads depend on each other's results
- **Critical Sections**: Need mechanisms to protect shared resources

#### Programming Model

- **Function-based**: Each thread starts with a function
- **Parameter Passing**: Entry function can receive parameters
- **Return Values**: Thread functions can return values
- **Lifecycle Management**: Need to handle thread creation, execution, and termination

## Key Concepts Summary

### Inter-Process Communication

1. **Sockets**: Network communication endpoints with IP:port identification
2. **RPC**: Remote function calls with transparent networking
3. **RMI**: Java-based remote method invocation

### Threading Fundamentals

1. **Thread**: Lightweight execution unit within a process
2. **Concurrency**: Multiple threads executing pseudo-parallel
3. **Resource Sharing**: Threads share code, data, and files
4. **Independence**: Each thread has own stack and CPU state

### Threading Benefits

1. **Responsiveness**: Non-blocking operation
2. **Resource Sharing**: Efficient data sharing
3. **Economy**: Low overhead creation and management
4. **Scalability**: Multi-processor utilization

### Programming Implications

1. **Shared Memory**: Global variables accessible to all threads
2. **Synchronization**: Need for coordination mechanisms
3. **Race Conditions**: Potential for data inconsistency
4. **Design Complexity**: More complex program logic required

---

**Next Topic**: Chapter 4 - Advanced Threading concepts, Thread Libraries (Pthreads, Win32, Java), and Threading Issues