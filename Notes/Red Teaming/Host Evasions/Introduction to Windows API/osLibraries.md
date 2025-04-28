# Introduction to Windows API: OS Libraries

Each API call of the Win32 library resides in memory and requires a pointer to a memory address. The process of obtaining pointers to these functions is obscured because of ASLR (Address Space Layout Randomization) implementations. Each language or package has a unique procedure to overcome ASLR. Throughout this room, we will discuss the two most popular implementations: **P/Invoke** and the **Windows header file**.

In this task, we will take a deep dive into the theory of how both of these implementations work, and in future tasks, we will put them to practical use.

---

## Windows Header File

Microsoft has released the Windows header file, also known as the Windows loader, as a direct solution to the problems associated with ASLR’s implementation. At a high level, at runtime, the loader determines what calls are being made and creates a thunk table to obtain function addresses or pointers.

Luckily, we do not have to dive deeper than that to continue working with API calls if we do not desire to do so.

Once the `windows.h` file is included at the top of an unmanaged program, any Win32 function can be called.

> **Note:** We will cover this concept at a more practical level in Task 6.

---

## P/Invoke

Microsoft describes **P/Invoke** (Platform Invoke) as:

> "A technology that allows you to access structs, callbacks, and functions in unmanaged libraries from your managed code."

P/Invoke provides tools to handle the entire process of invoking an unmanaged function from managed code, or in other words, calling the Win32 API. P/Invoke begins by importing the desired DLL that contains the unmanaged function or Win32 API call. Below is an example of importing a DLL with options:

```csharp
using System;
using System.Runtime.InteropServices;

public class Program
{
    [DllImport("user32.dll", CharSet = CharSet.Unicode, SetLastError = true)]
    ...
}
```

In the above code, we are importing the DLL `user32.dll` using the attribute `DllImport`.

> **Note:** A semicolon is not included because the P/Invoke function is not yet complete. 

In the second step, we must define a managed method as an external one. The `extern` keyword informs the runtime of the specific DLL that was previously imported. Below is an example of creating the external method:

```csharp
using System;
using System.Runtime.InteropServices;

public class Program
{
    ...
    private static extern int MessageBox(IntPtr hWnd, string lpText, string lpCaption, uint uType);
}
```

Now we can invoke the function as a managed method, but we are calling the unmanaged function!

> **Note:** We will cover this concept at a more practical level in Task 7.