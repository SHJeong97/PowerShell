# PowerShell Process & Connectivity

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
All examples and validations below come from real commands I executed.

---

## Skills Practiced

- Process inspection & verification (name/PID, version metadata)
- Basic process control (start/stop)
- Network connectivity testing (detailed results + automation-friendly boolean checks)

---

## Full Implementation

### Process inspection (version + PID)
```powershell
Get-Process -Name notepad -FileVersionInfo
Get-Process -Id 4
### Start/stop a program
Start-Process -FilePath "C:\Program Files\TeamViewer\TeamViewer.exe"
Stop-Process -Id 3542
### Network connectivity test
Test-Connection -ComputerName google.com
Test-Connection -ComputerName google.com -Quiet
```

## Implementation Walkthrough
### 1) Get-Process -Name notepad -FileVersionInfo
- Finds the Notepad process by name.


- -FileVersionInfo returns executable metadata like ProductVersion, FileVersion, and FileName.


### 2) Get-Process -Id 4
- Pulls details for the process with PID 4.


- On Windows, PID 4 is typically the System process (core OS process).


### 3) Start-Process -FilePath "...TeamViewer.exe"
- Launches TeamViewer using its full path.


- Useful when the executable isn’t in your PATH or you want an exact binary.


### 4) Stop-Process -Id 3542
- Terminates a process by its PID.


- Requires the correct PID and appropriate permissions.


### 5) Test-Connection -ComputerName google.com
- Sends ICMP echo requests (PowerShell’s ping-style test).


- Returns detailed results: source, destination, IP, bytes, time.


### 6) Test-Connection -ComputerName google.com -Quiet
- Returns only True/False (quick health check for scripts/automation).



## Key Takeaways (What I Learned)
- PowerShell returns objects, not just text.
 Commands like Get-Process provide structured properties (CPU, Id, WorkingSet, etc.), which makes filtering and automation easier.


- You can verify a running program’s binary details.
 Get-Process -Name notepad -FileVersionInfo quickly shows file path and version info—useful for basic system validation and triage.


- PID targeting is accurate for troubleshooting.
 Get-Process -Id 4 demonstrates how to reference a specific process, and seeing System reinforces that some PIDs represent core OS components.


- Starting/stopping apps is scriptable.
 Start-Process launches an app from an exact path, while Stop-Process -Id ... ends a specific instance—useful for admin workflows.


- Connectivity tests can be human-readable or automation-ready.
 Test-Connection gives latency/IP details, while -Quiet returns True/False for quick checks inside scripts.



## Results & Validation
### Captured output from the session:
```powershell
PS C:\Users\tech> Get-Process -Name notepad -FileVersionInfo

ProductVersion   FileVersion      FileName
--------------   -----------      --------
10.0.19041.1     10.0.19041.1 ... C:\Windows\system32\NOTEPAD.EXE

PS C:\Users\tech> Get-Process -Id 4

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
  4454       0      204       5056     687.20      4   0 System

PS C:\Users\tech> Start-Process -FilePath "C:\Program Files\TeamViewer\TeamViewer.exe"
PS C:\Users\tech> Stop-Process -Id 3542

PS C:\Users\tech> Test-Connection -ComputerName google.com

Source        Destination     IPV4Address      IPV6Address                              Bytes    Time(ms)
------        -----------     -----------      -----------                              -----    --------
ARTHUR        google.com      142.250.65.78                                             32       4
ARTHUR        google.com      142.250.65.78                                             32       2
ARTHUR        google.com      142.250.65.78                                             32       2
ARTHUR        google.com      142.250.65.78                                             32       3

PS C:\Users\tech> Test-Connection -ComputerName google.com -Quiet
```

## Validation Walkthrough
### To reproduce similar results on your machine:
```powershell
# Process version metadata
Get-Process -Name notepad -FileVersionInfo

# Process by PID
Get-Process -Id 4

# Start/Stop (use a valid path + PID on your system)
Start-Process -FilePath "C:\Program Files\TeamViewer\TeamViewer.exe"
Stop-Process -Id 3542

# Connectivity test (detailed + boolean)
Test-Connection -ComputerName google.com
Test-Connection -ComputerName google.com -Quiet
```
