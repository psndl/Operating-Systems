
## 1. FIFO (First-In-First-Out) Page Replacement Algorithm

### Basic Concept

- **Principle**: Remove the page that was brought into memory first
- **Implementation**: Maintain pages in a queue structure
- **Rule**: First page loaded → First page removed (oldest page)

### FIFO Anomaly (Belady's Anomaly)

- **Expected Behavior**: More frames → Fewer page faults
- **Anomaly**: Sometimes more frames → More page faults for certain reference strings
- **Frequency**: Rare occurrence, but theoretically possible

### Problems with FIFO

- Only considers **when** a page was loaded, not **when** it was last accessed
- May remove heavily used pages that happen to be old
- Can remove recently referenced pages if they were loaded long ago

---

## 2. Optimal Page Replacement Algorithm

### Basic Concept

- **Principle**: Remove the page that will not be used for the longest time in the future
- **Goal**: Minimize total page faults (theoretically optimal)
- **Strategy**: Look into the future to make replacement decisions

### Example Execution

```
Reference String: 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5
Frames: 4

Step-by-step:
1 → [1] (page fault)
2 → [1,2] (page fault)  
3 → [1,2,3] (page fault)
4 → [1,2,3,4] (page fault)
1 → [1,2,3,4] (hit)
2 → [1,2,3,4] (hit)
5 → [1,2,3,5] (page fault, remove 4 - longest future distance)
```

### Limitations

- **Impractical**: Requires future knowledge of reference string
- **Theoretical Use**: Benchmark for comparing other algorithms
- **Cannot be implemented**: No way to predict future page references

---

## 3. LRU (Least Recently Used) Page Replacement Algorithm

### Basic Concept

- **Principle**: Remove the page that hasn't been used for the longest time
- **Strategy**: Look to the past instead of future
- **Assumption**: Recently used pages are likely to be used again soon

### Example Execution

```
Reference String: 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5
Frames: 4

1 → [1] (page fault)
2 → [1,2] (page fault)
3 → [1,2,3] (page fault)  
4 → [1,2,3,4] (page fault)
1 → [1,2,3,4] (hit, 1 becomes most recent)
2 → [1,2,3,4] (hit, 2 becomes most recent)
5 → [1,2,5,4] (page fault, remove 3 - least recently used)
```

---

## 4. LRU Implementation Methods

### 4.1 Counter Implementation

- **Mechanism**: Associate a counter with each page
- **Process**:
    1. Every page reference → update counter with current clock value
    2. Page fault occurs → select page with smallest counter value
- **Advantage**: Exact LRU implementation
- **Disadvantage**: Overhead of maintaining counters

### 4.2 Stack Implementation (Linked List)

- **Structure**: Doubly-linked list of pages in memory
    
- **Process**:
    
    1. Page reference → move page to head of list
    2. Page fault → remove page from tail of list
    3. Most recent page always at head, least recent at tail
- **Advantage**: Exact LRU implementation
    
- **Disadvantage**: High overhead (5-6 memory references per page reference)
    

### Cost Analysis

Both exact implementations are expensive:

- Counter method: Clock updates on every reference
- Stack method: Pointer manipulation on every reference
- **Solution**: Use approximation algorithms

---

## 5. LRU Approximation Algorithms

### 5.1 Reference Bit Algorithm (Crude Approximation)

#### Mechanism

- **Reference Bit**: 1 bit per page in page table
- **Setting**: Set to 1 when page is referenced
- **Clearing**: Periodic clock interrupt clears all reference bits

#### Operation

1. **Normal Operation**: Page references set reference bits to 1
2. **Clock Interrupt**: OS clears all reference bits to 0
3. **Page Fault**: Select a page with reference bit = 0 for removal

#### Limitations

- Very crude approximation
- Only knows if page was referenced in last clock interval
- No information about relative recency among unreferenced pages

### 5.2 Second Chance Algorithm

#### Basic Concept

- **Combination**: FIFO + Reference bits
- **Structure**: FIFO queue with reference bit checking
- **Principle**: Give recently used pages a "second chance"

#### Algorithm Steps

1. **Page Fault**: Start from head of FIFO queue
2. **Check Reference Bit**:
    - If 0: Remove this page
    - If 1: Set bit to 0, move page to tail, check next page
3. **Continue**: Until finding a page with reference bit = 0

#### Guarantees

- **Always terminates**: At worst, all pages get second chance and original head is selected
- **Degrades to FIFO**: When all pages have reference bit = 1

### 5.3 Clock Algorithm

#### Concept

- **Efficient Implementation**: Of Second Chance algorithm
- **Structure**: Circular linked list with a "hand" pointer
- **Advantage**: No actual movement of pages in data structure

#### Operation

1. **Circular List**: Pages arranged in circular linked list
2. **Hand Pointer**: Points to "oldest" page (head of FIFO queue)
3. **Page Fault**:
    - Check page at hand position
    - If reference bit = 0: Remove this page
    - If reference bit = 1: Set to 0, advance hand
4. **Continue**: Until finding page with reference bit = 0

#### Efficiency

- **No Data Movement**: Just advance pointer instead of moving pages
- **Same Logic**: As Second Chance but more efficient implementation

---

## 6. Counting Algorithms

### 6.1 LFU (Least Frequently Used)

- **Counter**: Track number of references per page
- **Selection**: Remove page with smallest counter
- **Logic**: Infrequently used pages are less likely to be needed

### 6.2 MFU (Most Frequently Used)

- **Counter**: Track number of references per page
- **Selection**: Remove page with largest counter
- **Logic**: Heavily used pages may not be needed anymore; new pages need time to be used

### Trade-offs

- **LFU Problem**: Old pages with initially high usage but recent inactivity
- **MFU Problem**: New pages haven't had chance to accumulate references
- **Both**: Expensive to maintain counters

---

## 7. Frame Allocation

### 7.1 Minimum Frame Allocation

#### Architecture Dependency

- **Instruction Complexity**: Determines minimum frames needed
- **Example**: Move instruction with memory operands
    - Instruction: May span 2 pages
    - Source operand: May span 2 pages
    - Destination operand: May span 2 pages
    - **Total**: Up to 6 pages needed simultaneously

#### Critical Requirement

- **All Required Pages**: Must be in memory simultaneously
- **Restart Problem**: Page fault during instruction execution requires restart
- **Infinite Loop**: Without sufficient frames, same instruction keeps faulting

### 7.2 Allocation Strategies

#### Equal Allocation

- **Method**: Divide total frames equally among all processes
- **Formula**: If M frames and N processes, each gets M/N frames
- **Problem**: Unfair to processes of different sizes

#### Proportional Allocation

- **Method**: Allocate frames based on process size
- **Formula**: Process i gets (Si/S) × M frames
    - Si = size of process i
    - S = total size of all processes
    - M = total frames
- **Example**:
    - 64 frames total
    - Process 1: size 10, Process 2: size 127
    - Process 1 gets: (10/137) × 64 ≈ 5 frames
    - Process 2 gets: (127/137) × 64 ≈ 59 frames

#### Priority-Based Allocation

- **Method**: Allocate frames based on process priority
- **Dynamic**: Higher priority processes can take frames from lower priority ones
- **Flexibility**: Adapts to changing system demands

---

## 8. Replacement Scope

### 8.1 Local Replacement

- **Scope**: Process selects replacement frame only from its own allocated frames
- **Advantage**: Predictable performance per process
- **Disadvantage**: Cannot adapt to changing memory needs

### 8.2 Global Replacement

- **Scope**: Process can select replacement frame from any frame in system
- **Dynamic**: Processes can "steal" frames from each other
- **Advantage**: Better overall system performance
- **Disadvantage**: Unpredictable individual process performance

---

## Key Takeaways

1. **Algorithm Trade-offs**: Exact algorithms (LRU implementations) vs. approximations (reference bit methods)
    
2. **Practical Considerations**:
    
    - Counter and stack LRU implementations are too expensive
    - Second Chance and Clock algorithms provide good approximations
    - Reference bit methods balance performance and overhead
3. **Frame Allocation**:
    
    - Minimum frames determined by architecture
    - Various allocation strategies for remaining frames
    - Local vs. global replacement affects system behavior
4. **Performance Factors**:
    
    - Algorithm choice affects page fault rate
    - Implementation overhead can impact overall system performance
    - Frame allocation strategy affects fairness and efficiency

---

## Next Topics

- Thrashing and working set model
- Page fault frequency algorithms
- Memory-mapped files
- Kernel memory allocation