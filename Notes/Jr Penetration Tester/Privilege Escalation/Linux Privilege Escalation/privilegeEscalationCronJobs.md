# Privilege Escalation with Cron Jobs

Cron jobs are used to run scripts or binaries at specific times. By default, they run with the privilege of their owners and not the current user. While properly configured cron jobs are not inherently vulnerable, they can provide a privilege escalation vector under some conditions.

The idea is quite simple; if there is a scheduled task that runs with root privileges and we can change the script that will be run, then our script will run with root privileges.

Cron job configurations are stored as crontabs (cron tables) to see the next time and date the task will run.

Each user on the system has their crontab file and can run specific tasks whether they are logged in or not. As you can expect, our goal will be to find a cron job set by root and have it run our script, ideally a shell.

Any user can read the file keeping system-wide cron jobs under `/etc/crontab`.

While CTF machines can have cron jobs running every minute or every 5 minutes, you will more often see tasks that run daily, weekly, or monthly in penetration test engagements.

You can see the `backup.sh` script was configured to run every minute. The content of the file shows a simple script that creates a backup of the `prices.xls` file.

As our current user can access this script, we can easily modify it to create a reverse shell, hopefully with root privileges.

The script will use the tools available on the target system to launch a reverse shell. Two points to note:

1. The command syntax will vary depending on the available tools. (e.g., `nc` will probably not support the `-e` option you may have seen used in other cases)
2. We should always prefer to start reverse shells, as we do not want to compromise the system integrity during a real penetration testing engagement.

The file should look like this:

```bash
# Example content of the modified backup.sh script
```

We will now run a listener on our attacking machine to receive the incoming connection.

Crontab is always worth checking as it can sometimes lead to easy privilege escalation vectors. The following scenario is not uncommon in companies that do not have a certain cyber security maturity level:

1. System administrators need to run a script at regular intervals.
2. They create a cron job to do this.
3. After a while, the script becomes useless, and they delete it.
4. They do not clean the relevant cron job.

This change management issue leads to a potential exploit leveraging cron jobs.

The example above shows a similar situation where the `antivirus.sh` script was deleted, but the cron job still exists. If the full path of the script is not defined (as it was done for the `backup.sh` script), cron will refer to the paths listed under the `PATH` variable in the `/etc/crontab` file. In this case, we should be able to create a script named `antivirus.sh` under our user’s home folder and it should be run by the cron job.

The file on the target system should look familiar:

```bash
# Example content of the script created in the user's home folder
```

The incoming reverse shell connection has root privileges:

```bash
# Example of the reverse shell connection
```

In the odd event you find an existing script or task attached to a cron job, it is always worth spending time to understand the function of the script and how any tool is used within the context. For example, `tar`, `7z`, `rsync`, etc., can be exploited using their wildcard feature.