# PowerShell Mini Project: Interactive App Monitor (Get-Process • Responding • Kill)

This mini project prompts for an application name, checks whether the process exists, and reports whether it is responding.  
If the process is unresponsive, the script attempts to close it using `.Kill()`.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- Interactive input with `Read-Host`
- Process lookup with `Get-Process`
- Conditional logic (`if / elseif / else`)
- Checking process responsiveness (`.Responding`)
- Taking action on unresponsive processes (`.Kill()`)
- Console output messaging (`Write-Host`)

---

## Full Implementation

### Script: `app_monitor.ps1`

```powershell
# Ask the user for the process name
$process_name = Read-Host "Enter the name of your Application"

# Try to get the process
$process_object = Get-Process $process_name

# Check if the process is running and responding
if ($process_object -eq $null) {
    Write-Host "$process_name is not currently running."
}
elseif ($process_object.Responding) {
    Write-Host "$process_name is working correctly."
}
else {
    Write-Host "$process_name is unresponsive, closing..."
    $process_object.Kill()
}
```
### Execution
```powershell
.\Desktop\PlayGround\script\app_monitor.ps1
```

##  Implementation Walkthrough
- Prompt for a process name


	- Read-Host collects a process name (example: notepad).


- Get the process object


	- Get-Process $process_name attempts to retrieve the process.


- Decision logic


	- If the process object is $null, print that it isn’t running.


	- If .Responding is true, print that it’s working correctly.


	- Otherwise, report it as unresponsive and run .Kill() to terminate it.



##  Key Takeaways (What I Learned)
- PowerShell can monitor apps interactively with just a few lines.


- The process object contains health-related properties like .Responding.


- You can automate remediation by terminating a process if it becomes unresponsive.



##  Results & Validation
```powershell
PS C:\Users\tech> .\Desktop\PlayGround\script\app_monitor.ps1
Enter the name of your Application: notepad
notepad is working correctly.
```

##  Validation Walkthrough
```powershell
# Run the script
.\Desktop\PlayGround\script\app_monitor.ps1

# When prompted, enter a process name, e.g.:
Notepad
```

