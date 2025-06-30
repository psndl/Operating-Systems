Operating Systems Structures (OS Services, Interface, System Calls, System Programs, Design & Implementation

---

## 🧩 1. Operating System (OS) Structure Overview

- **Monolithic Kernels**: All OS services (e.g., file system, device drivers) reside in kernel space as one large process.
    
- **Layered Systems**: OS is organized into hierarchical layers (hardware at bottom, user interface at top).
    
- **Microkernels**: Minimal kernel; most services (e.g., device drivers, file systems) run in user space.
    
- **Modular/Modules**: Kernel supports dynamic loadable modules for added functionality.
    

---

## 2. OS Services

Core services provided to users and applications:

1. **Program execution** – loading and running programs.
    
2. **I/O operations** – abstract device interfaces.
    
3. **File-system manipulation** – create, delete, read, write.
    
4. **Communication** – interprocess communication, networking.
    
5. **Error detection** – for hardware and software errors.
    
6. **Resource allocation** – assigning system resources to multiple users/processes.
    
7. **Accounting & protection** – usage tracking, security control, and enforcing access permissions.
    

---

## 3. OS Interface

### a. User Interface

- **Command-Line Interface (CLI)**: Text-based commands.
    
- **Graphical User Interface (GUI)**: Windows, icons, pointers.
    
- **Batch Interface**: Job submission without interaction.
    

### b. Programming Interface

- **API/System Calls**: Needed for interacting with kernel-level services and hardware.
    

---

## 4. System Calls

Mechanism for application–kernel interaction.

- **Types**:
    
    - Process control (e.g. `fork`, `wait`)
        
    - File management (`open`, `read`, `write`)
        
    - Device management (`ioctl`)
        
    - Communications (`pipe`, `socket`)
        
    - Protection (`chmod`, `chown`)
        
- **Invocation Methods**:
    
    - **Trap instruction** – software interrupt takes execution to kernel mode.
        
    - **API wrapper** – e.g., C library functions call underlying system calls.
        

---

## 5. System Programs

- **File manipulation tools** (`cp`, `mv`, `ls`, etc.)
    
- **Status information programs** (`ps`, `top`)
    
- **File modification utilities** (editors, compilers)
    
- **Programming-language support** (e.g., compilers, debuggers, interpreters)
    
- **Program loading and execution** utilities
    
- **Communications tools** (e.g., `mail`, `chat`)
    

These act as the bridge between users and system calls.

---

## 6. OS Design and Implementation

### a. Design Goals

- **Efficiency**: quick execution, low overhead.
    
- **Reliability**: fault tolerance, error handling.
    
- **Portability**: ability to run across hardware/architecture.
    
- **Security**: protection from unauthorized access.
    
- **Ease of use**: user-friendly interfaces.
    

### b. Implementation Techniques

1. **Programming Language**:
    
    - Mostly written in C for portability and ease.
        
    - Critical parts (boot loader, interrupt handlers) often in Assembly.
        
2. **Module-based**:
    
    - Enables loading or unloading of components without rebuilding the kernel.
        
3. **Data structures**:
    
    - Key structures: process table, file descriptor table, memory tables.
        
4. **System Bootstrapping**:
    
    - Starts in ROM/firmware → loads kernel → sets up memory → starts `init` process.
        

---

## 7. Summary Table

|Topic|Key Ideas|
|---|---|
|OS Structures|Monolithic vs. Layered vs. Microkernel vs. Modular|
|Services|Execution, I/O, FS, IPC, Error detection, Resource mgmt, Protection|
|Interfaces|CLI/GUI/batch + API/system calls|
|System Calls|Kernel interactions via traps and standardized wrappers|
|System Programs|Tools that expose OS functionality to users|
|OS Design & Implementation|Goals: efficiency, reliability, etc. Techniques: modularity, languages, etc.|

---

### ✅ Tips for Your Study

- Understand **why OS structures differ** (e.g., trade-offs in stability vs performance).
    
- Practice tracing a **system call**: from user application → trap → kernel service → return.
    
- Compare **real-world OS architectures**: Linux (modular monolithic), MINIX (microkernel).
    