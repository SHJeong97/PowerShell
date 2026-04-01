# PowerShell Mini Project: Disk Space Checker v2 (Total/Free GB • % Free • Usage Summary)

This mini project calculates **total space**, **free space**, **used space**, and **% free** for the **C: drive**, then prints an alert message based on free-space percentage thresholds.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- Querying disk capacity with `Get-CimInstance` (`Win32_LogicalDisk`)
- Filtering data using both `-Filter` and `Where-Object`
- Unit conversion to GB (`/ 1GB`) and rounding (`[math]::Round`)
- Calculating derived metrics (used space and percentage free)
- Conditional alerting with `if / elseif / else`

---

## Full Implementation

### Script: `disk_space_checkerV2.ps1`

```powershell
# Get the totla space on the C drive (in GB), rounded to 2 decimal places
$totalSpace = [math]::round((Get-CimInstance -ClassName Win32_LogicalDisk -Filter "DeviceID='C:'").Size / 1GB, 2)

# Get the free space in GB on the C drive, rounded to 2 decimal places
$freeSpace = [math]::round((Get-CimInstance -ClassName Win32_LogicalDisk | Where-Object DeviceID -eq 'C:').FreeSpace / 1GB, 2)

# Calculate the Percentage of free Space
$FreePercentage = ($freeSpace / $totalSpace) * 100
$usedSpace = ($totalSpace - $freeSpace)

# Check free space and provice appropriate message
if ($FreePercentage -ge 75) {
    Write-Host "Planty Space left! You used $usedSpace GB, and You have ($FreePercentage% free)"
}
elseif ($FreePercentage -ge 50) {
    Write-Host "More than a half space left! You used $usedSpace GB, and You have ($FreePercentage% free)"
}
elseif ($FreePercentage -ge 25) {
    Write-Host "Quite of Space left! You used $usedSpace GB, and You have ($FreePercentage% free)"
}
elseif ($FreePercentage -ge 5) {
    Write-Host "Warning:You are running low on space! You used $usedSpace GB, and You have ($FreePercentage% free)"
}
else {
    Write-Host "Critical: Your space is almost Full! You used $usedSpace GB, and You have ($FreePercentage% free)"
}
```
### Execution
```
```powershell

.\Desktop\PlayGround\script\disk_space_checkerV2.ps1
```

##  Implementation Walkthrough
- Collect total disk size (GB)


	- Uses Get-CimInstance Win32_LogicalDisk -Filter "DeviceID='C:'" to target the C: drive directly.


	- Converts bytes to GB and rounds to 2 decimals.


- Collect free space (GB)


	- Uses Get-CimInstance Win32_LogicalDisk | Where-Object DeviceID -eq 'C:' to pick C: from all disks.


	- Converts bytes to GB and rounds to 2 decimals.


- Calculate usage


	- % free = (free / total) * 100


	- used = total - free


- Print message based on % free


	- ≥ 75% free → plenty


	- ≥ 50% free → more than half


	- ≥ 25% free → some space left


	- ≥ 5% free → warning


	- < 5% free → critical



##  Key Takeaways (What I Learned)
- CIM makes it easy to pull disk capacity data reliably from Windows.


- Percent-based thresholds are clearer than raw GB alone, since different drives have different sizes.


- Small math + branching logic can turn raw system data into a readable health message.



##  Results & Validation
```
```powershell

PS C:\Users\tech> .\Desktop\PlayGround\script\disk_space_checkerV2.ps1
More than a half space left! You used 216.68 GB, and You have (53.4151742523596% free)
```

##  Validation Walkthrough
```
```powershell

# Run the script
.\Desktop\PlayGround\script\disk_space_checkerV2.ps1

# Optional: confirm the raw disk values the script is using
Get-CimInstance -ClassName Win32_LogicalDisk -Filter "DeviceID='C:'" | Select-Object DeviceID, Size
```

