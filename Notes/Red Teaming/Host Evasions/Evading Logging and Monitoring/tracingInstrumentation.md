## ETW Components Overview

Event Tracing for Windows (ETW) is composed of three main components that work together to manage and correlate data. Event logs in Windows are essentially XML data, making them easy to process and interpret.

### 1. Event Controllers

Event Controllers are responsible for building and configuring sessions. They determine how and where data will flow. According to Microsoft:

> "Controllers are applications that define the size and location of the log file, start and stop event tracing sessions, enable providers so they can log events to the session, manage the size of the buffer pool, and obtain execution statistics for sessions."

### 2. Event Providers

Event Providers generate events. Controllers instruct providers on how to operate and collect logs from designated sources. From Microsoft:

> "Providers are applications that contain event tracing instrumentation. After a provider registers itself, a controller can then enable or disable event tracing in the provider. The provider defines its interpretation of being enabled or disabled. Generally, an enabled provider generates events, while a disabled provider does not."

#### Types of Providers

| Provider Type   | Purpose                                                                                  | Sessions Supported                |
|-----------------|------------------------------------------------------------------------------------------|-----------------------------------|
| MOF             | Defines events from MOF classes.                                                         | Enabled by one trace session      |
| WPP             | Uses TMF (Trace Message Format) files to decode information.                             | Enabled by one trace session      |
| Manifest-Based  | Defines events from a manifest.                                                          | Enabled by up to eight sessions   |
| TraceLogging    | Self-describing events containing all required information.                              | Enabled by up to eight sessions   |

### 3. Event Consumers

Event Consumers interpret events. They select sessions and parse events from one or multiple sessions, commonly seen in the "Event Viewer". From Microsoft:

> "Consumers are applications that select one or more event tracing sessions as a source of events. A consumer can request events from multiple event tracing sessions simultaneously; the system delivers the events in chronological order. Consumers can receive events stored in log files, or from sessions that deliver events in real time."

---

## ETW Data Flow

Events originate from providers. Controllers determine where the data is sent and how it is processed through sessions. Consumers save or deliver logs for interpretation or analysis.

---

## Attacker Perspective: Targeting ETW Components

Attackers may attempt to limit visibility while maintaining system integrity by targeting specific ETW components. Below are techniques associated with each component:

| Component  | Techniques                                                                                   |
|------------|---------------------------------------------------------------------------------------------|
| Provider   | PSEtwLogProvider Modification, Group Policy Takeover, Log Pipeline Abuse, Type Creation      |
| Controller | Patching EtwEventWrite, Runtime Tracing Tampering                                            |
| Consumer   | Log Smashing, Log Tampering                                                                  |

Each of these techniques will be covered in-depth in the upcoming tasks to provide a comprehensive set of possibilities.