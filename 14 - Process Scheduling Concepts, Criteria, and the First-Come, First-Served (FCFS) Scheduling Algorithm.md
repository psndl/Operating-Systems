
**1. The Operating System as Resource Manager**

- Manages hardware and software resources on behalf of users and applications
    
- Schedules access to the CPU—a critically scarce resource—to optimize overall system performance and meet policy goals ([youtube.com](https://www.youtube.com/watch?v=caQgEeC8NiU&utm_source=chatgpt.com "CS-342 Operating Systems Lecture 14 - YouTube"))
    

---

**2. Objectives of CPU Scheduling**  
A scheduler must balance multiple—and sometimes conflicting—goals:

1. **CPU Utilization**
    
    - Keep the CPU as busy as possible (ideal: 100% utilization; realistic: 40–90%) ([cs.uic.edu](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/5_CPU_Scheduling.html?utm_source=chatgpt.com "Operating Systems: CPU Scheduling"))
        
2. **Throughput**
    
    - Number of processes completed per unit time
        
    - Example: 10 processes/sec in batch systems vs. 1 process/hour for long scientific jobs ([cs.uic.edu](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/5_CPU_Scheduling.html?utm_source=chatgpt.com "Operating Systems: CPU Scheduling"))
        
3. **Turnaround Time**
    
    - Total time from submission to completion
        
    - Turnaround Time=Completion Time−Arrival Time \text{Turnaround Time} = \text{Completion Time} - \text{Arrival Time}
    - Includes waiting time, execution time, and I/O time ([geeksforgeeks.org](https://www.geeksforgeeks.org/operating-systems/cpu-scheduling-criteria/?utm_source=chatgpt.com "CPU Scheduling Criteria - GeeksforGeeks"))
        
4. **Waiting Time**
    
    - Time a process spends waiting in the ready queue
        
    - \text{Waiting Time} = \text{Turnaround Time} - \text{Burst Time} \] :contentReference[oaicite:5]{index=5}
5. **Response Time**
    
    - Time from submission until the first response is produced (important in interactive systems)
        
    - \text{Response Time} = \text{First CPU Allocation Time} - \text{Arrival Time} \] :contentReference[oaicite:6]{index=6}
6. **Predictability and Fairness**
    
    - Similar processes under similar loads should experience similar performance
        
    - Avoid starvation—ensure low-priority processes eventually run
        

---

**3. Scheduling Criteria in Detail**

|Criterion|Definition|Goal|
|---|---|---|
|CPU Utilization|Percentage of time in which CPU is busy|Maximize|
|Throughput|Number of processes completed per unit time|Maximize|
|Turnaround Time|Time from submission to completion|Minimize|
|Waiting Time|Total time a process spends waiting in ready queue|Minimize|
|Response Time|Time from submission to first response|Minimize|
|Fairness|Avoid starvation; equal share for similar workloads|Ensure|

---

**4. First-Come, First-Served (FCFS) Scheduling**

- **Description**
    
    - Non-preemptive: once a process starts running, it runs to completion or blocks voluntarily
        
    - Processes are scheduled in the order of their arrival times ([geeksforgeeks.org](https://www.geeksforgeeks.org/first-come-first-serve-cpu-scheduling-non-preemptive/?utm_source=chatgpt.com "FCFS - First Come First Serve CPU Scheduling - GeeksforGeeks"))
        
- **Implementation**
    
    - Maintain a FIFO queue of ready processes
        
    - Dispatcher picks the process at the head of the queue
        
- **Gantt-Chart Example**
    
    |Process|Arrival Time (AT)|Burst Time (BT)|Start Time|Completion Time (CT)|Turnaround Time (TAT = CT – AT)|Waiting Time (WT = TAT – BT)|
    |---|---|---|---|---|---|---|
    |P1|0|5|0|5|5|0|
    |P2|0|3|5|8|8|5|
    |P3|0|8|8|16|16|8|
    
    - **Average Turnaround Time** = (5 + 8 + 16) / 3 = 9.67
        
    - **Average Waiting Time** = (0 + 5 + 8) / 3 = 4.33 ([geeksforgeeks.org](https://www.geeksforgeeks.org/first-come-first-serve-cpu-scheduling-non-preemptive/?utm_source=chatgpt.com "FCFS - First Come First Serve CPU Scheduling - GeeksforGeeks"))
        
- **Advantages & Disadvantages**
    
    - **Pros**:
        
        - Simple to understand and implement
            
        - No starvation (since non-preemptive, once started, a process will complete)
            
    - **Cons**:
        
        - **Convoy effect**: short processes wait behind long ones, increasing average waiting time
            
        - Poor average response time in interactive systems
            

---

**5. When to Use FCFS**

- Batch systems with minimal user interaction
    
- Environments where fairness (order of arrival) outweighs response-time requirements
    

---

**6. Summary**

- CPU scheduling is central to OS performance, balancing utilization, throughput, and user experience
    
- A clear understanding of scheduling criteria guides the choice of algorithm
    
- FCFS is the simplest non-preemptive algorithm—easy to implement but can suffer from long waits and poor responsiveness
    

---

_End of Lecture 14 Notes_