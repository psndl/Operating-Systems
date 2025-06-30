
## 1. 🌐 Introduction to Process Synchronization

* **Race Condition**: Occurs when multiple processes concurrently access or manipulate shared data, and the final result depends on who runs when—leading to unpredictable outcomes ([home.adelphi.edu][1], [geeksforgeeks.org][2]).
* **Critical Section**: A segment of code accessing shared resources that **must execute atomically**, meaning uninterrupted and exclusive ([home.adelphi.edu][1]).
* **Critical-Section Problem Requirements**:

  1. **Mutual Exclusion** – No two processes in the critical section simultaneously.
  2. **Progress** – If no one is in the critical section, processes competing should get access without unnecessary delay.
  3. **Bounded Waiting** – There must be a limit on how long a process waits before entering the critical section ([cs.rochester.edu][3]).

---

## 2. Early (Flawed) Approaches

### 2.1 Simple Lock Variable

```c
while (lock);  // busy-wait
lock = TRUE;
// critical section
lock = FALSE;
```

* **Problem**: Not atomic—two processes may both pass the `while` before `lock` is set, causing race ([home.adelphi.edu][1]).

### 2.2 Strict Alternation (`turn` Variable)

```c
int turn = 0;
while (turn != me);
...critical section...
turn = other;
```

* Ensures mutual exclusion but **violates progress** (if one process doesn't want in, it still blocks the other) and **unfair waiting** ([cs.rochester.edu][3]).

### 2.3 Flag Array Only

```c
flag[i] = TRUE;
while (flag[j]);
// critical section
flag[i] = FALSE;
```

* Also fails: both may set `flag` to `TRUE` and enter – **no mutual exclusion** ([home.adelphi.edu][1]).

---

## 3. Correct 2-Process Solutions

### 3.1 Dekker’s Algorithm

* Uses both `flag[]` and `turn` to ensure fairness and mutual exclusion ([home.adelphi.edu][1]).
* Works by having each process indicate intent and check turn to resolve conflicts, with busy-wait and yielding.

### 3.2 Peterson’s Algorithm

* Elegant two-process solution:

  ```c
  // Shared: bool flag[2] = {false,false}; int turn;

  void enter_region(int i) {
    flag[i] = TRUE;
    turn = 1 - i;
    while (flag[1 - i] && turn == (1 - i)) { /* busy wait */ }
  }

  void leave_region(int i) {
    flag[i] = FALSE;
  }
  ```
* Guarantees **mutual exclusion**, **progress**, and **bounded waiting** ([home.adelphi.edu][1], [en.wikipedia.org][4]).

---

## 4. Hardware Solutions

### 4.1 Disabling Interrupts

* On uniprocessor systems, disabling interrupts prevents preemption during critical sections.
* **Cons**: Not scalable to multiprocessors; problematic if interrupts stay disabled ([ia.pw.edu.pl][5]).

### 4.2 Atomic Instructions: Test-and-Set, Swap, Fetch-and-Add

* e.g., **Test-and-Set**: Atomically reads a lock and sets it.
* Forms the basis for **spin-locks** (busy-waiting locks).
* However, can lead to **starvation under contention** ([home.adelphi.edu][1]).

---

## 5. Semaphores (Dijkstra, 1962)

* **Semaphore**: Integer with two atomic operations:

  * **P(s)**: Wait/decrement (blocks if ≤ 0)
  * **V(s)**: Signal/increment
* **Binary semaphore** (mutex) = counting semaphore with values {0,1}.
* Example:

  ```c
  semaphore mutex = 1;

  P(mutex);
  ```

// critical section
V(mutex);

```
- Semaphores enforce mutual exclusion and are used widely in OS synchronization constructs :contentReference[oaicite:33]{index=33}.

---

## 6. Higher-Level Constructs

### 6.1 Monitors
- High-level synchronization abstraction (Brinch Hansen, Hoare).
- Encapsulate shared data + procedures + condition variables (`wait`/`signal`).
- Automatically grants mutual exclusion within procedures :contentReference[oaicite:34]{index=34}.
- Implemented in languages like Java (`synchronized`) and C# (`Monitor`).

### 6.2 Additional Patterns
- **Producer–Consumer**, **Dining Philosophers**, **Readers-Writers** – classic sync problems solved using semaphores or monitors :contentReference[oaicite:35]{index=35}.

---

## 7. Summary Table

| Approach                | Mutual Exclusion | Progress | Bounded Waiting | Notes                                 |
|------------------------|------------------|----------|------------------|---------------------------------------|
| Lock variable          | ✗                | ✗        | ✗                | Non-atomic                              |
| Strict alternation     | ✓                | ✗        | ✗                | Blocks progress                      |
| Flag + busy-wait       | ✗                | ✗        | ✗                | Unsafe                                |
| Dekker / Peterson      | ✓                | ✓        | ✓                | Correct for 2 processes             |
| Disabling interrupts   | ✓ (uniprocessor) | ✓        | ✗                | Not scalable                        |
| Atomic instr + spinlock| ✓                | ✗ maybe | ✗ maybe         | Can starve under contention           |
| Semaphores             | ✓                | ✓        | ✓                | Kernel-level blocking                |
| Monitors               | ✓                | ✓        | ✓                | High-level, structured abstraction    |

---

## 🧭 Final Thoughts
- Fundamental to safe concurrent programming.
- **Peterson** is elegant but unsuitable for real multiprocessor systems.
- **Spinlocks** rely on hardware primitives; semaphores/blocking locks are more versatile.
- High-level constructs like monitors facilitate safe and maintainable code.

---

Would you like sample code, proofs (e.g., for Peterson's), or examples like Producer–Consumer in detail next?
::contentReference[oaicite:36]{index=36}
```

[1]: https://home.adelphi.edu/~siegfried/cs453/453l6.pdf?utm_source=chatgpt.com "[PDF] CSC 453 Operating Systems - Lecture 6 : Process Synchronization"
[2]: https://www.geeksforgeeks.org/operating-systems/introduction-of-process-synchronization/?utm_source=chatgpt.com "Introduction of Process Synchronization - GeeksforGeeks"
[3]: https://www.cs.rochester.edu/users/faculty/sandhya/csc256/lectures/lecture06-highsync.pdf?utm_source=chatgpt.com "[PDF] Synchronization Principles Race Condition The Critical-Section ..."
[4]: https://en.wikipedia.org/wiki/Peterson%27s_algorithm?utm_source=chatgpt.com "Peterson's algorithm"
[5]: https://www.ia.pw.edu.pl/~tkruk/edu/eopsya2009/lecture/eopsy04.pdf?utm_source=chatgpt.com "[PDF] Operating Systems Mutual Exclusion and Synchronization Race ..."
