# Introduction to Windows API

Microsoft provides low-level programming languages such as C and C++ with a pre-configured set of libraries that we can use to access needed API calls.

The `windows.h` header file, as discussed in Task 4, is used to define call structures and obtain function pointers. To include the Windows header, prepend the following line to any C or C++ program:

```c
#include <windows.h>
```

## Creating Our First API Call

Let’s jump right into creating our first API call. As our first objective, we aim to create a pop-up window with the title: **“Hello THM!”** using `CreateWindowExA`. To reiterate what was covered in Task 5, let’s observe the in/out parameters of the call:

```c
HWND CreateWindowExA(
    [in]           DWORD     dwExStyle,       // Optional window styles
    [in, optional] LPCSTR    lpClassName,     // Window class
    [in, optional] LPCSTR    lpWindowName,    // Window text
    [in]           DWORD     dwStyle,         // Window style
    [in]           int       X,               // X position
    [in]           int       Y,               // Y position
    [in]           int       nWidth,          // Width size
    [in]           int       nHeight,         // Height size
    [in, optional] HWND      hWndParent,      // Parent window
    [in, optional] HMENU     hMenu,           // Menu
    [in, optional] HINSTANCE hInstance,       // Instance handle
    [in, optional] LPVOID    lpParam          // Additional application data
);
```

### Assigning Values to Parameters

Let’s take these pre-defined parameters and assign values to them. As mentioned in Task 5, each parameter for an API call has an explanation of its purpose and potential values. Below is an example of a complete call to `CreateWindowExA`:

```c
HWND hwnd = CreateWindowEx(
        0, 
        CLASS_NAME, 
        L"Hello THM!", 
        WS_OVERLAPPEDWINDOW, 
        CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, 
        NULL, 
        NULL, 
        hInstance, 
        NULL
);
```

We’ve defined our first API call in C! Now we can implement it into an application and use the functionality of the API call.

### Example Application

Below is an example application that uses the API to create a small blank window:

```c
BOOL Create(
                PCWSTR lpWindowName,
                DWORD dwStyle,
                DWORD dwExStyle = 0,
                int x = CW_USEDEFAULT,
                int y = CW_USEDEFAULT,
                int nWidth = CW_USEDEFAULT,
                int nHeight = CW_USEDEFAULT,
                HWND hWndParent = 0,
                HMENU hMenu = 0
        )
{
        WNDCLASS wc = {0};

        wc.lpfnWndProc   = DERIVED_TYPE::WindowProc;
        wc.hInstance     = GetModuleHandle(NULL);
        wc.lpszClassName = ClassName();

        RegisterClass(&wc);

        m_hwnd = CreateWindowEx(
                dwExStyle, ClassName(), lpWindowName, dwStyle, x, y,
                nWidth, nHeight, hWndParent, hMenu, GetModuleHandle(NULL), this
        );

        return (m_hwnd ? TRUE : FALSE);
}
```

If successful, we should see a window with the title **“Hello THM!”**.

---

As demonstrated throughout this task, low-level languages make it very easy to define an API call quickly. Because of their ease of use and extensibility, C-based languages are the most popular among threat actors and vendors alike.