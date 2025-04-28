# Introduction to Windows API: API Call Structure

API calls are the second main component of the Win32 library. These calls offer extensibility and flexibility that can be used to meet a plethora of use cases. Most Win32 API calls are well-documented under the Windows API documentation and [pinvoke.net](https://www.pinvoke.net/).

In this task, we will take an introductory look at naming schemes and in/out parameters of API calls.

## Naming Schemes and Representational Characters

API call functionality can be extended by modifying the naming scheme and appending a representational character. Below is a table of the characters Microsoft supports for its naming scheme:

| Character | Explanation                                                                 |
|-----------|-----------------------------------------------------------------------------|
| A         | Represents an 8-bit character set with ANSI encoding                        |
| W         | Represents a Unicode encoding                                              |
| Ex        | Provides extended functionality or in/out parameters to the API call       |

For more information about this concept, check out the [Microsoft documentation](https://learn.microsoft.com/).

## API Call Structure

Each API call also has a pre-defined structure to define its in/out parameters. You can find most of these structures on the corresponding API call document page of the Windows documentation, along with explanations of each I/O parameter.

Let’s take a look at the `WriteProcessMemory` API call as an example. Below is the I/O structure for the call:

```c
BOOL WriteProcessMemory(
    [in]  HANDLE  hProcess,
    [in]  LPVOID  lpBaseAddress,
    [in]  LPCVOID lpBuffer,
    [in]  SIZE_T  nSize,
    [out] SIZE_T  *lpNumberOfBytesWritten
);
```

For each I/O parameter, Microsoft also explains its use, expected input or output, and accepted values.

Even with an explanation, determining these values can sometimes be challenging for particular calls. It is recommended to always research and find examples of API call usage before using a call in your code.