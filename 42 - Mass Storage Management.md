

**Lecture Context**: Introduction from Bilkent University CS-342 Operating Systems, Lecture 42 (April 28, 2009), covering storage-device organization, management policies, and performance optimization techniques.

---

### 1. Objectives of Mass Storage Management

- Understand the hierarchy of storage: registers, cache, main memory, secondary storage, tertiary storage.
    
- Explore disk structure and performance factors.
    
- Learn disk-scheduling algorithms to minimize seek and latency.
    
- Examine redundancy and reliability: RAID levels.
    
- Study disk attachment, partitioning, and file-system formatting.
    
- Discuss buffering, caching, and virtualization of disk resources.
    

---

### 2. Storage Hierarchy and Characteristics

|Level|Access Time|Capacity|Volatility|
|---|---|---|---|
|Registers|~1 ns|few bytes|Volatile|
|Cache|~10 ns|few KB–MB|Volatile|
|Main memory|~100 ns|GBs|Volatile|
|Secondary (Disk)|~~milliseconds|TBs|Nonvolatile|
|Tertiary (Tape)|~~seconds|PBs|Nonvolatile|

- **Key insight**: Cost per bit increases down the hierarchy; speed decreases.
    

---

### 3. Magnetic Disk Structure

- **Platters, Tracks, Sectors**: A disk comprises multiple platters; each platter surface is divided into concentric tracks, each track into fixed-size sectors.
    
- **Cylinders**: Set of tracks aligned across platters at one arm position.
    
- **Disk Access Time**:
    
    - **Seek Time**: Time to position head to target track (average ~4–8 ms).
        
    - **Rotational Latency**: Wait for sector under head (~½ rotational period; e.g., 4 ms at 7200 RPM).
        
    - **Transfer Time**: Time to read/write data; proportional to number of sectors.
        

**Total Access Time** ≈ Seek Time + Rotational Latency + Transfer Time

---

### 4. Disk-Scheduling Algorithms

Aim to minimize average seek time; handle multiple requests efficiently.

1. **First-Come, First-Served (FCFS)**: Simple but can lead to long seeks.
    
2. **Shortest Seek Time First (SSTF)**: Chooses request with minimal seek from current head position; risk of starvation.
    
3. **SCAN (Elevator)**: Head moves in one direction, servicing all requests, then reverses. Provides more uniform wait time.
    
4. **C-SCAN (Circular SCAN)**: Like SCAN but only services in one direction; jumps back to start without servicing on return.
    

**Comparison**:

- SCAN and C-SCAN reduce variance in response time.
    
- SSTF can starve distant requests.
    

---

### 5. Disk Management: Partitioning and Formatting

- **Partitioning**: Divide a physical disk into logical regions; each can host a different file system or OS.
    
- **Logical Block Addressing (LBA)**: Abstracts away cylinder/head/sector; sequential block numbers.
    
- **Formatting**:
    
    - **Low-level format**: Defines sector markers, sync fields; usually done by manufacturer.
        
    - **High-level format**: Creates file-system structures (boot sector, FAT/inode tables, free-space maps).
        

---

### 6. RAID: Redundant Arrays of Inexpensive Disks

Improve reliability and performance by striping and mirroring.

|RAID Level|Description|Reliability|Performance|
|---|---|---|---|
|0|Striping (no redundancy)|Low|High throughput|
|1|Mirroring|High|Read optimized|
|4|Single parity disk (block-level striping + parity)|Moderate|Good read/write|
|5|Distributed parity (block-level)|Moderate|Balanced I/O|
|6|Dual distributed parity|High|Read optimum|

- **Parity**: P = D1 ⊕ D2 ⊕ … ⊕ Dn; can reconstruct one failed disk.
    

---

### 7. Disk Attachment and Interfaces

- **Controller Logic**: Manages low-level operations, DMA transfers.
    
- **Interfaces**: IDE/ATA, SCSI, SATA, Fibre Channel, NVMe.
    
- **Bus Characteristics**: Bandwidth, command queueing, CPU overhead.
    

---

### 8. Caching and Buffering

- **Buffering**: Use main-memory buffers to smooth differences between device and CPU speeds.
    
- **Caching**: Keep frequently accessed disk blocks in memory (e.g., page cache, buffer cache).
    
- **Write policies**:
    
    - **Write-through**: Write to cache and disk synchronously; safe but slow.
        
    - **Write-back**: Write to cache, defer disk write; faster but needs recovery mechanisms.
        

---

### 9. Virtual Disks and Storage Virtualization

- **Logical Volume Managers**: Abstract physical disks into volumes; allow resizing, snapshots.
    
- **Network Storage**: NAS (file-level), SAN (block-level).
    

---

### 10. Summary and Best Practices

- Understand storage hierarchy to optimize placement of data.
    
- Choose appropriate disk-scheduling algorithm based on workload.
    
- Use RAID level matching reliability and performance requirements.
    
- Leverage caching and buffering to improve I/O throughput.
    
- Adopt virtualization for flexibility in storage management.
    

---

**Further Reading:**

- Silberschatz, Galvin, and Gagne, _Operating System Concepts_, Chapter on Storage Management.
    
- Tanenbaum and Woodhull, _Modern Operating Systems_, Section on Disk Management.
    

---

_End of notes._