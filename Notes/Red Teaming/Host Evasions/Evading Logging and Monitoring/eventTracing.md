## Event Tracing for Windows (ETW) Overview

Almost all event logging capability within Windows is handled by **Event Tracing for Windows (ETW)** at both the application and kernel level. While other services like Event Logging and Trace Logging exist, these are either extensions of ETW or less prevalent to attackers.

### ETW Components

| Component   | Purpose                        |
|-------------|-------------------------------|
| Controllers | Build and configure sessions   |
| Providers   | Generate events                |
| Consumers   | Interpret events               |

We will cover each component and how it is instrumented in more depth in the next task.

---

### Event IDs and Logging

While less important to an attacker than components, **event IDs** are a core feature of Windows logging. Events are sent and transferred in XML (Extensible Markup Language) format, which is the standard for how events are defined and implemented by providers.

Below is an example of **event ID 4624**: An account was successfully logged on.

```
Event ID: 4624
Source: Security
Category: Logon/Logoff
Message: An account was successfully logged on.

Subject:
    Security ID: NT AUTHORITY\SYSTEM
    Account Name: WORKSTATION123$
    ...
    [ snip ]
    ...
    Logon Type: 7

New Logon:
    Security ID: CORPDOMAIN\john.doe
    Account Name: john.doe
    ...
    [ snip ]
    ...

Process Information:
    Process ID: 0x314
```

For more information about event logging, check out the **Windows Event Logs** room.

---

![Decorative image of glasses, a nose, and a mustache](#)

---

At this point, we understand why logging can disrupt an attacker, but how exactly is ETW relevant to an attacker? ETW has visibility over a majority of the operating system, whereas traditional logging generally has limited visibility or detail.

Due to the visibility of ETW, an attacker should always be mindful of the events that could be generated when carrying out their operation. The best approach to taking down ETW is to limit its insight as much as possible into specifically what you are doing while maintaining environment integrity.

In the upcoming tasks, we will cover **ETW instrumentation**, **ETW evasion**, and other ETW-based solutions.