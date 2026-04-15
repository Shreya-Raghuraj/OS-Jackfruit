# OS JACKFRUIT PROJECT

## Team Members

| Name | SRN |
|------|-----|
| SHREYA RAGHURAJ | PES2UG24AM154 |
| NAMRATA M PATIL | PES2UG24AM099 |

---

##  Project Overview

OS Jackfruit is a minimal container runtime built in user space along with a Linux kernel module for monitoring memory usage.

The project demonstrates core Operating System concepts such as:
- Process isolation
- Namespace management
- Inter-process communication
- Memory monitoring and control

---

##  Features :

- Container creation using Linux namespaces
- Filesystem isolation using `chroot`
- Execution of commands inside containers
- Logging system using producer-consumer model
- Communication using UNIX domain sockets
- Memory monitoring using a kernel module
- Enforcement of soft and hard memory limits

---

##  System Architecture :

                    +----------------------+
                    |      Client CLI      |
                    | (User Commands)      |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |     Supervisor       |
                    |     (engine.c)       |
                    +----------+-----------+
                               |
        +----------------------+----------------------+
        |                                             |
        v                                             v
+----------------------+                 +----------------------+
|   Container Manager  |                 |   Communication      |
|  (clone, namespaces) |                 | (UNIX sockets)       |
+----------+-----------+                 +----------+-----------+
           |                                             |
           v                                             v
+----------------------+                 +----------------------+
|     Containers       |                 |    Logging System    |
| (Isolated Processes) |                 | (Producer-Consumer)  |
+----------+-----------+                 +----------+-----------+
           |                                             |
           v                                             v
+----------------------+                 +----------------------+
|  Filesystem (chroot) |                 |      Log Files       |
+----------------------+                 +----------------------+

                               |
                               v
                    +----------------------+
                    |    Kernel Module     |
                    |     (monitor.c)      |
                    +----------+-----------+
                               |

                               v
                    +----------------------+
                    | Memory Monitoring &  |
                    | Limit Enforcement   |
                    +----------------------+
                    ### 🔍 Explanation

- The **Client CLI** sends commands to the supervisor.
- The **Supervisor (engine.c)** manages container lifecycle and coordination.
- The **Container Manager** handles process isolation using namespaces and `clone()`.
- The **Communication module** uses UNIX sockets for interaction.
- The **Logging system** asynchronously records container activity.
- The **Kernel module (monitor.c)** monitors memory usage and enforces limits.
##  How to Run

# 1. Compile the project
```bash
gcc engine.c -o engine
2. start supervisor:
./engine supervisor <rootfs>
3. run a container :
./engine run c1 <rootfs> "/cpu_hog 10"
