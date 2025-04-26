# Windows Internals: Processes

A process maintains and represents the execution of a program; an application can contain one or more processes. A process has many components that it gets broken down into to be stored and interacted with. The Microsoft documentation describes these components as follows:

> "Each process provides the resources needed to execute a program. A process has a virtual address space, executable code, open handles to system objects, a security context, a unique process identifier, environment variables, a priority class, minimum and maximum working set sizes, and at least one thread of execution."

This information may seem intimidating, but this guide aims to simplify the concept.

## Processes in Windows

Processes are created from the execution of an application. They are core to how Windows functions, as most of its functionality can be represented as applications with corresponding processes. Below are a few examples of default applications that start processes:

- **MsMpEng** (Microsoft Defender)
- **wininit** (keyboard and mouse)
- **lsass** (credential storage)

Attackers often target processes to evade detection and disguise malware as legitimate processes. Below is a small list of potential attack vectors attackers could employ against processes:

- **Process Injection** (T1055)
- **Process Hollowing** (T1055.012)
- **Process Masquerading** (T1055.013)

## Key Components of a Process

Processes have many components that can be described at a high level. The table below outlines the critical components of processes and their purposes:

| **Process Component**          | **Purpose**                                                                 |
|--------------------------------|-----------------------------------------------------------------------------|
| **Private Virtual Address Space** | Virtual memory addresses allocated to the process.                          |
| **Executable Program**         | Defines code and data stored in the virtual address space.                  |
| **Open Handles**               | Handles to system resources accessible to the process.                      |
| **Security Context**           | Access token defining the user, security groups, privileges, and security info. |
| **Process ID**                 | Unique numerical identifier of the process.                                 |
| **Threads**                    | Sections of a process scheduled for execution.                             |

### Process Memory Layout

At a lower level, a process resides in the virtual address space. The table below describes the components of a process in memory:

| **Component**       | **Purpose**                              |
|---------------------|------------------------------------------|
| **Code**            | Code to be executed by the process.      |
| **Global Variables**| Stored variables.                        |
| **Process Heap**    | Defines the heap where data is stored.   |
| **Process Resources**| Further resources of the process.       |
| **Environment Block**| Data structure defining process info.   |

## Observing Processes

To make processes more tangible, you can observe them in the Windows Task Manager. The Task Manager reports on many components and details about a process. Below is a table with a brief list of essential process details:

| **Value/Component** | **Purpose**                                      | **Example**       |
|---------------------|--------------------------------------------------|-------------------|
| **Name**            | Name of the process, typically inherited from the application. | `conhost.exe`    |
| **PID**             | Unique numerical value to identify the process.  | `7408`            |
| **Status**          | Determines how the process is running (e.g., running, suspended). | `Running`        |
| **User Name**       | User that initiated the process, denoting its privilege level. | `SYSTEM`         |

These are the components you would interact with most as an end-user or manipulate as an attacker.

## Tools for Observing Processes

There are multiple utilities available to make observing processes easier, including:

- **Process Hacker 2**
- **Process Explorer**
- **Procmon**

## Conclusion

Processes are at the core of most internal Windows components. Understanding their structure and behavior is essential for both system administrators and security professionals. The following tasks will extend the information about processes and their usage in Windows.