# PowerShell Mini Project: Memory-Intensive Process Report (ArrayList • Threshold Filter • Stats)

This mini project prompts for a memory threshold (MB), finds processes using more than that amount of RAM (WorkingSet), prints a report, and calculates the total RAM usage of those “memory-intensive” processes.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- Script parameters with `param()` (optional search input)
- Collecting user input with `Read-Host`
- Using an `ArrayList` to store selected objects
- Filtering processes by memory usage (`WorkingSet`)
- Sorting with `Sort-Object`
- Rounding and unit conversion (MB/GB)
- Summarizing totals with `Measure-Object -Sum`

---

## Full Implementation

### Script: `memory_report.ps1`

```powershell
# Parameter for search functionality
param(
    [string] $SearchProcess
)

# Create an ArrayList to store memory-intensive processes
$memoryHungryProcesses = [System.Collections.ArrayList]::new()

# Get all running processes
$allProcesses = Get-Process

# Setting an usage amount to see morethat $setUsage proccess
$setUsage = Read-Host "Enter the usage amount that you want to see above the number of MB"

# Loop through all processes and add those using more than 100MB to the ArrayList 
foreach ($process in $allProcesses) {
    if ($process. WorkingSet / 1MB -gt $setUsage) {
        [void] $memoryHungryProcesses.Add($process)
    }
}

# Add the search functionality
if ($SearchProcess) { 
    Write-Output = "`nSearching for process: $SearchProcess"
    $found =$false
    foreach ($process in $memoryHungryProcesses) { 
        if ($process.Name -eq $SearchProcess) {
            $memoryUsageMB = [math]::Round($process. WorkingSet / 1MB, 2) 
            Write-Output "$SearchProcess is using $memoryUsageMB MB of memory"
            $found = $true
            exit
        } 
    }

    if (-not $found) {
        Write-Output "$SearchProcess is not in the list of memory-intensive processes"
        exit
    }
}

# Display report header
Write-Output "Memory-Intensive Processes Report"
Write-Output "--------------------------------"
Write-Output "Total memory-intensive processes: $($memoryHungryProcesses.Count)"

# Sort processes by memory usage
$sortedProcesses = $memoryHungryProcesses | Sort-Object -Property WorkingSet -Descending

# Loop Through the array list and display the memory usage of each process
foreach ($process in $memoryHungryProcesses) {
    $memoryUsageMB = [math]::Round($process.WorkingSet / 1MB, 2)
    Write-Output "$($process.Name) is using $memoryUsageMB MB of memory"
}

# Calculate and display total memory usage
$totalmemoryUsage = ($memoryHungryProcesses | Measure-Object -Property WorkingSet -Sum).Sum / 1GB
$totalmemoryUsage = [math]::Round($totalmemoryUsage, 2)
Write-Output "`nTotal memory usage of intensive processe: $totalmemoryUsage GB"

```
### Execution
```powershell
.\script\memory_report.ps1
```

## Implementation Walkthrough
- Input threshold


	- The script asks for a number (MB) and stores it in $setUsage.


- Collect memory-heavy processes


	- Get-Process returns all processes.


	- It checks each process: (WorkingSet / 1MB) -gt $setUsage.


	- Matching processes are stored into an ArrayList.


- Report output


	- Displays a header and total count of matching processes.


	- Prints each process name and its WorkingSet converted to MB.


- Totals


	- Uses Measure-Object WorkingSet -Sum to compute total memory usage.


	- Converts total to GB and rounds it.



## Key Takeaways (What I Learned)
- WorkingSet is a practical way to estimate RAM usage per process.


- A threshold filter creates an instant triage list (only “big” memory processes).


- Measure-Object -Sum is a fast way to create summary stats for a report.


- Storing results first (ArrayList) makes reporting easier than re-filtering repeatedly.



## Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> .\script\memory_report.ps1
Enter the usage amount that you want to see above the number of MB: 500
Memory-Intensive Processes Report
--------------------------------
Total memory-intensive processes: 2
Memory Compression is using 1148.81 MB of memory
VMS_SMC is using 607.75 MB of memory

Total memory usage of intensive processe: 1.72 GB
PS C:\Users\tech\Desktop\PlayGround>
```

## Validation Walkthrough
```powershell
# Run the script
.\script\memory_report.ps1

# When prompted, enter a threshold in MB (example used here):
500
```
