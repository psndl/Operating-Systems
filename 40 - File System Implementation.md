## Overview

This lecture covers file system implementation details, focusing on free space management, performance optimization, and recovery mechanisms.

---

## 1. Free Space Management

### Problem Statement

- When allocating new blocks to growing files, we need to find free blocks on disk
- Must keep track of which disk blocks are available for allocation
- Essential for dynamic file allocation and system efficiency

### Methods for Free Space Management

#### 1.1 Bit Vector (Bitmap) Method

**Concept:**

- One bit per disk block indicating free (1) or used (0) status
- Simple binary representation of disk block availability

**Implementation:**

- If disk has n blocks → bitmap has n bits
- Bit value conventions:
    - 1 = free block
    - 0 = used block (or vice versa)

**Example:**

```
Disk with 12 blocks:
Bitmap: [0,1,0,0,1,1,0,1,0,1,1,0]
        Block 0 is used, Block 1 is free, etc.
```

**Searching for Free Blocks:**

- Efficient word-by-word search
- Skip zero-valued words (all blocks used)
- Find first non-zero word, then locate first '1' bit
- Block number = (words_skipped × bits_per_word) + bit_offset

**Storage Requirements:**

- For 1GB disk with 4KB blocks:
    - Total blocks = 2³⁰ / 2¹² = 2¹⁸ blocks
    - Bitmap size = 2¹⁸ bits = 32 KB

**Advantages:**

- Easy to find contiguous free blocks
- Simple implementation
- Good for sequential allocation

**Disadvantages:**

- Requires extra disk space for bitmap storage
- Space overhead increases with disk size

#### 1.2 Linked List Method

**Concept:**

- Link all free blocks together using pointers
- Each free block contains pointer to next free block
- Super block stores pointer to first free block

**Implementation:**

```
Super Block → Block 4 → Block 7 → Block 9 → Block 10 → NULL
```

**Allocation Process:**

1. Access first free block via super block pointer
2. Allocate that block to file
3. Update super block pointer to next free block

**Advantages:**

- No wasted space (pointers stored in free blocks)
- No extra storage overhead

**Disadvantages:**

- Difficult to find contiguous blocks
- Each pointer traversal requires disk access (costly)
- Not efficient for contiguous allocation

#### 1.3 Grouping Method

**Concept:**

- Store multiple free block addresses in a single block
- Link blocks containing free block addresses together

**Implementation:**

- Block contains array of free block addresses
- One address points to next "address block"
- Remaining addresses point to actual free blocks

**Example:**

```
Address Block 17: [82, 107, next_block_ptr]
Address Block 25: [53, 251, 215, next_block_ptr]
```

**Advantages:**

- Fewer disk accesses than simple linked list
- Can store many addresses per block (e.g., 1023 addresses in 4KB block)
- More efficient than simple linking

#### 1.4 Counting Method

**Concept:**

- Combine free block pointer with count of contiguous free blocks
- Store (starting_address, count) pairs

**Implementation:**

```
Entry 1: (Block 3, Count 2) → Blocks 3,4 are free
Entry 2: (Block 7, Count 3) → Blocks 7,8,9 are free
Entry 3: (Block 11, Count 1) → Block 11 is free
```

**Advantages:**

- Efficient for contiguous block allocation
- Reduces number of pointers needed
- Good for systems with clustering

---

## 2. File Allocation Table (FAT) Integration

### Free Space in FAT

- FAT entries that don't contain valid block numbers indicate free blocks
- No separate free space management structure needed
- FAT serves dual purpose: file allocation + free space management

**Advantages:**

- Single structure for both purposes
- No additional storage overhead
- Simplified implementation

---

## 3. Performance and Efficiency Optimization

### 3.1 Factors Affecting Performance

- Disk allocation algorithms efficiency
- Directory structure and search algorithms
- Disk access patterns and seek times
- Cache utilization strategies

### 3.2 Disk Cache (Buffer Cache)

**Purpose:**

- Store frequently accessed disk blocks in main memory
- Reduce disk I/O operations (20ms → nanoseconds)

**Implementation:**

- Portion of main memory dedicated to caching disk blocks
- Use LRU (Least Recently Used) replacement policy
- Cache hit provides immediate access, cache miss requires disk I/O

**Benefits:**

- Dramatic performance improvement for repeated access
- Reduced disk wear and power consumption

### 3.3 Read-Ahead Optimization

**Concept:**

- Predict future block accesses during sequential file reading
- Fetch multiple blocks before they're actually needed

**Implementation:**

- When reading block N, also fetch blocks N+1, N+2, etc.
- Particularly effective for sequential file access patterns

**Benefits:**

- Overlaps I/O with computation
- Reduces total access time for sequential operations

---

## 4. Advanced Caching: Page Cache vs Buffer Cache

### 4.1 Page Cache

- Caches pages for memory-mapped files
- Used when files are mapped into process virtual address space
- Accessed through pointer operations and memory references

### 4.2 Buffer Cache

- Caches disk blocks for traditional file I/O
- Used with read/write system calls
- Direct file system interaction

### 4.3 Unified Buffer Cache

**Problem:** Redundant caching when same file accessed via both methods

**Solution:** Single unified cache for both:

- Memory-mapped I/O and traditional I/O use same cache
- Eliminates data duplication
- More efficient memory utilization
- Consistent view of file data

---

## 5. System Reliability and Recovery

### 5.1 Consistency Issues

**Causes of Inconsistency:**

- Power failures during cache write-back
- System crashes before metadata updates
- Incomplete file operations

**Types of Inconsistencies:**

- Directory entries pointing to wrong inodes
- Missing directory entries for existing files
- Free block list corruption
- Inode reference count errors

### 5.2 Consistency Checking

**Tools:** fsck (file system check) utilities

**Process:**

1. Compare inodes with directory entries
2. Verify free block list accuracy
3. Check inode reference counts
4. Validate directory structure integrity
5. Attempt automatic repair of detected inconsistencies

**Execution:** Typically run during system boot after improper shutdown

### 5.3 Backup and Recovery

**Backup Strategies:**

- Regular data backup to secondary storage (tapes, external disks)
- Incremental vs. full backups
- Offsite storage for disaster recovery

**Recovery Process:**

1. Detect disk failure or corruption
2. Restore data from most recent backup
3. Apply incremental changes if available
4. Verify system integrity

---

## 6. Key Performance Considerations

### Space Efficiency

- Choice of allocation method affects storage overhead
- Bitmap vs. linked list vs. grouping trade-offs
- Block size optimization for different file sizes

### Time Efficiency

- Cache hit ratios critical for performance
- Sequential vs. random access patterns
- Minimizing disk seek operations

### Reliability

- Consistency checking mechanisms
- Backup strategies and recovery procedures
- Fault tolerance in file system design

---

## Summary

This lecture covered the essential components of file system implementation beyond basic file allocation:

1. **Free Space Management** - Multiple methods (bitmap, linked list, grouping, counting) each with distinct trade-offs
2. **Performance Optimization** - Caching strategies, read-ahead, and unified cache systems
3. **System Reliability** - Consistency checking and recovery mechanisms

The choice of implementation depends on specific system requirements, balancing performance, reliability, and storage efficiency. Modern file systems often combine multiple techniques to optimize for different usage patterns and failure scenarios.