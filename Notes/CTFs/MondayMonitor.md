# Monday Monitor CTF Notes

## Question 1
**Initial access was established using a downloaded file. What is the file name saved on the host?**

**A:** SwiftSpend_Financial_Expenses.xlsm

I understand that the first question might be a bit confusing at first glance. But don’t worry, we’ll figure it out together.

Firstly, the question asks for the file name saved on the host. To find this, you should navigate to: Events → search bar (localhost).

**Source:** TryHackMe’s Wazuh

You will see three hits displayed above the table. Please direct your attention to the top document.

**Source:** TryHackMe’s Wazuh

Then, you will find the answer in the `data.win.event.data.commandLine` section.

**Source:** TryHackMe’s Wazuh

## Question 2
**What is the full command run to create a scheduled task?**

**A:** `cmd.exe /c reg add HKCU\SOFTWARE\ATOMIC-T1053.005 /v test /t REG_SZ /d cGluZyB3d3cueW91YXJldnVsbmVyYWJsZS50aG0= /f & schtasks.exe /Create /F /TN "ATOMIC-T1053.005" /TR "cmd /c start /min "" powershell.exe -Command IEX([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String((Get-ItemProperty -Path HKCU:\\SOFTWARE\\ATOMIC-T1053.005).test)))" /sc daily /st 12:34`

As we learned in the Wazuh module, you can search for ‘scheduler’ in the search bar. To find the full command used to create the scheduled task, add the filter `data.win.eventdata.parentCommandLine`, which will display the complete command.

**Source:** TryHackMe’s Wazuh

Then, you’ll find that one of the documents contains a lengthy command.

**Source:** TryHackMe’s Wazuh

## Question 3
**What time is the scheduled task meant to run?**

**A:** 12:34

This one is quite simple. Scroll up a bit, and you’ll find the answer in the original `data.win.eventdata.CommandLine` fields.

**Source:** TryHackMe’s Wazuh

## Question 4
**What was encoded?**

**A:** ping www.youarevulnerable.thm

This one might seem a bit confusing, but it’s actually quite straightforward. Notice that the `data.win.eventdata.parentCommandLine` field contains a long string with seemingly random code. We need to address this, as it includes an encoded string: `cGluZyB3d3cueW91YXJldnVsbmVyYWJsZS50aG0=`.

**Source:** TryHackMe’s Wazuh

We use the powerful tool CyberChef to decode this string easily. And there you have it — our answer!

**Source:** TryHackMe’s Wazuh

## Question 5
**What password was set for the new user account?**

**A:** I_AM_M0NIT0R1NG

To answer this question, search for ‘net’ in the search bar. Then, look for the answer in the `data.win.eventdata.parentCommandLine` fields.

**Source:** TryHackMe’s Wazuh

Finally, we find it among the other documents!

**Source:** TryHackMe’s Wazuh

## Question 6
**What is the name of the .exe that was used to dump credentials?**

**A:** memotech.exe

To answer this question, search for ‘mimikatz’ since the question emphasizes dumping credentials.

After that, filter by `data.win.eventdata.Image`.

**Source:** www.wallarm.com

And you’ll get the answer right away.

**Source:** TryHackMe’s Wazuh

## Question 7
**Data was exfiltrated from the host. What was the flag that was part of the data?**

**A:** THM{M0N1T0R_1$_1N_3FF3CT}

This one is straightforward. To find the THM flag, simply search for ‘THM’ in the search bar to avoid missing it. Additionally, focus on `data.win.eventdata.CommandLine` to locate the flag.
