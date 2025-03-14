# Windows Scripting Host (WSH)

Windows Scripting Host is a built-in Windows administration tool that runs batch files to automate and manage tasks within the operating system.

It is a Windows native engine, `cscript.exe` (for command-line scripts) and `wscript.exe` (for UI scripts), which are responsible for executing various Microsoft Visual Basic Scripts (VBScript), including `.vbs` and `.vbe`. For more information about VBScript, please visit [here](https://docs.microsoft.com/en-us/previous-versions//d1wf56tt(v=vs.85)). It is important to note that the VBScript engine on a Windows operating system runs and executes applications with the same level of access and permission as a regular user; therefore, it is useful for red teamers.

Now let's write a simple VBScript code to create a Windows message box that shows the "Welcome to THM" message. Make sure to save the following code into a file, for example, `hello.vbs`.

```vbscript
Dim message 
message = "Welcome to THM"
MsgBox message
```

In the first line, we declared the `message` variable using `Dim`. Then we store a string value of "Welcome to THM" in the `message` variable. In the next line, we use the `MsgBox` function to show the content of the variable. For more information about the `MsgBox` function, please visit [here](https://docs.microsoft.com/en-us/previous-versions//xshwtwzs(v=vs.85)). Then, we use `wscript` to run and execute the content of `hello.vbs`. As a result, a Windows message will pop up with the "Welcome to THM" message.

Now let's use VBScript to run executable files. The following VBScript code is to invoke the Windows calculator, proof that we can execute `.exe` files using the Windows native engine (WSH).

```vbscript
Set shell = WScript.CreateObject("Wscript.Shell")
shell.Run("C:\Windows\System32\calc.exe " & WScript.ScriptFullName), 0, True
```

We create an object of the WScript library using `CreateObject` to call the execution payload. Then, we utilize the `Run` method to execute the payload. For this task, we will run the Windows calculator `calc.exe`.

To execute the VBScript file, we can run it using `wscript` as follows:

```sh
c:\Windows\System32>wscript c:\Users\thm\Desktop\payload.vbs
```

We can also run it via `cscript` as follows:

```sh
c:\Windows\System32>cscript.exe c:\Users\thm\Desktop\payload.vbs
```

As a result, the Windows calculator will appear on the Desktop.

Another trick: If the VBS files are blacklisted, then we can rename the file to `.txt` and run it using `wscript` as follows:

```sh
c:\Windows\System32>wscript /e:VBScript c:\Users\thm\Desktop\payload.txt
```

The result will be the same as executing the VBS files, which run the `calc.exe` binary.
