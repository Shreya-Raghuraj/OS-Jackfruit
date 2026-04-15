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
```
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
              +----------------+----------------+
              |                                 |
              v                                 v
+--------------------------+     +--------------------------+
|   Container Manager      |     |     Communication        |
| (clone, namespaces)      |     |    (UNIX sockets)        |
+-----------+--------------+     +-----------+--------------+
            |                                |
            v                                v
+--------------------------+     +--------------------------+
|      Containers          |     |     Logging System       |
| (Isolated Processes)     |     |   (Producer-Consumer)    |
+-----------+--------------+     +-----------+--------------+
            |                                |
            v                                v
+--------------------------+     +--------------------------+
| Filesystem (chroot)      |     |        Log Files         |
+--------------------------+     +--------------------------+

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
                    | Limit Enforcement    |
                    +----------------------+
```

- The **Client CLI** sends commands to the supervisor.
- The **Supervisor (engine.c)** manages container lifecycle and coordination.
- The **Container Manager** handles process isolation using namespaces and `clone()`.
- The **Communication module** uses UNIX sockets for interaction.
- The **Logging system** asynchronously records container activity.
- The **Kernel module (monitor.c)** monitors memory usage and enforces limits.
##  How to Run
```
## Commands Used (Task-wise)

---

### Task 1: Container Creation (clone + namespaces)
Implemented using clone()
Namespaces used:
CLONE_NEWPID
CLONE_NEWUTS
CLONE_NEWNS
Mounted /proc inside container
Used chroot for filesystem isolation
**Compile**
```bash
gcc engine.c -o engine -lpthread

(Start supervisor):
sudo ./engine supervisor ./rootfs-base
(Run container):
sudo ./engine start alpha ./rootfs-alpha "/bin/sh -c 'echo hello; sleep 30'"
```
screenshot :
<img width="1090" height="502" alt="Screenshot 2026-04-15 at 4 36 30 PM" src="https://github.com/user-attachments/assets/eb4f1dd5-f051-462b-8ac0-eacc4eb7d675" />
### Task 2: Logging System (Producer-Consumer):
Implemented using producer-consumer model
Separate logging thread
Stores container activity logs
logs stored in 
```
logs/<container_id>.log
```
Commands used:
```
./engine run c1 <rootfs> "/cpu_hog 5"
ls logs/
cat logs/c1.log
```
screenshots:

<img width="1090" height="502" alt="Screenshot 2026-04-15 at 4 41 26 PM" src="https://github.com/user-attachments/assets/20790566-724c-438c-9e1a-f64caa94be01" />

Task 3: Communication
Used UNIX domain sockets
Enables communication between CLI and supervisor
socket path 
```
/tmp/mini_runtime.sock
```
commands used:
```
./engine start c1 <rootfs> "/cpu_hog 10"
./engine ps
./engine logs c1
./engine stop c1
```
screenshots:
<img width="1144" height="435" alt="Screenshot 2026-04-15 at 4 44 41 PM" src="https://github.com/user-attachments/assets/6212d2aa-a63f-4f78-b8e8-10185b639eff" />

###task 4 Kernel Module
Tracks memory usage of containers
Provides data to user space
commands used:
```
./engine run c2 <rootfs> "/bin/sh"
```
screenshots:
<img width="1030" height="472" alt="Screenshot 2026-04-15 at 4 46 59 PM" src="https://github.com/user-attachments/assets/7acf77de-20ae-46a7-a478-7f2b4bf1e3d3" />

###task 5Memory Limits
Implemented soft and hard limits
Prevents excessive memory usage

commands used:
```
sudo insmod monitor.ko
dmesg | tail
```
screenshots:

<img width="670" height="477" alt="Screenshot 2026-04-15 at 4 48 59 PM" src="https://github.com/user-attachments/assets/8e0f0a5d-94d9-43c5-a11a-77f42923aaac" />
<img width="1114" height="413" alt="Screenshot 2026-04-15 at 4 52 11 PM" src="https://github.com/user-attachments/assets/9b0654df-a04e-4663-815f-bbabf2884c29" />

##Task 6:User-Kernel Integration
Communication via ioctl system calls
Containers registered with kernel module
Kernel tracks memory usage of registered processes
commands used:
```
./engine run c3 <rootfs> "/memory_hog 8 100"
dmesg | tail
```
screenshots
<img width="1131" height="390" alt="Screenshot 2026-04-15 at 4 50 08 PM" src="https://github.com/user-attachments/assets/2bd0c160-7971-42f4-bcf6-6d329165c4e4" />
<img width="1155" height="388" alt="Screenshot 2026-04-15 at 4 51 44 PM" src="https://github.com/user-attachments/assets/7e56b11a-28b1-4523-b350-9024010a1d1b" />

---

##  Challenges Faced

- Understanding how `clone()` works with multiple namespaces
- Debugging container isolation issues
- Handling synchronization in producer-consumer logging
- Kernel module errors during loading (`insmod`, permissions)
- Interpreting `dmesg` logs for debugging

---

##  Key Learnings

- How containers work internally (without Docker)
- Importance of Linux namespaces for isolation
- Basics of kernel module development
- Memory management and enforcement in OS
- Inter-process communication using UNIX sockets

---

##  Limitations

- Basic implementation (not as robust as Docker)
- Limited error handling
- No advanced scheduling or resource control
- Manual setup required for kernel module

---

##  Future Improvements

- Add better CLI interface
- Implement cgroups for stronger resource control
- Improve logging with structured format
- Add container networking support
- Build a dashboard for monitoring

---

##  Conclusion

This project provided hands-on experience in building a minimal container runtime and understanding low-level OS concepts like process isolation, IPC, and kernel interaction. It bridges the gap between theoretical OS concepts and real-world system design.
