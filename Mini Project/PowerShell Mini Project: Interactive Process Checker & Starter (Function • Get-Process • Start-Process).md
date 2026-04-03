# PowerShell Mini Project: Interactive Process Checker & Starter (Function • Get-Process • Start-Process)

This mini project prompts for a process name, checks whether it is running, and if not, asks whether to start it.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- Writing reusable functions with `param()`
- Mandatory parameters with `[Parameter(Mandatory)]`
- Process lookup with `Get-Process -ErrorAction SilentlyContinue`
- Conditional logic (`if / else`)
- Interactive prompts with `Read-Host`
- Starting applications with `Start-Process`

---

## Full Implementation

### Script: `process_checker.ps1`

```powershell
# Function to check the status of a given service
function Check-ProcessStatus {
    param(
        [Parameter(Mandatory)]
        [string]$processName
    )
    # Get the status of the
    $process = Get-Process -Name $processName -ErrorAction SilentlyContinue

    if ($null -ne $process) {
        Write-Host "$processName is running"
        return
    }
    else {
        Write-Host "$processName is not running. You want to start the process?"
        $start_process = Read-Host "You want to start the process?[y/n]"
        
        if ($start_process -eq "y" -or $start_process -eq "Y") {
            Start-Process $processName
        }
        else {
            Write-Host "Closing the session."
            exit
        }
    }
}

$processName = Read-Host "What process status you want to check?"

Check-ProcessStatus $processName

```
### Execution
```powershell
.\script\process_checker.ps1

```

##  Implementation Walkthrough
- The script asks: “What process status you want to check?”


- It calls Check-ProcessStatus with that name.


- Inside the function:


	- Get-Process -Name <name> checks if the process exists.


	- If found → prints “<process> is running”


	- If not found → prompts whether to start it


		- y/Y → Start-Process <name>


		- anything else → exits



##  Key Takeaways (What I Learned)
- Functions make scripts cleaner and reusable.


- -ErrorAction SilentlyContinue prevents noisy errors when a process name - doesn’t exist/running.


- Interactive confirmation is a safe pattern before starting an application.


- Combining “check + action” creates a practical troubleshooting tool for daily admin work.



##  Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> .\script\process_checker.ps1
What process status you want to check?: calc
calc is not running. You want to start the process?
You want to start the process?[y/n]: y
```

##  Validation Walkthrough
```powershell
# Run the script
.\script\process_checker.ps1

# Example test:
# 1) Enter: calc
# 2) If prompted, enter: y
```

