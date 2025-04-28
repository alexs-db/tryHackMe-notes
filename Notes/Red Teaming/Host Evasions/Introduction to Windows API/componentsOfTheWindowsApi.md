# Components of the Windows API

The Win32 API, more commonly known as the Windows API, has several dependent components that define its structure and organization.

Let’s break the Win32 API down using a top-down approach. We’ll assume the API is the top layer, and the parameters that make up a specific call are the bottom layer. The table below describes the top-down structure at a high level, with more details provided later.

| **Layer**            | **Explanation**                                                                                                                                              |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **API**              | A top-level/general term or theory used to describe any call found in the Win32 API structure.                                                              |
| **Header files or imports** | Defines libraries to be imported at runtime, specified by header files or library imports. Uses pointers to obtain the function address.                     |
| **Core DLLs**        | A group of four DLLs that define call structures (e.g., `KERNEL32`, `USER32`, and `ADVAPI32`). These DLLs define kernel and user services not contained in a single subsystem. |
| **Supplemental DLLs**| Other DLLs defined as part of the Windows API. These control separate subsystems of the Windows OS (~36 other defined DLLs, e.g., `NTDLL`, `COM`, `FVEAPI`, etc.). |
| **Call Structures**  | Defines the API call itself and the parameters of the call.                                                                                                 |
| **API Calls**        | The API call used within a program, with function addresses obtained from pointers.                                                                         |
| **In/Out Parameters**| The parameter values defined by the call structures.                                                                                                       |

Let’s expand these definitions. In the next task, we will discuss importing libraries, the core header file, and the call structure. In Task 4, we will dive deeper into the calls, understanding where and how to digest call parameters and variants.
