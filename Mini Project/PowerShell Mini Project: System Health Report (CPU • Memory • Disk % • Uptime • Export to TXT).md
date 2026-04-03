# PowerShell Mini Project: System Health Report (CPU • Memory • Disk % • Uptime • Export to TXT)

This mini project collects key system metrics (OS, CPU load, memory, disk usage) and **adds system uptime** based on `LastBootUpTime`.  
It prints a formatted report to the console and saves it to `System_Health_report.txt`.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- System inventory via CIM (`Get-CimInstance`)
- Reading environment variables (`$env:COMPUTERNAME`)
- CPU load collection (`Win32_Processor.LoadPercentage`)
- Memory + disk calculations (free/total + % used)
- Calculating uptime from `LastBootUpTime`
- Building multi-line reports using strings, escape sequences (`` `n ``), and the format operator (`-f`)
- Outputting to console (`Write-Host`) and exporting to TXT (`Out-File`)

---

## Full Implementation

### Script: `system_health_report.ps1`

```powershell
# Get system information
$computerName = $env:COMPUTERNAME
$osInfo = Get-CimInstance Win32_OperatingSystem
$cpu = (Get-CimInstance Win32_Processor).LoadPercentage
$totalMemory = $osInfo.TotalVisibleMemorySize / 1MB
$freeMemory = $osInfo.FreePhysicalMemory / 1MB
$totalSpace = (Get-CimInstance -ClassName Win32_LogicalDisk | Where-Object DeviceID -eq 'C:').Size / 1GB
$freeSpace = (Get-CimInstance -ClassName Win32_LogicalDisk | Where-Object DeviceID -eq 'C:').FreeSpace / 1GB

# Get System's uptime
$uptime = (Get-Date) - $osInfo.LastBootUpTime
$uptimeDays = $uptime.Days
$uptimeHours = $uptime.Hours
$uptimeMinutes = $uptime.Minutes

# Create the report
$report = "System Health Report`n"
$report += "--------------------`n"
$report += "Generated on: {0:yyyy-MM-dd HH:mm: ss}`n`n" -f (Get-Date)
 
$report += "Computer Name: {0}`n" -f $computerName
$report += "OS Name: {0}`n" -f $osInfo.Caption
$report += "OS Version: {0}`n`n" -f $osInfo.Version
$report += "CPU Usage: {0}%`n" -f $cpu
$report += "Memory: {0:N2} GB free of {1:N2} GB`n" -f $freeMemory, $totalMemory
$report += "Disk (C:): {0:N2} GB free of {1:N2} GB`n" -f $freeSpace, $totalSpace

# Calculate and add usage percentages
$memoryUsagePercent = 100 - (($freeMemory / $totalMemory) * 100)
$diskUsagePercent = 100 - (($freeSpace / $totalSpace) * 100)
$report += "`nUsage Percentages:`n"
$report += "Memory: {0:N2}% used`n" -f $memoryUsagePercent
$report += "Disk (C:): {0:N2}% used`n" -f $diskUsagePercent

# Display the uptime
$report += "System Uptime: {0}`n" -f $(
    $uptimeString = ""
    if ($uptimeDays -gt 0) {
        $uptimeString += "$uptimeDays days,"
    }
    if ($uptimeHours -gt 0 -or $uptimeDays -gt 0) {
        $uptimeString += "$uptimeHours hours,"
    }
    if ($uptimeMinutes -gt 0 -or $uptimeHours -gt 0 -or $uptimeDays -gt 0) {
        $uptimeString += "$uptimeMinutes minutes" 
    }
    $uptimeString
)

# Display the report
Write-Host $report

# Save the report
$report | Out-File -FilePath "System_Health_report.txt"
```
### Execution
```powershell
.\script\system_health_report.ps1
```

## Implementation Walkthrough
- Collect system facts


	- $env:COMPUTERNAME for the device name


	- Win32_OperatingSystem for OS caption/version + memory values + LastBootUpTime


	- Win32_Processor.LoadPercentage for current CPU load


	- Win32_LogicalDisk for C: drive total/free space


- Calculate uptime


	- Uptime = current time minus LastBootUpTime


	- Breaks down into days/hours/minutes and builds a readable string


- Calculate usage percentages


	- Memory used % = 100 - ((free / total) * 100)


	- Disk used % = 100 - ((free / total) * 100)


- Output + save


	- Prints the report with Write-Host


	- Saves the report with Out-File to System_Health_report.txt



## Key Takeaways (What I Learned)
- I can generate a complete “health snapshot” using only CIM queries.


- Uptime is easy to compute using LastBootUpTime from Win32_OperatingSystem.


- Percent used is more meaningful than raw values for quick health status checks.


- Exporting to TXT makes the output portable for troubleshooting logs or tickets.



## Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> .\script\system_health_report.ps1
System Health Report
--------------------
Generated on: 2026-02-25 20:24: 41

Computer Name: DESKTOP-SFB1UJM
OS Name: Microsoft Windows 11 Pro
OS Version: 10.0.26200

CPU Usage: 11%
Memory: 21.65 GB free of 31.93 GB
Disk (C:): 147.35 GB free of 931.40 GB

Usage Percentages:
Memory: 32.20% used
Disk (C:): 84.18% used
System Uptime: 47 minutes
```

## Validation Walkthrough
```powershell
# Run from the project folder
cd C:\Users\tech\Desktop\PlayGround
.\script\system_health_report.ps1

# Confirm the report file was created
gci .\System_Health_report.txt

# Review the saved report
Get-Content .\System_Health_report.txt

```
