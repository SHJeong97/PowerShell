# PowerShell Practice Log

This repo documents my hands-on PowerShell practice sessions.  
Everything below is based on commands I actually ran in the console.

---

## Full Implementation

### Commands executed (session log)

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
Get-Date
Get-Host
Get-Command
Get-Content
Get-History
```
### What happened
Get-Content prompted for Path because no -Path argument was provided.


Get-History (and alias h) confirmed the command history.



##  Implementation Walkthrough
- Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
 Lists running processes, sorts them by CPU usage (highest first), then shows the top 5.


- Get-Date
 Displays current date/time.


- Get-Host
 Shows your PowerShell host details (host type/version/culture).


- Get-Command
 Lists available commands (cmdlets/functions/aliases). (Output can be long.)


- Get-Content
 Reads a file’s contents. If you don’t provide -Path, PowerShell asks for it.


- Get-History / h
 Shows the commands you ran in the session.
##  Results & Validation
### Console output captured from the session:
```powershell
PS C:\Users\tech> Get-Date
Tuesday, February 24, 2026 11:25:17 AM

PS C:\Users\tech> Get-Host
Name             : ConsoleHost
Version          : 5.1.19041.6456
InstanceId       : 0b88093b-170f-4baa-a359-0a909799f39b
UI               : System.Management.Automation.Internal.Host.InternalHostUserInterface
CurrentCulture   : en-US
CurrentUICulture : en-US
PrivateData      : Microsoft.PowerShell.ConsoleHost+ConsoleColorProxy
DebuggerEnabled  : True
IsRunspacePushed : False
Runspace         : System.Management.Automation.Runspaces.LocalRunspace

PS C:\Users\tech> Get-Content
cmdlet Get-Content at command pipeline position 1
Supply values for the following parameters:
Path[0]:

PS C:\Users\tech> Get-History
  Id CommandLine
  -- -----------
   1 Get-Process | Sort-Object CPU -Descending | select-Object -First 5
   2 clear
   3 Get-Process
   4 clear
   5 Get-Date
   6 Get-Host
   7 clear
   8 Get-File
   9 Get-Content

PS C:\Users\tech> h
  Id CommandLine
  -- -----------
   1 Get-Process | Sort-Object CPU -Descending | select-Object -First 5
   2 clear
   3 Get-Process
   4 clear
   5 Get-Date
   6 Get-Host
   7 clear
   8 Get-File
   9 Get-Content
  10 Get-History
```
##  Validation Walkthrough
### To reproduce the same type of output on your machine:
```powershell
Get-Date
Get-Host
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
Get-Content            # (expect it to ask for Path if you don’t provide -Path)
Get-History
```

