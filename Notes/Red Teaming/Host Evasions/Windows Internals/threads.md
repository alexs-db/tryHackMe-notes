# Threads in Windows Internals

A thread is an executable unit employed by a process and scheduled based on device factors.

Device factors can vary based on CPU and memory specifications, priority and logical factors, and others.

We can simplify the definition of a thread: **"controlling the execution of a process."**

Since threads control execution, this is a commonly targeted component. Thread abuse can be used on its own to aid in code execution, or it is more widely used to chain with other API calls as part of other techniques.

Threads share the same details and resources as their parent process, such as code, global variables, etc. Threads also have their unique values and data, outlined in the table below.

| **Component**           | **Purpose**                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| **Stack**               | All data relevant and specific to the thread (exceptions, procedure calls, etc.) |
| **Thread Local Storage** | Pointers for allocating storage to a unique data environment               |
| **Stack Argument**       | Unique value assigned to each thread                                       |
| **Context Structure**    | Holds machine register values maintained by the kernel                    |

Threads may seem like bare-bones and simple components, but their function is critical to processes.