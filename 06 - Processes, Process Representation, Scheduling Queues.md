
## 1. **What is a Process?**

- A **process** is a _program in execution_—not just code, but a runtime entity with state and resources. A process alternates between:
    
    - **CPU bursts** (executing instructions)
        
    - **I/O bursts** (waiting for input/output) ([video.bilkent.edu.tr](https://video.bilkent.edu.tr/supplementary_files/CS342_2008_2009_Spring/lecture12_mass_storage.pdf?utm_source=chatgpt.com "[PDF] Lecture 12 Mass Storage (chapter 12)"), [scribd.com](https://www.scribd.com/document/435775476/Operating-system?utm_source=chatgpt.com "Operating System | PDF | Scheduling (Computing) | Process ... - Scribd"))
        
- Processes may be **I/O‑bound** (many short CPU bursts) or **CPU‑bound** (few long bursts).
    

---

## 2. **Process State & Representation**

- A process moves among states: **New**, **Ready**, **Running**, **Waiting (Blocked)**, and **Terminated** ([scribd.com](https://www.scribd.com/document/435775476/Operating-system?utm_source=chatgpt.com "Operating System | PDF | Scheduling (Computing) | Process ... - Scribd")).
    
- Each process is represented by a **Process Control Block (PCB)** containing:
    
    - CPU registers & **Program Counter**
        
    - Process state, Priority, PCB pointers
        
    - Memory info: stack/data/text segment pointers
        
    - I/O & file-handle details, accounting data (e.g. CPU time used) ([scribd.com](https://www.scribd.com/document/435775476/Operating-system?utm_source=chatgpt.com "Operating System | PDF | Scheduling (Computing) | Process ... - Scribd"), [slideplayer.com](https://slideplayer.com/slide/5148461/?utm_source=chatgpt.com "CS 342 – Operating Systems Spring 2003 © Ibrahim Korpeoglu ..."))
        

---

## 3. **Context Switching & Dispatcher**

- When switching between processes:
    
    1. Save current registers and state into PCB of the outgoing process.
        
    2. Load registers/state from PCB of the incoming process.
        
    3. Switch to user mode and resume execution—this is handled by the **dispatcher** ([en.wikipedia.org](https://en.wikipedia.org/wiki/Scheduling_%28computing%29?utm_source=chatgpt.com "Scheduling (computing)"), [slideplayer.com](https://slideplayer.com/slide/5148461/?utm_source=chatgpt.com "CS 342 – Operating Systems Spring 2003 © Ibrahim Korpeoglu ...")).
        
- **Context switch overhead** can be significant (microseconds to milliseconds!) .
    

---

## 4. **Scheduling Queues**

- Multiple queues exist:
    
    - **Job (Process) queue**: All processes in the system.
        
    - **Ready queue**: Processes ready to execute.
        
    - **Device queues**: For each I/O device, holding processes waiting for service ([en.wikipedia.org](https://en.wikipedia.org/wiki/Multilevel_feedback_queue?utm_source=chatgpt.com "Multilevel feedback queue"), [slideplayer.com](https://slideplayer.com/slide/5148461/?utm_source=chatgpt.com "CS 342 – Operating Systems Spring 2003 © Ibrahim Korpeoglu ..."), [scribd.com](https://www.scribd.com/document/845962447/Operating-Systems-Summary?utm_source=chatgpt.com "Operating Systems Summary | PDF | Thread (Computing) - Scribd")).
        
- State transitions:
    
    - **Ready → Running** when scheduled
        
    - **Running → Waiting** on I/O
        
    - **Waiting → Ready** when I/O completes
        

---

## 5. **Schedulers: Types & Roles**

- **Long-term scheduler**: Decides which jobs enter the system (degree of multiprogramming), less frequent.
    
- **Medium‑term scheduler**: Handles swapping out/in for memory management, balancing load.
    
- **Short-term scheduler (CPU scheduler)**: Chooses next process from the ready queue; invoked frequently (e.g., every ~100 ms) ([slideserve.com](https://www.slideserve.com/parkerernest/chapter-5-process-scheduling-powerpoint-ppt-presentation?utm_source=chatgpt.com "Chapter 5 Process Scheduling - SlideServe"), [slideplayer.com](https://slideplayer.com/slide/5148461/?utm_source=chatgpt.com "CS 342 – Operating Systems Spring 2003 © Ibrahim Korpeoglu ...")).
    

---

## 6. **Common Scheduling Algorithms**

1. **First-Come, First-Served (FCFS)** – simple FIFO; minimal overhead but poor response for short jobs ([slideserve.com](https://www.slideserve.com/parkerernest/chapter-5-process-scheduling-powerpoint-ppt-presentation?utm_source=chatgpt.com "Chapter 5 Process Scheduling - SlideServe")).
    
2. **Shortest Job First (SJF) / Shortest Remaining Time First (SRTF)** – minimizes average wait time but needs burst-time prediction.
    
3. **Round-Robin (RR)** – each process gets a time quantum; preemptive, fair, and starvation-free ([en.wikipedia.org](https://en.wikipedia.org/wiki/Scheduling_%28computing%29?utm_source=chatgpt.com "Scheduling (computing)"), [en.wikipedia.org](https://en.wikipedia.org/wiki/Round-robin_scheduling?utm_source=chatgpt.com "Round-robin scheduling")).
    
4. **Priority Scheduling** – tasks assigned priorities; risk of starvation for low-priority jobs unless aging is used.
    
5. **Multilevel & Multilevel Feedback Queues** – multiple queues by priority or feedback from process behavior; adaptable to both CPU-bound and I/O-bound patterns ([en.wikipedia.org](https://en.wikipedia.org/wiki/Multilevel_feedback_queue?utm_source=chatgpt.com "Multilevel feedback queue")).
    

---

## 7. **Cooperating Processes & IPC**

- **Independent** processes have no interaction.
    
- **Cooperating** processes share data or synchronize, e.g., via the **Producer–Consumer** model, requiring mechanisms like IPC and synchronization primitives ([slideplayer.com](https://slideplayer.com/slide/5148461/?utm_source=chatgpt.com "CS 342 – Operating Systems Spring 2003 © Ibrahim Korpeoglu ...")).
    

---

## 8. **Process Creation & Termination (UNIX Model)**

- **Creation**: via `fork()` → duplicates the process image, including memory and open files; optionally followed by `exec()` to run new program ([slideplayer.com](https://slideplayer.com/slide/5148461/?utm_source=chatgpt.com "CS 342 – Operating Systems Spring 2003 © Ibrahim Korpeoglu ...")).
    
- **Termination**: via `exit()`. Parent can `wait()` for child’s exit status. OS reclaims resources (memory, files) ([slideplayer.com](https://slideplayer.com/slide/5148461/?utm_source=chatgpt.com "CS 342 – Operating Systems Spring 2003 © Ibrahim Korpeoglu ...")).
    
- Process relationships form **parent-child trees**.
    

---

## Summary Table

|**Concept**|**Key Details**|
|---|---|
|**Process**|Active program with state (CPU/I/O bursts)|
|**PCB**|Stores execution context & metadata|
|**Schedulers**|Long-term, Medium-term, Short-term|
|**Dispatching**|Context switching via dispatcher|
|**Scheduling algorithms**|FCFS, SJF, RR, Priority, Multilevel (feedback)|
|**IPC/cooperation**|Shared memory, Producer–Consumer model|
|**Creation/Termination**|fork/exec, exit, wait|
