# PowerShell Mini Project: Disk Space Checker (C: • CIM • Conditional Alerts)

This mini project checks **free disk space on the C: drive** (in GB) and prints an alert message based on thresholds.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- Querying system info with `Get-CimInstance` (Win32_LogicalDisk)
- Filtering objects with `Where-Object`
- Unit conversion to GB (`/ 1GB`)
- Conditional logic (`if / elseif / else`)
- Creating simple status/alert messages with `Write-Host`

---

## Full Implementation

### Script: `disk_space_checker.ps1`

```powershell
# Get the free space in GB on the C drive
$freeSpace = (Get-CimInstance -ClassName Win32_LogicalDisk | Where-Object DeviceID -eq 'C:').FreeSpace / 1GB

# Check free space and provice appropriate message
if ($freeSpace -gt 50) {
    Write-Host "Planty Space left! You have $freespace GB space"
}
elseif ($freeSpace -gt 20) {
    Write-Host "You have $freespace GB space available"
}
elseif ($freeSpace -gt 5) {
    Write-Host "Warning:You are running low on space! You have $freespace GB space"
}
else {
    Write-Host "Critical: Your space is almost Full! You have $freespace GB space"
}
```
### Execution
```powershell
.\Desktop\PlayGround\script\disk_space_checker.ps1
```

##  Implementation Walkthrough
- Collect free space


	- Get-CimInstance -ClassName Win32_LogicalDisk pulls disk objects.


	- Where-Object DeviceID -eq 'C:' selects the C: drive.


	- .FreeSpace / 1GB converts bytes into GB.


- Choose a message based on thresholds


	- > 50→ “"Planty Space left!”


	- > 20 → normal notice


	- > 5 → warning


	- otherwise → critical



##  Key Takeaways (What I Learned)
- CIM is a clean way to query Windows disk information without extra tools.


- PowerShell makes unit conversion simple (bytes → GB using 1GB).


- Threshold-based messages are an easy first step toward automated monitoring.



##  Results & Validation
```powershell
PS C:\Users\tech> .\Desktop\PlayGround\script\disk_space_checker.ps1
More than a half space available! You have 248.4700050354 GB space
```

##  Validation Walkthrough
```powershell
# Run the script
.\Desktop\PlayGround\script\disk_space_checker.ps1

# Optional: view the raw disk object used
Get-CimInstance -ClassName Win32_LogicalDisk | Where-Object DeviceID -eq 'C:' | Select-Object DeviceID, FreeSpace, Size
```

