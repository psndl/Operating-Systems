## Introduction

- **Objective**: Understand how to protect system resources in a computer system.
- **Textbook Reference**: Chapter 14.
- **Focus**: Mechanisms to define and enforce protection rules for system resources.

## Key Concepts

### System Resources

- A computer system consists of **objects** (hardware or software resources) that need protection.
    - **Hardware Objects**: CPU, memory segments, devices (e.g., printers).
    - **Software Objects**: Files, semaphores, other system objects.
- Each object has:
    - A **unique name** for identification.
    - A set of **well-defined operations** (e.g., read, write, execute for files).

### Protection Problem

- **Goal**: Ensure that objects are accessed **correctly** and only by **authorized processes**.
- **Authorization**: Only processes with proper access rights can perform operations on objects.
- **Access Rights**: Define which processes can access which objects and in what way (e.g., read, write, execute).

### Guiding Principle: Principle of Least Privilege

- Programs, users, and systems should have **just enough privileges** to perform their tasks.
- Avoid granting unnecessary privileges to prevent misuse by malfunctioning or malicious processes/users.

### Protection Requirements

1. A process should only access resources for which it has **authorization**.
2. A process should only access resources it **currently requires** to complete its task (Need-to-Know Principle).

## Protection Domains

- **Definition**: A protection domain specifies which objects a process can access and how.
- **Example**: In a university system, domains could be:
    - Student domain
    - Faculty domain
    - Staff domain
- Each domain has associated **access rights** defining what operations can be performed on specific objects.
- Processes operate within a domain, and their access is restricted to the domain’s defined rights.

### Domain Association

- **Static Association**: A process is assigned to a domain at creation and cannot change domains during its lifetime.
- **Dynamic Association**: A process can switch domains during execution, gaining different access rights.

### Domain Specification

- **Static Domain**: Access rights are fixed and cannot be changed.
- **Dynamic Domain**: Access rights can be added or removed, allowing flexibility in privileges.

### Formal Definition of a Domain

- A domain is a set of **access rights**.
- **Access Right**: A tuple `<object-name, rights-set>`.
    - Example: `<file3, {read, write}>` means a process in the domain can read and write `file3`.
- **Example Domains**:
    - **D1**: `<O3, {read, write}>, <O1, {read, write}>, <O2, {execute}>`
    - **D2**: `<O2, {write}>, <O4, {print}>`
    - **D3**: `<O1, {execute}>, <O3, {read}>, <O4, {print}>`

## Protection in Unix

- **Multi-User System**: Multiple users can log in, each creating processes with their **user ID** and **group ID**.
- **Domains in Unix**: Defined by user ID (and optionally group ID).
    - Each user’s processes operate in a domain tied to their user ID.
- **File Access**:
    - Files (including device files) have associated user IDs and group IDs.
    - Access rights (read, write, execute) are specified for:
        - Owner (user ID)
        - Group
        - Others
    - Processes can access files if their user ID matches the file’s access permissions.

### Domain Switching in Unix

- **Set User ID (SUID) Bit**:
    - Each executable file has an SUID bit.
    - If SUID is set (`1`), a process executing the file switches to the file owner’s user ID (domain).
    - If SUID is not set (`0`), the process retains its original user ID.
- **Example**:
    - File `X` is owned by user `A` with SUID bit set.
    - User `B` creates process `P` (user ID `B`).
    - When `P` executes `X`, its user ID switches to `A` if SUID is set; otherwise, it remains `B`.

## Hierarchical Domains (Multics)

- Domains are organized as **rings** in a hierarchy.
- **Inner Rings**: Have more privileges.
- **Outer Rings**: Have fewer privileges (subset of inner ring privileges).
- **Example**:
    - Ring 0: Kernel processes (most privileges).
    - Higher rings: User processes (fewer privileges).
- A process in an inner ring (e.g., `Dj`) has all privileges of outer rings (e.g., `Di` where `j < i`).

## Access Matrix

- **Definition**: A matrix modeling protection rules, where:
    - **Rows**: Represent domains.
    - **Columns**: Represent objects.
    - **Entries**: Specify operations a domain can perform on an object (e.g., `{read, write}`).
- **Example**:
    
    ```
    |        | F1           | F2         | F3           | Printer    |
    |--------|--------------|------------|--------------|------------|
    | D1     | {read}       |            | {read}       |            |
    | D2     |              |            |              | {print}    |
    | D3     |              | {read}     | {execute}    |            |
    | D4     | {read, write}|            | {read, write}|            |
    ```
    
    - **Interpretation**:
        - `D1` can read `F1` and `F3`.
        - `D2` can print to `Printer`.
        - `D3` can read `F2` and execute `F3`.
        - `D4` can read and write `F1` and `F3`.

### Dynamic Access Matrix

- **Static Matrix**: Fixed entries, no changes allowed.
- **Dynamic Matrix**: Entries can be modified to add or remove access rights.
- **Special Access Rights**:
    - **Owner**: The domain owning an object can modify its access rights (e.g., grant read permission to another domain).
    - **Copy**: Allows a domain to copy its access right to another domain (e.g., `D2` can copy `read` on `F2` to `D3`).
    - **Control**: Allows a domain to modify another domain’s rights (e.g., `D2` can add/remove rights for `D4`).
    - **Switch**: Allows a process to switch from one domain to another (e.g., `D1` can switch to `D2`).

### Enforcement

- The operating system consults the access matrix when a process attempts an operation on an object.
- If the operation is listed in the matrix entry for the process’s domain and object, it is allowed; otherwise, it is denied.

## Implementation of Access Matrix

- **Access Control List (ACL)**:
    - **Object-Oriented**: Store a list of access rights with each object.
    - **Example**: For `F1`, store `{D1: read, D4: read, write}`.
    - **Advantages**:
        - Easy to revoke rights for an object (modify its ACL).
        - Compact storage (only non-empty entries stored).
    - **Unix Example**: File permissions stored in the inode (owner, group, others).
- **Capability List**:
    - **Domain-Oriented**: Store a list of capabilities with each domain.
    - **Example**: For `D1`, store `{F1: read, F3: read}`.
    - **Advantages**: Specifies what a domain can do.
    - **Disadvantages**: Revoking rights for an object requires searching all capability lists.
    - **Optimization**: Use back pointers from objects to capability lists for faster revocation.
- **Examples of Systems**:
    - **Unix**: Uses ACLs (file permissions).
    - **Hydra, Cambridge CAP**: Use capability-based systems.

## Revoking Access Rights

- **ACL Implementation**:
    - Simple and immediate: Modify the object’s ACL to remove the right (e.g., remove `write` from `F1` for `D4`).
- **Capability List Implementation**:
    - Complex: Search all capability lists to find and remove the right.
    - Optimization: Use back pointers to locate relevant capabilities quickly.

## Role-Based Access Control (RBAC)

- **Concept**: Processes are assigned **roles**, each with a set of privileges.
- **Example**: System administrator, normal user.
- **Switching Roles**: A process can switch roles (e.g., from user to admin) with proper authentication (e.g., password).
- **Advantages**: Flexible management of privileges based on roles rather than individual processes.

## Language-Based Protection

- **Example**: Java Virtual Machine (JVM).
- **Mechanism**:
    - Classes are assigned a **protection domain** when loaded.
    - The domain specifies allowed operations.
    - Example: Class members can be hidden (e.g., private fields) to restrict access by other classes.
- **Purpose**: Protect application-level objects, complementing kernel-level protection.

## Summary

- **Protection Goal**: Ensure authorized and correct access to system resources.
- **Mechanisms**:
    - Protection domains (static or dynamic).
    - Access matrix (ACL or capability list).
    - Special rights (owner, copy, control, switch).
    - Role-based access control.
    - Language-based protection (e.g., Java).
- **Unix**: Uses user IDs and SUID for domain-based protection, ACLs for file access.
- **Multics**: Hierarchical ring-based domains.
- **Implementation**: ACLs are efficient for revocation; capability lists are domain-focused but complex for revocation.
- **Policy vs. Mechanism**: Operating system enforces mechanisms; users specify policies.

## Questions

- How does the principle of least privilege improve system security?
- Compare static vs. dynamic domains in terms of flexibility and security.
- How does Unix handle domain switching, and what is the role of the SUID bit?
- Why is revoking access rights easier with ACLs than with capability lists?