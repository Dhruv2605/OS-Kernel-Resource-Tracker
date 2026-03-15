# Linux Kernel Resource Usage Tracker

A custom Linux kernel module and set of system calls designed to monitor and cap the resource usage of specific processes. 

This robust project was implemented as a patch to the Linux Kernel (`v5.15` context) for the Operating Systems (OS) coursework (Assignment 1) at IIT Delhi.

## Architecture & Implementation
The core functionality is introduced via a custom source file (`all_codes.c`) integrated into the kernel tree, and supported by modifications to the system call tables (`syscall_64.tbl`) and necessary kernel headers.

### Custom System Calls
The patch implements the following Syscalls to interact with process resources from user space:
- **`sys_register (452)`**: Registers a PID to be monitored for its heap memory size and open file descriptor count.
- **`sys_fetch (453)`**: Retrieves the current tracked resource statistics (`heapsize`, `files_count`) for a monitored PID.
- **`sys_deregister (454)`**: Removes a PID from the kernel's monitoring list and frees associated structures.
- **`sys_resource_cap (455)`**: Dynamically sets quota limits on heap memory and open files for a monitored process.
- **`sys_print_list (456)`**: Dumps all currently monitored PIDs to the kernel log (`dmesg`).
- **`sys_resource_reset (457)`**: Removes the applied resource limits from a specific process.

### Internal Mechanisms
- Maintains a thread-safe linked list (`monitored_processes`) protected by spinlocks (`monitored_processes_lock`).
- Safely traverses memory descriptors (`mm_struct`, `vm_area_struct`) using Maple Tree/VMA iteration to calculate active heap sizes.
- Traverses the FD table (`files_struct`, `fdtable`) to count open file descriptors in real-time.

## Repository Contents
- **`res_usage.patch`**: The unified diff file containing all kernel modifications, custom system calls, and hook integrations needed to build the tracking functionality into the Linux kernel.

## How to Apply
To test this implementation, place the `.patch` file in the root of your target Linux kernel source tree and apply it:
```bash
patch -p1 < res_usage.patch
```
After patching, recompile the kernel and boot into it to access the new system calls.

## About the Author
Developed by Dhruv in his second semester at IIT Delhi (2025).
