## Obfuscating API Calls and Dynamic Loading in C

Obfuscating functions and properties can achieve a lot with minimal modification. Even after breaking static signatures attached to a file, modern engines may still observe the behavior and functionality of the binary. This presents numerous problems for attackers that cannot be solved with simple obfuscation.

As covered in the Introduction to Anti-Virus, modern anti-virus engines employ two common methods to detect behavior:

- **Observing imports**
- **Hooking known malicious calls**

While imports can be easily obfuscated or modified, hooking requires complex techniques out of scope for this room. Observing API calls is a significant factor in determining if a file is suspicious.

### How API Calls Are Imported

API calls and other functions native to an operating system require:

- A pointer to a function address
- A structure to utilize them

These structures are located in import libraries such as `kernel32.dll` or `ntdll.dll` that store function structures and other core information for Windows.

#### The Import Address Table (IAT)

The most significant issue with function imports is the function addresses. Due to ASLR (Address Space Layout Randomization), function addresses are dynamic and must be found at runtime.

The Windows loader (`windows.h`) maps all modules to process address space and lists all functions from each. The Import Address Table (IAT) stores function addresses for all imported functions and assigns pointers for them. The IAT is stored in the PE (Portable Executable) header `IMAGE_OPTIONAL_HEADER` and is filled by the Windows loader at runtime.

> **Tip:** Check out the Windows Internals room for more information about the PE structure.

#### Example: PE Dump of a Function

*Image of DiE showing the IAT table of a binary*

The import table can provide a lot of insight into the functionality of a binary, which can be detrimental to an adversary. But how can we prevent our functions from appearing in the IAT if it is required to assign a function address?

### Dynamic Loading to Avoid the IAT

The thunk table is not the only way to obtain a pointer for a function address. We can also use an API call to obtain the function address from the import library itself. This technique is known as **dynamic loading** and can be used to avoid the IAT and minimize the use of the Windows loader.

We will write our structures and create new arbitrary names for functions to employ dynamic loading.

#### Steps for Dynamic Loading in C

1. **Define the structure of the call**
2. **Obtain the handle of the module the call address is present in**
3. **Obtain the process address of the call**
4. **Use the newly created call**

##### 1. Define the structure of the call

```c
typedef BOOL (WINAPI* myNotGetComputerNameA)(
    LPSTR   lpBuffer,
    LPDWORD nSize
);
```

##### 2. Obtain the handle of the module

```c
HMODULE hkernel32 = LoadLibraryA("kernel32.dll");
```

##### 3. Obtain the process address of the call

```c
myNotGetComputerNameA notGetComputerNameA = (myNotGetComputerNameA) GetProcAddress(hkernel32, "GetComputerNameA");
```

#### Considerations

- `GetProcAddress` and `LoadLibraryA` are still present in the IAT; this can reinforce suspicion.
- This problem can be solved using PIC (Position Independent Code).
- Modern agents may hook specific functions and monitor kernel interactions; this can be solved using API unhooking.

---

## Challenge: Obfuscate the C Snippet

Using the knowledge from this task, obfuscate the following C snippet, ensuring no suspicious API calls are present in the IAT.

```c
#include <windows.h>
#include <stdio.h>
#include <lm.h>

int main() {
    printf("GetComputerNameA: 0x%p\n", GetComputerNameA);
    CHAR hostName[260];
    DWORD hostNameLength = 260;
    if (GetComputerNameA(hostName, &hostNameLength)) {
        printf("hostname: %s\n", hostName);
    }
}
```

Once sufficiently obfuscated, submit the snippet to the webserver at `http://MACHINE_IP/challenge-2.html`. The file name must be saved as `challenge-2.exe`. If correctly obfuscated, a flag will appear in an alert pop-up.