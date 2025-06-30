
### 1. Motivation and Overview

- **Definition:** Virtual memory is an illusion of a large, contiguous address space presented to each process, decoupling logical (virtual) addresses from physical RAM. It allows processes to execute even if their entire address space is not resident in main memory simultaneously ([geeksforgeeks.org](https://www.geeksforgeeks.org/operating-systems/virtual-memory-in-operating-system/?utm_source=chatgpt.com "Virtual Memory in Operating System - GeeksforGeeks")).
    
- **Benefits:**
    
    - Enables multiprogramming by treating disk storage as an extension of RAM.
        
    - Simplifies programming: the OS and hardware handle loading pages/segments as needed, relieving programmers from manual overlay schemes ([home.adelphi.edu](https://home.adelphi.edu/~siegfried/cs553/553l8.pdf?utm_source=chatgpt.com "[PDF] CSC 553 Operating Systems - Lecture 8 - Virtual Memory")).
        
    - Supports processes larger than physical memory and isolates processes for reliability and security.
        

---

### 2. Real vs. Virtual Memory

||Real Memory (RAM)|Virtual Memory (Disk + RAM)|
|---|---|---|
|Location|Physical DRAM chips|Backing store on disk + RAM|
|Managed by|Hardware (MMU)|OS + Hardware (MMU)|
|Address space size|Limited (e.g., GBs)|Much larger (TBs or more)|
|Loading mechanism|Explicit load/swap|Demand paging/segmentation|

---

### 3. Address Translation and the MMU

- **Memory Management Unit (MMU):** Translates virtual addresses to physical addresses on every memory reference. Without translation, each memory access would require two lookups—one for page table and one for data—doubling memory access time ([en.wikipedia.org](https://en.wikipedia.org/wiki/Translation_lookaside_buffer?utm_source=chatgpt.com "Translation lookaside buffer")).
    
- **Page Table:** Data structure mapping virtual page numbers to physical frame numbers, including status bits (valid/invalid, dirty, reference).
    

---

### 4. Paging

- **Basic Concept:** Divides both virtual and physical memory into fixed‐size blocks (pages and frames). Virtual pages are loaded into any free physical frame when needed.
    
- **Demand Paging:** Pages are loaded only on a page fault (when a referenced page is not in RAM). Steps on a page fault:
    
    1. OS checks validity; if invalid, terminate process → segmentation fault.
        
    2. Select a free frame or invoke a page-replacement algorithm.
        
    3. If the victim frame is dirty, write it back to disk.
        
    4. Load the demanded page into the freed frame; update the page table.
        
    5. Restart the faulting instruction. ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory"))
        

---

### 5. Page Replacement Algorithms

- **FIFO (First-In, First-Out):** Replace the oldest loaded page. Simple but can suffer Belady’s anomaly.
    
- **Optimal (Belady’s Algorithm):** Replace the page that will not be used for the longest future time. Theoretical benchmark.
    
- **LRU (Least Recently Used):** Approximate by replacing the page that has not been used for the longest past time. Implements locality principle.
    
- **Clock (Second-Chance):** Circular list with reference bits; gives each page a “second chance” before replacement. ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory"))
    

---

### 6. Frame Allocation Strategies

- **Fixed Allocation:** Each process is allocated a fixed number of frames.
    
    - _Equal:_ Equal share for all processes.
        
    - _Proportional:_ Share proportional to process size.
        
- **Dynamic Allocation:** Number of frames can grow or shrink based on process behavior and overall memory pressure.
    
- **Local vs. Global Replacement:**
    
    - _Local:_ Replacement among pages of the faulting process only.
        
    - _Global:_ Any page in memory can be victim, possibly leading to a process losing all its frames.
        

---

### 7. Thrashing and the Working-Set Model

- **Thrashing:** A process spends more time servicing page faults than executing instructions. Often occurs when the sum of working sets of all processes exceeds physical memory ([cs.uic.edu](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/9_VirtualMemory.html?utm_source=chatgpt.com "Operating Systems: Virtual Memory")).
    
- **Working Set (𝑊𝑆):** The set of pages a process has referenced in the last Δ memory references (window size). Keeping the working set in memory minimizes page faults.
    
- **Load Control:** If total demanded frames > available frames → reduce multiprogramming or swap out processes to alleviate thrashing.
    

---

### 8. Segmentation and Segmentation with Paging

- **Segmentation:** Divides a program’s address space into variable‐length segments (e.g., code, data, stack), each with its own segment table entry (base, limit). Segments correspond to logical units; protection and sharing are segment‐based ([en.wikipedia.org](https://en.wikipedia.org/wiki/Memory_segmentation?utm_source=chatgpt.com "Memory segmentation")).
    
- **Segmentation with Paging:** Each segment is further divided into pages, combining benefits of both schemes: segments for logical isolation and paging for physical memory management.
    

---

### 9. Translation Lookaside Buffer (TLB)

- **Purpose:** High‐speed cache in the MMU that stores recent translations of virtual page numbers to physical frames, reducing two memory accesses to one when a hit occurs.
    
- **Structure:** Contains a fixed number of entries with (VPN → PFN) mappings and status bits. On a TLB miss, the page table in main memory is consulted, the entry is loaded into the TLB (possibly evicting another).
    
- **Performance Impact:** A well‐tuned TLB can reduce effective memory access time dramatically; TLB hit ratio is critical for performance ([en.wikipedia.org](https://en.wikipedia.org/wiki/Translation_lookaside_buffer?utm_source=chatgpt.com "Translation lookaside buffer")).
    

---

### 10. Inverted Page Tables (Optional)

- **Idea:** Instead of per‐process page tables, a single global hash table of (process, VPN) → PFN entries. Reduces memory overhead for systems with large virtual address spaces but increases lookup time.
    

---

**Key Takeaways**

- Virtual memory underpins modern OS multiprogramming, abstraction, and memory protection.
    
- Understanding demand paging, replacement algorithms, and working‐set dynamics is critical to tuning system performance.
    
- Hardware support (MMU, TLB) works in tandem with OS algorithms to make virtual memory transparent and efficient.
    

---

_End of Lecture Notes_