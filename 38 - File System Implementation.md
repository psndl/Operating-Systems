
## 1. On-Disk Data Structures for File Systems

File systems require persistent data structures that survive power outages. These structures are stored on disk to maintain file information permanently.

### Major On-Disk Structures:

1. **Boot Control Block**
    
    - Contains information needed to boot the operating system from that partition
    - Partition-specific boot information
2. **Volume Control Block**
    
    - Keeps information about the file system in that partition
    - Contains partition metadata (number of blocks, directory locations)
    - Information about where the directory structure is maintained
3. **Directory Information**
    
    - Stored somewhere on the partition
    - Contains file names and pointers to file control blocks
4. **File Control Blocks (FCBs)**
    
    - One FCB per file
    - Contains file attributes, permissions, dates (creation, access, modification)
    - File size information
    - Location information for file data blocks on disk

## 2. Relationship Between File System Structures

### Directory Structure Organization:

- Directory contains entries (one per file)
- Each entry includes:
    - File name
    - Pointer/address to the file's FCB
    - Other basic file information

### File Access Process:

1. Search directory structure for file name
2. Find corresponding directory entry
3. Use pointer in directory entry to locate FCB
4. Access FCB to get detailed file information
5. Use FCB information to locate actual file data blocks

## 3. Memory Caching for Performance

### Initial State (No Files Open):

- All structures (directories, FCBs, file content) reside on disk

### When Files Are Opened:

1. **Directory Entry Caching**
    
    - Directory entries cached in memory (Directory Structure Table)
    - Avoids repeated disk searches for same files
    - Shared across processes
2. **FCB Caching**
    
    - FCBs cached in System-wide Open File Table
    - One table for entire system (not per process)
    - Contains file attributes and data block location information
3. **Per-Process Open File Table**
    
    - Maintains process-specific information
    - **File Position Pointer**: Current position in file for next operation
    - Initially set to 0 (beginning of file)
    - Advanced with each read/write operation

### File Descriptor System:

- `open()` system call returns integer file descriptor
- File descriptor is index into per-process open file table
- Used in subsequent operations (read, write, close)
- Kernel uses descriptor to locate file information in tables

## 4. Virtual File System (VFS)

### Problem:

Operating systems need to support multiple file system types simultaneously:

- Linux ext3 file system
- Windows NTFS
- CD-ROM file systems
- USB flash drive file systems
- Network/distributed file systems

### Solution: VFS Layer

VFS provides a unified interface that:

- **Upper Interface**: Standard system calls (open, read, write, close) for applications
- **Lower Interface**: Well-defined interface for concrete file systems

### Benefits:

1. **Single Interface**: Applications use same operations regardless of underlying file system
2. **Easy Integration**: New file systems can be added by implementing VFS interface
3. **Transparency**: Processes don't need to know specific file system details

### VFS Architecture:

```
Applications
     ↓
VFS Layer (open, read, write, close)
     ↓
Concrete File Systems (ext3, NTFS, NFS, etc.)
```

## 5. Directory Implementation

### Purpose of Directories:

Translate file names to corresponding FCBs or file data locations

### Implementation Methods:

#### Linear List Approach:

- **Structure**: Sequential list of entries
- **Entry Format**: File name + pointer to FCB
- **Advantages**: Simple to implement
- **Disadvantages**: Time-consuming for large directories (linear search required)

#### Hash Table Approach:

- **Structure**: Hash table for faster access
- **Process**: Hash file name → table entry → directory entry
- **Advantages**: Direct access, faster than linear search
- **Use Case**: Better for directories with many files

## 6. File Allocation Methods

The fundamental question: How to allocate disk blocks to files?

### 6.1 Contiguous Allocation

#### Concept:

- Allocate consecutive disk blocks to each file
- Similar to contiguous memory allocation for processes

#### Implementation:

- **FCB/Directory Entry Stores**:
    - Starting block address
    - Number of blocks allocated
- **Example**: File starts at block 6, occupies 4 blocks (blocks 6, 7, 8, 9)

#### Advantages:

1. **Fast Random Access**: Can immediately compute block number for any file position
2. **Efficient Sequential Access**: Blocks are consecutive
3. **Simple Address Calculation**: Easy logical-to-physical translation

#### Disadvantages:

1. **File Growth Problems**: Cannot grow if adjacent blocks are occupied
2. **External Fragmentation**: Creates unusable gaps between allocated spaces

#### Address Translation Algorithm:

```
Given: Logical address (offset) in file
Block size = 1024 bytes
File starts at disk block = start_block

Relative block number = offset / block_size
Physical block number = start_block + relative_block_number
Displacement in block = offset % block_size
```

#### Example Calculation:

- File starts at block 6, block size = 1024 bytes
- Access offset 2500:
    - Relative block = 2500 / 1024 = 2
    - Physical block = 6 + 2 = 8
    - Displacement = 2500 % 1024 = 452
    - Result: Access block 8, byte 452

#### Solution for Growth: Extent-Bas