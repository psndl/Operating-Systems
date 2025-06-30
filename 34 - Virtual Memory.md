
## 🎥 Lecture Context

This lecture (CS‑342 Lecture 34, April 7, 2009) introduces **Virtual Memory** as a key operating-system concept ([web.stanford.edu](https://web.stanford.edu/class/archive/cs/cs111/cs111.1232/lectures/21/Lecture21.pdf?utm_source=chatgpt.com "[PDF] CS111, Lecture 21 - Virtual Memory Introduction"), [csapp.cs.cmu.edu](https://www.csapp.cs.cmu.edu/2e/ch9-preview.pdf?utm_source=chatgpt.com "[PDF] Virtual Memory - Computer Systems: A Programmer's Perspective")).

---

## 1. What Is Virtual Memory?

- A technique that gives processes the illusion of a large, contiguous memory space, even if physical RAM is limited ([geeksforgeeks.org](https://www.geeksforgeeks.org/operating-systems/virtual-memory-in-operating-system/?utm_source=chatgpt.com "Virtual Memory in Operating System - GeeksforGeeks")).
    
- Translates **virtual addresses** (used by processes) into **physical addresses** via the MMU.
    
- Benefits include supporting large processes, isolation, efficient multitasking, and demand loading ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    

---

## 2. Demand Paging

- Only load pages into RAM when they’re accessed, not all at once ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- Pages have a **valid–invalid bit**:
    
    - **Invalid → page fault**, triggering loading from disk ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory"), [en.wikipedia.org](https://en.wikipedia.org/wiki/Demand_paging?utm_source=chatgpt.com "Demand paging")).
        
- Handling a page fault: trap → check address validity → find free frame or evict → load page → restart instruction ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    

---

## 3. Performance & Effective Access Time (EAT)

- EAT = (1–p) × (mem access time) + p × (page-fault overhead + swap times) ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- Even low page-fault rates (e.g., 1/1000) can cause massive slowdowns.
    
- Goal: minimize **p** to ensure high performance ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    

---

## 4. Page Replacement Algorithms

When no free frame is available, the OS must evict a page:

- **Optimal (clairvoyant)**: evict the page used farthest in the future (ideal but impossible) ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- **FIFO**: evict the oldest page; simple, but suffers from _Belady’s Anomaly_ (faults may _increase_ with more frames) ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- **LRU**: evict least recently used; practical but requires tracking/tagging mechanisms .
    
- **Clock (Second-Chance)**: circular buffer using a reference bit to approximate LRU ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- **Enhanced Clock**: considers both reference and dirty bits to refine eviction choice ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    

---

## 5. Frame Allocation Strategies

Determines how many frames each process gets:

- **Fixed allocation**: equal or proportional share ([cs.uic.edu](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/9_VirtualMemory.html?utm_source=chatgpt.com "Operating Systems: Virtual Memory"), [cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- **Priority allocation**: high-priority processes get more frames ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- **Global vs. Local replacement**:
    
    - _Global_: frames can be stolen across processes (maximizes utilization).
        
    - _Local_: each process only manages its own frames (better fairness) ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
        

---

## 6. Thrashing

- Occurs when the working set exceeds available frames → excessive page faults and CPU underutilization ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- To prevent it, OS tracks the **working set** of each process and balances frame allocation based on **page-fault frequency (PFF)** ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    

---

## 7. Address Translation & Data Structures

- **Page tables** map virtual pages to physical frames with control bits (valid, dirty, ref) ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- Multi-level tables and TLBs optimize translation speed.
    
- **Page size** impacts performance: larger pages reduce table size but increase internal fragmentation .
    

---

## 8. OS Examples

- **Windows** uses demand paging with working-set limits, clustering prefetch, and auto-trimming ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    
- **Solaris** employs threshold-based free-page scanning, enhanced clock algorithm, and priority paging ([cs.hunter.cuny.edu](https://www.cs.hunter.cuny.edu/~sweiss/course_materials/csci340/slides/chapter10.pdf?utm_source=chatgpt.com "[PDF] Chapter 10: Virtual Memory")).
    

---

## ✔️ Summary

Virtual memory is essential for modern OS: it allows efficient memory use, process isolation, and multitasking. Through demand paging, smart page-replacement, and balanced frame allocation, the OS ensures performance while managing limited physical RAM.

---

Need deeper dives into specific algorithms, data structures, or OS implementations? Just let me know!