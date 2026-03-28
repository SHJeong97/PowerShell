# PowerShell Alias Lifecycle Portfolio (Get-Command • Help • Set-Alias • Remove-Alias)

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- Command discovery (`Get-Command`)
- Help system basics and updating help files (`Get-Help`, `Update-Help` prompt)
- Alias creation and reassignment (`Set-Alias`)
- Alias removal and validation (`Remove-Item alias:<name>`)
- Troubleshooting command resolution errors

---

## Full Implementation

### 1) Discover available commands (sample output shows aliases/modules)
```powershell
Get-Command
```
### 2) Check command help (and update help when prompted)
```powershell
Get-Help Disable-LocalUser
# When prompted: Update-Help? -> Y
```
### 3) Create an alias and use it (Test-Connection)
```powershell
Set-Alias tc Test-Connection
tc   # then provided google.com when prompted
```
### 4) Reassign the same alias to a different command (Get-Date)
```powershell
Set-Alias tc Get-Date
Tc
```
### 5) Remove the alias and confirm it’s gone
```powershell
Remove-Item alias:tc
Tc
```

## Implementation Walkthrough
### Get-Command
- Lists commands available in your session: cmdlets, functions, scripts, and aliases.


- Your output shows several entries where CommandType = Alias, including aliases provided by modules like Appx and Dism.


### Get-Help Disable-LocalUser (+ Update-Help prompt)
- Opens built-in help for Disable-LocalUser (which disables a local user account).


- PowerShell asked whether to run Update-Help, which downloads the latest help files for installed modules.


### Set-Alias tc Test-Connection then tc
- Creates a shortcut: typing tc runs Test-Connection.


- Running tc without parameters caused PowerShell to prompt for ComputerName, where you entered google.com.


### Set-Alias tc Get-Date then tc
- Reassigns the alias tc to a different command.


- Now tc runs Get-Date and prints the current date/time.


### Remove-Item alias:tc then tc
- Removes the alias from the session.


- Running tc after removal triggers a CommandNotFoundException, proving it no longer resolves to anything.



## Key Takeaways (What I Learned)
- PowerShell makes discovery easy.
 Get-Command quickly shows what commands exist and where they come from (modules/sources).


- Help is a feature you can refresh.
 Get-Help can prompt you to run Update-Help, which improves accuracy and detail in documentation.


- Aliases are flexible and can be reassigned.
 The same alias name (tc) can point to different commands at different times.


- Validation is immediate.
 Running the alias right after changing it is a fast way to confirm behavior.


- Command resolution errors are useful feedback.
 After removing tc, PowerShell clearly explains it can’t find a cmdlet/function/script/program with that name.



## Results & Validation
```powershell
PS C:\Users\tech> Get-Command

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Add-AppPackage                                     2.0.1.0    Appx
Alias           Add-AppPackageVolume                               2.0.1.0    Appx
Alias           Add-AppProvisionedPackage                          3.0        Dism
Alias           Add-ProvisionedAppPackage                          3.0        Dism

Get-Help Disable-LocalUser

Do you want to run Update-Help?
... (default is "Y"): y

NAME
   Disable-LocalUser

SYNOPSIS
   Disables a local user account.

PS C:\Users\tech> Set-Alias tc Test-Connection
PS C:\Users\tech> tc
cmdlet Test-Connection at command pipeline position 1
Supply values for the following parameters:
ComputerName[0]: google.com
ComputerName[1]:

Source        Destination     IPV4Address      IPV6Address                              Bytes    Time(ms)
------        -----------     -----------      -----------                              -----    --------
ARTHUR        google.com      142.250.217.142                                           32       3
ARTHUR        google.com      142.250.217.142                                           32       2
ARTHUR        google.com      142.250.217.142                                           32       3
ARTHUR        google.com      142.250.217.142                                           32       3

PS C:\Users\tech> Set-Alias tc Get-Date
PS C:\Users\tech> tc
Tuesday, February 24, 2026 1:50:49 PM

PS C:\Users\tech> Remove-Item alias:tc
PS C:\Users\tech> tc
tc : The term 'tc' is not recognized as the name of a cmdlet, function, script file, or operable program.
...
FullyQualifiedErrorId : CommandNotFoundException
```

## Validation Walkthrough
### Run these commands in order:
```powershell
Get-Command
Get-Help Disable-LocalUser

Set-Alias tc Test-Connection
tc  # enter google.com when prompted

Set-Alias tc Get-Date
tc

Remove-Item alias:tc
tc
```
