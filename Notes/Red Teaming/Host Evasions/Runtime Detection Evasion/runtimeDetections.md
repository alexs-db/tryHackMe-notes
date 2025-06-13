## Runtime Detection Evasion

When executing code or applications, execution almost always flows through a runtime, regardless of the interpreter. This is especially common when using Windows API calls and interacting with .NET. The CLR (Common Language Runtime) and DLR (Dynamic Language Runtime) are the primary runtimes for .NET and are the most common you will encounter on Windows systems.

In this section, we will not discuss the specifics of runtimes. Instead, we will focus on how they are monitored and how malicious code is detected.

### How Runtime Detection Works

A runtime detection measure scans code before it executes in the runtime to determine if it is malicious. Depending on the detection technology, this can be based on:

- **String signatures**
- **Heuristics**
- **Behaviors**

If code is suspected of being malicious, it is assigned a value. If this value falls within a specified range, execution is stopped and the file or code may be quarantined or deleted.

### Runtime Detection vs. Standard Anti-Virus

Runtime detection measures differ from standard anti-virus solutions because they scan code directly from memory and the runtime environment. However, anti-virus products can also employ runtime detections to gain more insight into the calls and hooks originating from code. In some cases, anti-virus products may use a runtime detection stream or feed as part of their heuristics.

### Focus: AMSI (Anti-Malware Scan Interface)

In this room, we will primarily focus on **AMSI (Anti-Malware Scan Interface)**. AMSI is a runtime detection measure shipped natively with Windows and serves as an interface for other security products and solutions.