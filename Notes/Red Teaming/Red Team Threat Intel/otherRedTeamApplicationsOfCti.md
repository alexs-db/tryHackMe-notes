# Using CTI During Engagement Execution

## Introduction

**Cyber Threat Intelligence (CTI)** can be used during engagement execution to **emulate an adversary's behavioral characteristics**, such as:

- **C2 Traffic**
- **User Agents**
- **Ports, Protocols**
- **Listener Profiles**
- **Malware and Tooling**
- **IOCs**
- **Behaviors**

## C2 Traffic Manipulation

A **red team** can use CTI to analyze adversary traffic and **modify their own C2 traffic** to emulate it.

### Example: Malleable Profiles

**Malleable profiles** allow a red team operator to **control multiple aspects of a C2's listener traffic**.  
Information for these profiles can be gathered from **ISACs**, **collected IOCs**, or **packet captures**, including:

- **Host Headers**
- **POST URIs**
- **Server Responses and Headers**

Adjusting these parameters helps a red team make their traffic **closely resemble that of the targeted adversary**, improving adversary emulation.

## Analyzing Adversary Malware and Tooling Behavior

**Behavioral CTI** enables the analysis of adversary malware and tools to **develop offensive tooling that mimics similar behaviors** or exhibits **similar key indicators**.

### Example: Emulating a Dropper

If an adversary uses a **custom dropper**, the red team can emulate it by:

1. **Identifying the associated traffic.**
2. **Observing syscalls and API calls.**
3. **Analyzing overall dropper behavior and objectives.**
4. **Tampering with file signatures and IOCs.**

## Conclusion

**Behavioral threat intelligence** helps a **red team** prepare **specific tools** needed to **execute planned TTPs** with high accuracy.
