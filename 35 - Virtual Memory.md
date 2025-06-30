
---

## 1. Introduction and Motivation

- **Definition**: Virtual memory is a technique that gives processes the illusion of a large, contiguous address space by decoupling logical (virtual) addresses from physical memory addresses ([en.wikipedia.org](https://en.wikipedia.org/wiki/Virtual_memory?utm_source=chatgpt.com)).
    
- **Benefits**:
    
    - Allows programs to be larger than physical RAM.
        
    - Enables multiprogramming by isolating process address spaces and providing memory protection ([en.wikipedia.org](https://en.wikipedia.org/wiki/Memory_management?utm_source=chatgpt.com)).
        
    - Simplifies programming by abstracting memory management.
        

---

## 2. Address Translation Mechanisms

### 2.1 Paging vs. Segmentation

- **Paging**: Divides virtual and physical memory into fixed-size blocks (pages and frames). Page tables map virtual page numbers to physical frame numbers ([en.wikipedia.org](https://en.wikipedia.org/wiki/Page_%28computer_memory%29?utm_source=chatgpt.com)).
    
- **Segmentation**: Divides memory into variable-sized segments each representing logical units such as code, data, or stack ([en.wikipedia.org](https://en.wikipedia.org/wiki/Memory_segmentation?utm_source=chatgpt.com)).
    

### 2.2 Page Table

- A **page table** holds the mapping from virtual pages to physical frames. Each entry includes:
    
    - Frame number
        
    - Control bits: valid/invalid, protection flags, dirty bit, reference bit ([en.wikipedia.org](https://en.wikipedia.org/wiki/Page_table?utm_source=chatgpt.com)).
        

### 2.3 Translation Lookaside Buffer (TLB)

- A **TLB** is a small, fast cache that stores recent page-table entries to accelerate address translation.
    
- **TLB Hit/Miss**: On a hit, translation is immediate; on a miss, the page table is consulted, and the entry is loaded into the TLB ([en.wikipedia.org](https://en.wikipedia.org/wiki/Translation_lookaside_buffer?utm_source=chatgpt.com)).
    

---

## 3. Demand Paging

- **Concept**: Load pages into memory only when they are referenced (lazy loading), reducing I/O overhead ([en.wikipedia.org](https://en.wikipedia.org/wiki/Demand_paging?utm_source=chatgpt.com)).
    
- **Page Fault Handling**:
    
    1. Process issues memory access.
        
    2. MMU checks valid/invalid bit; if invalid, a page fault occurs.
        
    3. OS checks if access is legal; if not, terminate process.
        
    4. Choose a free frame (or victim page) and schedule a disk read.
        
    5. Update page table and TLB, then resume the faulting instruction.
        

### 3.1 Performance: Effective Access Time (EAT)

EAT=(1−p)×memory time+p×(page fault overhead)\text{EAT} = (1-p) \times \text{memory time} + p \times (\text{page fault overhead})

- Where pp is the page-fault rate.
    

---

## 4. Page Replacement Algorithms

- **Goal**: Minimize the number of page faults by selecting the best victim page to replace.
    

|Algorithm|Description|Optimal?|
|---|---|---|
|FIFO|Replace the oldest loaded page.|No|
|Optimal|Replace page not used for longest time in future. (Theoretic ideal)|Yes|
|LRU|Replace the page not used for longest time in past.|No|
|Clock|Approximation of LRU using a circular buffer and reference bits.|No|

- **Belady’s Anomaly**: In FIFO, increasing frames can sometimes increase faults.
    

---

## 5. Allocation of Frames

- **Fixed Allocation**: Equal or proportional distribution of physical frames among processes.
    
- **Priority Allocation**: Processes assigned frames based on priority.
    
- **Working Set Model**: Allocates frames based on the process’s working set — the set of pages referenced during a recent time window τ\tau ([en.wikipedia.org](https://en.wikipedia.org/wiki/Working_set?utm_source=chatgpt.com)).
    

---

## 6. Thrashing

- **Definition**: Occurs when processes spend more time paging than executing, due to insufficient frames for working sets. System throughput degrades significantly ([en.wikipedia.org](https://en.wikipedia.org/wiki/Thrashing_%28computer_science%29?utm_source=chatgpt.com)).
    
- **Detection**: High page-fault rate, low CPU utilization.
    
- **Mitigation**:
    
    - Reduce degree of multiprogramming.
        
    - Increase physical memory.
        
    - Use working-set-based allocation to prevent thrashing.
        

---

## 7. Copy-on-Write (COW)

- **Technique**: Allows parent and child processes to share physical pages initially after a fork; pages are duplicated only upon modification, reducing overhead of process creation ([scribd.com](https://www.scribd.com/document/590208803/Chapter-10-Virtual-Memory?utm_source=chatgpt.com)).
    

---

## 8. Memory-Mapped Files & Shared Memory

- **Memory-Mapped Files**: Map disk files into a process’s address space; file I/O can be treated as ordinary memory access, with paging handling disk transfers.
    
- **Shared Memory**: Multiple processes map the same pages to enable fast interprocess communication ([slideserve.com](https://www.slideserve.com/keiththomas/lecture-3-part2-virtual-memory-powerpoint-ppt-presentation?utm_source=chatgpt.com)).
    

---

## 9. Kernel Memory Management (Brief)

- Kernel uses its own memory allocator for data structures, often employing slab or buddy allocators.
    
- Requires careful management to avoid paging in critical OS code paths.
    

---

## 10. Summary

Virtual memory is a cornerstone of modern operating systems, providing abstraction, protection, and efficient use of limited physical memory. Key mechanisms such as paging, demand paging, page replacement, and working sets ensure processes can run transparently and efficiently even under heavy multiprogramming environments.

---

_End of Lecture 35 Notes_