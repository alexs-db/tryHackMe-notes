# Subsystem and Hardware Interaction

Programs often need to access or modify Windows subsystems or hardware but are restricted to maintain machine stability. To solve this problem, Microsoft released the **Win32 API**, a library to interface between user-mode applications and the kernel.

## User Mode vs Kernel Mode

Windows distinguishes hardware access by two distinct modes: **user mode** and **kernel mode**. These modes determine the hardware, kernel, and memory access an application or driver is permitted. API or system calls interface between each mode, sending information to the system to be processed in kernel mode.

| User Mode                  | Kernel Mode                |
|----------------------------|----------------------------|
| No direct hardware access  | Direct hardware access     |
| Access to "owned" memory locations | Access to entire physical memory |

For more information about memory management, check out *Windows Internals*.

## Visual Representation

Below is a visual representation of how a user application can use API calls to modify kernel components:

> **Diagram**: User mode at the top and kernel mode at the bottom, divided by the switching point.

## Language Interaction with Win32 API

When looking at how programming languages interact with the Win32 API, this process can become further warped. The application will go through the **language runtime** before going through the API.

For more information about the runtime, check out *Runtime Detection Evasion*.