

### 1. **Overview**

- Focuses on **processes** and the mechanisms they use to interact and synchronize.
    
- Highlights **shared memory** as a key IPC method compared to others like signals, message passing, and pipes.
    

---

### 2. **Processes Recap**

- A **process** is an executing program with its own memory and system resources.
    
- Multiple processes may run concurrently, each maintaining **private address space** unless IPC is used.
    

---

### 3. **Motivation for IPC**

- Complex systems require processes to **collaborate**, sharing data and tasks.
    
- Challenges:
    
    - **Isolation** protects processes but limits cooperation.
        
    - IPC bridges this gap, enabling efficient communication and resource sharing.
        

---

### 4. **IPC Mechanisms**

- IPC methods vary in overhead, complexity, and use cases:
    
    - **Signals**: Lightweight notifications.
        
    - **Pipes/FIFOs**: Streamed byte communication.
        
    - **Message Passing**: Structured communication via OS.
        
    - **Sockets**: Network-aware communication.
        
    - **Shared Memory**: Fastest, but requires **explicit synchronization**.
        

---

### 5. **Shared Memory Introduction**

- Processor mapping: A memory region is mapped into each participating process’s address space.
    
- **Key advantages**:
    
    - Minimal copying—fastest IPC.
        
    - Processes directly read/write to a common region.
        
- **Drawbacks**:
    
    - No OS-level synchronization.
        
    - Must manage consistency manually (e.g. race conditions).
        

---

### 6. **Setting Up Shared Memory (C/POSIX)**

Typical steps (in Linux/POSIX):

1. **Create/open segment**: `shm_open(name, O_CREAT|O_RDWR, mode)`
    
2. **Set size**: `ftruncate()`
    
3. **Map into memory**: `mmap(NULL, size, PROT_READ|PROT_WRITE, MAP_SHARED, fd, 0)`
    
4. **Access**: Use pointers to read/write shared variables.
    
5. **Cleanup**:
    
    - `munmap()`
        
    - `close(fd)`
        
    - `shm_unlink(name)`
        

---

### 7. **Synchronization Essentials**

Even with shared memory, **race conditions** can occur:

- If multiple processes read/write concurrently, results are unpredictable.
    
- You must use synchronization primitives:
    
    - **Semaphores** (`sem_init`, `sem_wait`, `sem_post`)
        
    - **Mutexes** (Pthreads, possibly in shared memory)
        
    - **Barriers** for more complex multi-step coordination.
        

---

### 8. **Example Workflow**

```c
// Producer-consumer model using shared memory + semaphore
struct shm_data {
    int buffer[10];
    sem_t empty, full, mutex;
};

// Setup done by one process:
// shm_fd = shm_open(...);
// ftruncate(...)
// ptr = mmap(...);
// sem_init(&ptr->empty, 1, 10);
// sem_init(&ptr->full, 1, 0);
// sem_init(&ptr->mutex, 1, 1);
//
// Producer:
// sem_wait(&ptr->empty);
// sem_wait(&ptr->mutex);
// ptr->buffer[in] = data; in=(in+1)%10;
// sem_post(&ptr->mutex);
// sem_post(&ptr->full);
//
// Consumer:
// sem_wait(&ptr->full);
// sem_wait(&ptr->mutex);
// data = ptr->buffer[out]; out=(out+1)%10;
// sem_post(&ptr->mutex);
// sem_post(&ptr->empty);
```

- This coordinates producers and consumers safely using shared memory and semaphores.
    

---

### 9. **Best Practices & Pitfalls**

- **Always pair mapping/unmapping** to avoid memory leaks.
    
- Check return values of system calls (`shm_open`, `mmap`, etc.).
    
- Unlink shared memory when no longer needed.
    
- Carefully choose and position synchronization primitives in shared memory.
    
- Document shared memory layout to ensure all processes agree on structure.
    

---

### 10. **Lecture Summary**

- Shared memory is the **fastest IPC** method due to direct access.
    
- But it lacks **built-in synchronization**, so developers must handle concurrency control.
    
- The combination of shared memory with semaphores or mutexes enables robust and efficient interprocess coordination.
    

---

### 🏁 Bottom Line

Use shared memory + explicit synchronization when performance matters and processes must exchange large data quickly—just be sure to **rigorously manage schemas and concurrency**.

---

### 👍 Want More?

- Let me know if you'd like:
    
    - Deep dives into **POSIX vs SysV shared memory**
        
    - **Semaphore types** and advanced mutex/intra-process usage
        
    - **Conditional variables**, barriers, or other synchronization models
        

Feel free to ask!