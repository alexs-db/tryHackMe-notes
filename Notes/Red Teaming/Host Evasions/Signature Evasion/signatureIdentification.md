# Signature Identification

Before breaking signatures, it's important to understand what we're looking for. As discussed in the Introduction to Anti-Virus, signatures are used by anti-virus engines to detect suspicious or malicious programs. In this task, we'll see how to manually identify the exact byte where a signature starts.

## Manual Signature Identification Process

When identifying signatures, whether manually or automatically, an iterative process is used to determine where a signature begins. By recursively splitting a compiled binary in half and testing each part, you can estimate the byte range to investigate further.

You can use native utilities like `head`, `dd`, or `split` to divide a compiled binary. The following example demonstrates using `head` to find the first signature present in an `msfvenom` binary.

Once split, move the binary from your development environment to a machine with the anti-virus engine you want to test. If an alert appears, focus on the lower half of the split binary and split it again. If no alert appears, focus on the upper half and split again. Continue this pattern until you narrow down the location, typically to the kilobyte range.

When you can no longer accurately split the binary, use a hex editor to view the end of the binary where the signature is present. For example:

```
0000C2E0  43 68 6E E9 0A 00 00 00 0C 4D 1A 8E 04 3A E9 89  Chné.....M.Ž.:é‰
0000C2F0  67 6F BE 46 01 00 00 6A 40 90 68 00 10 00 00 E9  go¾F...j@.h....é
0000C300  0A 00 00 00 53 DF A1 7F 64 ED 40 73 4A 64 56 90  ....Sß¡.dí@sJdV.
0000C310  6A 00 68 58 A4 53 E5 E9 08 00 00 00 15 0D 69 B6  j.hX¤Såé......i¶
0000C320  F4 AB 1B 73 FF D5 E9 0A 00 00 00 7D 43 00 40 DB  ô«.sÿÕé....}C.@Û
0000C330  43 8B AC 55 82 89 C3 90 E9 08 00 00 00 E4 95 8E  C‹¬U‚‰Ã.é....ä•Ž
0000C340  2C 06 AC 29 A3 89 C7 90 E9 0B 00 00 00 0B 32 AC  ,.¬)£‰Ç.é.....2¬
```

The readability of the signature depends on the tool and compilation method used.

---

No one wants to spend hours manually tracking down bad bytes. Let's automate the process! In the next task, we'll explore some FOSS (Free and Open-Source Software) solutions to help identify signatures in compiled code.