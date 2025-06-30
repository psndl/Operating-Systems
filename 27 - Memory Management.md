
## Chapter Overview

- **Chapter 8**: Basic memory management strategies and foundations
- **Chapter 9**: Advanced memory management techniques (Virtual Memory)
- This lecture covers fundamental concepts that enable virtual memory implementation

## Memory Management Basics

### System Architecture

- **Operating System**: Memory-resident program, always loaded in memory
- **Programs**: Stored as executable files on disk, loaded into memory when executed
- **System Components**:
    - Hard disk (program storage)
    - Main memory (program execution)
    - CPU with cache
    - Cache sits between CPU and main memory

### Program Execution Process

1. **Loading**: Program loaded from disk to memory
2. **Memory Image**: Instructions and variables placed in memory
3. **Execution**:
    - Instructions fetched from memory to CPU
    - Data flows between memory and CPU
    - Results stored back to memory

### Memory Access Performance

- **Register Access**: Very fast (1 clock cycle)
- **Cache Access**: Fast (2-3 clock cycles)
- **Main Memory Access**: Slow (10-15 cycles)
- **Cache Purpose**: Store frequently used instructions and data for faster access

## Address Types and Binding

### Physical vs Logical Addresses

#### Physical Addresses (Early Systems)

- **Definition**: Actual hardware memory addresses
- **Problem**: Programs must be loaded at specific memory locations
- **Limitation**: Not relocatable - cannot move program to different memory location
- **Multi-programming Issue**: Cannot run multiple programs concurrently

**Example Problem with Physical Addresses:**

```
Program 1: Instructions at addresses 0, 8, 12
Program 2: Instructions at addresses 0, 8, 12
```

- Both programs cannot load simultaneously
- Jump instructions reference wrong locations when programs loaded at different positions

#### Logical Addresses (Modern Systems)

- **Definition**: Addresses used by programmer/compiler, independent of physical memory
- **Advantage**: Programs are relocatable
- **Mapping**: Logical addresses converted to physical addresses at runtime
- **Address Space**: Range of logical addresses used by a program (e.g., 0 to MAX)

### Address Binding Methods

#### 1. Compile-Time Binding

- **Process**: Physical addresses generated during compilation
- **Requirement**: Must know exact memory location where program will load
- **Result**: Absolute code with physical addresses
- **Limitation**: Requires recompilation if program moved to different location

#### 2. Load-Time Binding

- **Process**: Logical addresses converted to physical addresses when program loaded
- **Requirement**: Program must use relocatable code
- **Flexibility**: Program can be loaded at any memory location
- **When**: Address translation occurs once at load time

#### 3. Execution-Time Binding (Runtime Binding)

- **Process**: Address translation occurs during program execution
- **Flexibility**: Process can be moved during execution
- **Implementation**: Uses base and limit registers
- **Advantage**: Maximum flexibility for memory management

## Base and Limit Registers

### Implementation

- **Base Register**: Contains starting physical address of process
- **Limit Register**: Contains size of process
- **Address Translation**: Physical Address = Logical Address + Base Register
- **Protection**: Logical addresses must be < Limit Register value

### Context Switching

- **Process Switch**: Base and limit registers updated with new process values
- **Storage**: Register values stored in Process Control Block (PCB)
- **Loading**: Values loaded from PCB when process becomes active

## Program Development Process

### Compilation Steps

1. **Source Files**: Multiple C files (.c)
2. **Compilation**: Each C file → Object file (.o)
3. **Linking**: Multiple object files → Single executable
4. **Loading**: Executable loaded into memory by OS loader

### Linking Types

#### Static Linking

- Library code included in executable at compile time
- Larger executable file size
- No external dependencies at runtime

#### Dynamic Linking

- **Shared Libraries**: Library code loaded at program start time
- **Advantages**:
    - Smaller executables
    - Multiple programs share single copy of library
    - Memory efficiency

#### Dynamic Loading

- Library functions loaded when first called
- On-demand loading reduces initial memory requirements

## Memory Management Unit (MMU)

### Function

- **Purpose**: Converts logical addresses to physical addresses
- **Location**: Hardware component between CPU and memory
- **Process**:
    1. CPU generates logical address
    2. MMU translates to physical address
    3. Physical address sent to main memory
    4. Memory returns data/instruction

### Address Translation Process

```
CPU → Logical Address → MMU → Physical Address → Main Memory
```

## Key Concepts Summary

### Logical Address Space Benefits

- **Multi-programming**: Multiple programs can run concurrently
- **Relocation**: Programs can be moved in memory
- **Protection**: Processes isolated from each other
- **Flexibility**: Dynamic memory allocation possible

### Memory Protection

- **Bounds Checking**: Logical addresses validated against limit register
- **Invalid Access**: Addresses ≥ limit register cause trap/error
- **Process Isolation**: Each process has separate address space

### Foundation for Virtual Memory

- Logical address space concept enables virtual memory
- Base/limit registers are building blocks for more sophisticated paging systems
- Address translation mechanisms support advanced memory management

## Important Notes

- This chapter provides foundations for Chapter 9 (Virtual Memory)
- Hardware and OS work together for memory management
- Paging concepts build upon these basic address translation mechanisms
- Understanding logical vs physical addresses is crucial for advanced topics

---

**Next Topic**: Advanced memory management techniques and virtual memory implementation using paging mechanisms.