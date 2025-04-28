# Commonly Abused Windows API Calls

Several API calls within the Win32 library lend themselves to be easily leveraged for malicious activity.

Several entities have attempted to document and organize all available API calls with malicious vectors, including SANs and MalAPI.io.

While many calls are abused, some are seen in the wild more than others. Below is a table of the most commonly abused API calls organized by frequency in a collection of samples.

| API Call           | Explanation                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| **LoadLibraryA**   | Maps a specified DLL into the address space of the calling process          |
| **GetUserNameA**   | Retrieves the name of the user associated with the current thread           |
| **GetComputerNameA** | Retrieves a NetBIOS or DNS name of the local computer                     |
| **GetVersionExA**  | Obtains information about the version of the operating system currently running |
| **GetModuleFileNameA** | Retrieves the fully qualified path for the file of the specified module and process |
| **GetStartupInfoA** | Retrieves contents of STARTUPINFO structure (window station, desktop, standard handles, and appearance of a process) |
| **GetModuleHandle** | Returns a module handle for the specified module if mapped into the calling process's address space |
| **GetProcAddress** | Returns the address of a specified exported DLL function                   |
| **VirtualProtect** | Changes the protection on a region of memory in the virtual address space of the calling process |

In the next task, we will take a deep dive into how these calls are abused in a case study of two malware samples.
