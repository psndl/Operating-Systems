## Table of Contents

1. [Memory Management with Base and Limit Registers](https://claude.ai/chat/111d09fe-1c00-4fe6-9d19-7684e1ccd5d1#memory-management-with-base-and-limit-registers)
2. [Dynamic Linking](https://claude.ai/chat/111d09fe-1c00-4fe6-9d19-7684e1ccd5d1#dynamic-linking)
3. [Swapping](https://claude.ai/chat/111d09fe-1c00-4fe6-9d19-7684e1ccd5d1#swapping)
4. [Contiguous Allocation](https://claude.ai/chat/111d09fe-1c00-4fe6-9d19-7684e1ccd5d1#contiguous-allocation)
5. [Memory Allocation Strategies](https://claude.ai/chat/111d09fe-1c00-4fe6-9d19-7684e1ccd5d1#memory-allocation-strategies)
6. [Fragmentation](https://claude.ai/chat/111d09fe-1c00-4fe6-9d19-7684e1ccd5d1#fragmentation)
7. [Paging](https://claude.ai/chat/111d09fe-1c00-4fe6-9d19-7684e1ccd5d1#paging)
8. [Address Translation in Paging](https://claude.ai/chat/111d09fe-1c00-4fe6-9d19-7684e1ccd5d1#address-translation-in-paging)

---

## Memory Management with Base and Limit Registers

### Basic Concept

- Programs use **logical addresses** (also called virtual addresses)
- Physical memory uses **physical addresses**
- **Memory Management Unit (MMU)** translates logical to physical addresses

### Hardware Components

- **Base Register (Relocation Register)**: Contains the starting physical address of the program
- **Limit Register**: Contains the size of the program's logical address space
- **CPU Registers**: General purpose, instruction register, PC register

### Example Execution

```
Program with logical addresses:
- Compare instruction at logical address 0
- Move R1, 28 (move value at logical address 28 to register R1)
- Move R2, 24 (move value at logical address 24 to register R2)
- Jump 16 (jump to logical address 16)

Physical memory loading:
- Program loaded at physical address 24
- Base register = 24
- Limit register = 32 (program size)
```

### Address Translation Process

1. CPU generates logical address (e.g., 28)
2. MMU adds base register value to logical address: 28 + 24 = 52
3. Physical address 52 is sent to memory
4. Memory returns value at physical address 52

### Key Points

- Program uses only logical addresses
- Translation happens at runtime (dynamic relocation)
- Every memory reference requires translation
- User program never sees physical addresses

---

## Dynamic Linking

### Concept

- Libraries are linked at program startup time, not compile time
- Executable contains **stubs** instead of actual library code
- Stubs are replaced with actual library addresses at runtime

### Process

1. Program calls library function
2. Stub locates the appropriate library routine in memory
3. Stub replaces itself with the address of the routine
4. Function executes

### Benefits

- Shared libraries (e.g., standard C library)
- Reduced executable size
- Memory efficiency through sharing

---

## Swapping

### Problem

- Memory becomes full with multiple programs
- New programs cannot be loaded
- Long-running programs occupy memory for extended periods

### Solution: Swapping

- **Swap Out**: Write process memory image to disk (backing store)
- **Swap In**: Load process back from disk to memory
- Allows other processes to use memory while original process is swapped out

### Process

```
Process P1 (needs 5 days to execute)
↓ Swap Out
Disk Storage
↓ Swap In (later)
Memory (continues execution)
```

### Requirements

- Fast disk for backing store
- Ability to save/restore complete process state
- Dynamic relocation support

---

## Contiguous Allocation

### Memory Layout

```
+------------------+
|   Operating      |
|   System         |
+------------------+
|   Process 1      |
+------------------+
|   Process 2      |
+------------------+
|   Process 3      |
+------------------+
|   Free Space     |
+------------------+
```

### Protection Mechanism

- Base register: smallest physical address for the process
- Limit register: range of logical addresses the process may reference
- Each logical address must be < limit register
- Physical address = logical address + base register

### Memory Management

- OS maintains list of allocated partitions
- OS maintains list of free partitions (holes)
- Processes terminate → create holes
- New processes → allocated to suitable holes

---

## Memory Allocation Strategies

### First Fit

- Select the **first hole** large enough to hold the process
- Fast allocation
- May leave large unused portions

### Best Fit

- Select the **smallest hole** large enough to hold the process
- Minimizes leftover hole size
- Requires searching entire hole list
- Generally better than worst fit

### Worst Fit

- Select the **largest hole** available
- Maximizes leftover hole size
- Generally performs poorly

### Performance

- **First Fit** and **Best Fit** are better than **Worst Fit** in terms of:
    - Speed of allocation
    - Storage utilization

---

## Fragmentation

### External Fragmentation

- **Definition**: Unused space external to allocated partitions
- **Cause**: Small holes scattered throughout memory
- **Problem**: Cannot allocate contiguous space even if total free space is sufficient

### Solution: Compaction

- Move allocated partitions toward one end of memory
- Combine small holes into one large hole
- **Requirements**:
    - Dynamic relocation support
    - Time-consuming process

### Internal Fragmentation

- **Definition**: Unused space internal to allocated partitions
- **Cause**: Allocated space larger than requested space
- **Example**: Process needs 100KB, gets 128KB → 28KB internal fragmentation

---

## Paging

### Motivation

- Eliminate external fragmentation
- Allow non-contiguous memory allocation
- Support virtual memory
- Enable programs larger than physical memory

### Basic Concepts

#### Physical Memory

- Divided into fixed-size blocks called **page frames** or **frames**
- Frame size must be power of 2 (e.g., 4KB = 2^12 bytes)
- All frames have equal size
- Frames are numbered: 0, 1, 2, 3, ...

#### Logical Memory

- Divided into fixed-size blocks called **pages**
- Page size = frame size
- Pages numbered: 0, 1, 2, 3, ...
- Contains program instructions and data

### Page Table

- **Purpose**: Maps page numbers to frame numbers
- **Structure**: Array of entries, one per page
- **Content**: Each entry contains corresponding frame number
- **Management**: Set up by operating system
- **Usage**: Used by hardware for address translation

### Example

```
Logical Memory (3 pages):    Physical Memory (8 frames):
+----------+                 +----------+----------+
| Page 0   |                 | Frame 0  |          |
+----------+                 +----------+----------+
| Page 1   |                 | Frame 1  | Page 2   |
+----------+                 +----------+----------+
| Page 2   |                 | Frame 2  | Page 3   |
+----------+                 +----------+----------+
                             | Frame 3  | Page 1   |
Page Table:                  +----------+----------+
Page 0 → Frame 5             | Frame 4  |          |
Page 1 → Frame 3             +----------+----------+
Page 2 → Frame 1             | Frame 5  | Page 0   |
                             +----------+----------+
                             | Frame 6  |          |
                             +----------+----------+
                             | Frame 7  |          |
                             +----------+----------+
```

---

## Address Translation in Paging

### Logical Address Structure

```
Logical Address (n bits)
+------------------+------------------+
|   Page Number    |   Page Offset    |
|   (n-m bits)     |   (m bits)       |
+------------------+------------------+
```

### Address Components

- **Page Number (p)**: Used as index into page table
- **Page Offset (d)**: Combined with frame number to get physical address
- **Page Size**: 2^m bytes (where m = number of offset bits)

### Translation Process

1. **Extract Components**: Divide logical address into page number and offset
2. **Page Table Lookup**: Use page number as index to get frame number
3. **Combine**: Concatenate frame number with offset to get physical address

### Detailed Example

```
Given:
- Page size = 4 bytes
- Logical memory = 16 bytes (4 pages)
- Physical memory = 32 bytes (8 frames)
- Logical address = 5

Step 1: Convert to binary
Logical address 5 = 0101 (binary)

Step 2: Extract components
Page number = 01 (upper 2 bits) = Page 1
Offset = 01 (lower 2 bits) = 1

Step 3: Page table lookup
Page 1 → Frame 6 (from page table)

Step 4: Create physical address
Frame 6 = 110 (binary, 3 bits needed for 8 frames)
Physical address = 110 + 01 = 11001 = 25 (decimal)
```

### Hardware Implementation

```
CPU → Logical Address
       ↓
   +----------+----------+
   |    p     |    d     |
   +----------+----------+
       ↓           ↓
   Page Table      |
       ↓           ↓
   Frame Number    |
       ↓           ↓
   +----------+----------+
   |    f     |    d     | → Physical Address → Memory
   +----------+----------+
```

### Key Points

- **Hardware Responsibility**: Address translation during execution
- **Software Responsibility**: Page table setup and management
- **Performance**: Must be fast (every memory reference requires translation)
- **No External Fragmentation**: Pages can be allocated to any available frames
- **Internal Fragmentation**: Only in the last page of each process

---

## Implementation Details

### Free Frame Management

- OS maintains list of free frames
- When process loads:
    1. Allocate free frames for each page
    2. Load pages into allocated frames
    3. Update page table with mappings
    4. Mark frames as used

### Process Loading Example

```
Before loading (free frames): 13, 14, 15, 18
Process has 4 pages (0, 1, 2, 3)

Allocation:
Page 0 → Frame 14
Page 1 → Frame 13  
Page 2 → Frame 18
Page 3 → Frame 15

After loading (free frames): (none)
Page table updated with mappings
```

### Advantages of Paging

1. **No External Fragmentation**: Any free frame can hold any page
2. **Non-contiguous Allocation**: Process pages can be scattered in memory
3. **Virtual Memory Support**: Foundation for advanced memory management
4. **Protection**: Each process has separate page table
5. **Sharing**: Multiple processes can share pages (with proper page table entries)

### Disadvantages of Paging

1. **Internal Fragmentation**: Last page may not be fully utilized
2. **Memory Overhead**: Page tables consume memory
3. **Translation Overhead**: Every memory access requires translation
4. **Complexity**: More complex than contiguous allocation

---

## Summary

This lecture covered fundamental memory management concepts:

1. **Basic Memory Management**: Using base and limit registers for dynamic relocation
2. **Swapping**: Moving processes between memory and disk to optimize memory usage
3. **Contiguous Allocation**: Traditional approach with external fragmentation issues
4. **Paging**: Modern solution eliminating external fragmentation and enabling virtual memory

The progression shows evolution from simple to sophisticated memory management techniques, with paging forming the foundation for virtual memory systems covered in subsequent lectures.