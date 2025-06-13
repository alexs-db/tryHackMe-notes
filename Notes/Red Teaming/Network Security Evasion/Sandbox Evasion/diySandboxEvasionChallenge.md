# The Great Escape

Now that you have gained some experience in escaping sandboxes, it's time for a challenge!

In this task, you will use the code from Task 4 to implement the **Ultimate Sandbox Evasion** method and escape TryHackMe's Sandbox program. To succeed, your dropper must implement the following techniques:

1. **Check if the device is joined to an Active Directory Domain**
2. **Check if the system memory is greater than 1GB of RAM**
3. **Implement an outbound HTTP request to `10.10.10.10`**
4. **Implement a 60-second sleep timer before retrieving your payload from your web server**

If your dropper meets these requirements, the flag will be printed out.

> **Good luck and have fun!**

---

### Resources

- Task 4 contains downloadable source code from four examples to assist with your sandbox evasion techniques.
- This material is also available on the VM at:  
    `C:\Users\Administrator\Desktop\Materials`

---

### Notes

- **Sandbox evasion techniques can fail.**  
    The program analyzes the binary to see if the checks are implemented. The outbound device may not have internet access—**as long as the checks are implemented, the sandbox check should succeed.**

---

## Sandbox Evasion Binary

When you are ready to test your evasion methods, use the binary at:

```
C:\Users\Administrator\Desktop\Materials\SandboxChecker.exe
```

**Example usage:**

```shell
C:\Users\Administrator\Desktop\Materials\> .\SandboxChecker.exe C:\Users\TryHackMe\Materials\SandboxEvasion.exe
[+] Memory Check found!
[+] Network Check found!
[+] GeoFilter Check found!
[+] Sleep Check found!
Congratulations! Here is your flag:
```

---

## Instructions

1. Create your own Sandbox Evasion executable using the code snippets in the task and the VM as reference.
2. Run `SandboxChecker.exe` in a command prompt, providing your executable as the argument.
3. All checks must be implemented correctly for the flag to be revealed.

> **Note:**  
> - If you have done it right, the "Sleep Check" will take approximately one minute to reveal the flag.
> - If your DNS check uses `if(dcNewName.find("\\"))` instead of `if(dcNewName.find("\\\\"))`, you may have difficulties with the sleep check.

---