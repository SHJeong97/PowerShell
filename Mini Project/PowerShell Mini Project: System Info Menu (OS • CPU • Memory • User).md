# PowerShell Mini Project: System Info Menu (OS • CPU • Memory • User)

This mini project provides an interactive menu to display key system information:
1) Operating System  
2) CPU  
3) Total Memory  
4) Current User  

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- Interactive menus with `Write-Host`
- User input with `Read-Host`
- Branching with `switch`
- System inventory using CIM/WMI classes:
  - `Win32_OperatingSystem`
  - `Win32_Processor`
  - `Win32_ComputerSystem`
- Unit conversion and rounding (`/ 1GB`, `[math]::Round`)
- Reading environment variables (`$env:USERNAME`)

---

## Full Implementation

### Script: `systemInfo.ps1`

```powershell
# Display menu options
Write-Host "What system information would you like to see?"
Write-Host "1. Operating System"
Write-Host "2. CPU"
Write-Host "3. Total Memory"
Write-Host "4. Current User"

# Get user input
$user_choice = Read-Host "Enter your chocie (1 - 4)"

# Switch statement to handle user input
switch ($user_choice) {
    "1" {
        # Retrieve Operating System information
        $os = Get-CimInstance Win32_OperatingSystem
        $os_name = $os.Caption
        $os_version = $os.Version
        Write-Host "Operating System: $os_name"
        Write-Host "Version: $os_version"
      }
    "2" {
        $cpu = Get-CimInstance Win32_Processor
        $cpu_name = $cpu.Name
        $cpu_cores = $cpu.NumberOfCores
        $cpu_speed = $cpu.MaxClockSpeed
        Write-Host "CPU: $cpu_name"
        Write-Host "Number of CPU cores: $cpu_cores"
        Write-Host "Max CPU Clock Speed: $($cpu_speed/1000) GHz" # covert to MHz to GHz
    }
    "3" {
        $totalMmoryGB = [math]::Round((Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory / 1GB, 2)
        Write-Host "Total Physical Memory: $totalMmoryGB GB"
    }
    "4"{
        # Display current user
        $currentUser = $env:USERNAME
        Write-Host "Current User: $currentUser"
    }
    Default {
        # Invalid Selection
        Write-Host "Invalid choice. Please run the script again."
    }
}
```
### Execution
```powershell
.\Desktop\PlayGround\script\systemInfo.ps1
```

##  Implementation Walkthrough
- The script prints a menu and reads a selection (1–4).


- A switch statement runs the correct block:


### Option 1 — Operating System
- Uses Get-CimInstance Win32_OperatingSystem


- Displays OS name (Caption) and OS version (Version)


### Option 2 — CPU
- Uses Get-CimInstance Win32_Processor


- Displays CPU name, number of cores, and max clock speed


- Converts MHz → GHz by dividing by 1000


### Option 3 — Total Memory
- Uses Get-CimInstance Win32_ComputerSystem


- Converts bytes → GB and rounds to 2 decimals


### Option 4 — Current User
- Displays the username from $env:USERNAME



##  Key Takeaways (What I Learned)
- CIM queries are a reliable way to pull system inventory (OS/CPU/Memory) without extra tools.


- Menus + switch statements make scripts user-friendly and reusable.


- PowerShell makes unit conversion easy (bytes to GB using 1GB, and MHz to GHz with math).


- Environment variables are quick for user context, like the currently logged-in account.



##  Results & Validation
```powershell
PS C:\Users\tech> .\Desktop\PlayGround\script\systemInfo.ps1
What system information would you like to see?
1. Operating System
2. CPU
3. Total Memory
4. Current User
Enter your chocie (1 - 4): 1
Operating System: Microsoft Windows 10 Home N
Version: 10.0.19045

PS C:\Users\tech> .\Desktop\PlayGround\script\systemInfo.ps1
What system information would you like to see?
1. Operating System
2. CPU
3. Total Memory
4. Current User
Enter your chocie (1 - 4): 2
CPU: Intel(R) Core(TM) i5-3330S CPU @ 2.70GHz
Number of CPU cores: 4
Max CPU Clock Speed: 2.701 GHz
```

##  Validation Walkthrough
```powershell
# Run the script
.\Desktop\PlayGround\script\systemInfo.ps1

# When prompted, test options you want to validate:
# 1 = OS
# 2 = CPU
# 3 = Memory
# 4 = Current User
```
