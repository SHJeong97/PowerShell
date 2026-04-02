# PowerShell Mini Project: App Data Folder + Config Generator (Read-Host • New-Item • Add-Content)

This mini project uses PowerShell to **prompt for an app name**, create a matching **data folder**, generate a **Config.txt**, and **write validation content** (including timestamp + last write time).  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- Using automatic variables and environment context (`$PWD`, `$Home`)
- Creating and using variables (`$server`, `$appName`, `$appDataDir`)
- Connectivity testing with variables (`Test-Connection -ComputerName $server`)
- Interactive input (`Read-Host`)
- Creating folders and files (`New-Item`)
- Writing and appending content (`Add-Content`)
- Validating results (`Get-ChildItem`, `Get-Content`, `Get-Item ... LastWriteTime`)

---

## Full Implementation

### Commands executed (console)
```powershell
$PWD
$Home
$server = "8.8.8.8"
Test-Connection -ComputerName $server -Count 1
```
##  Script: server_setup.ps1
```powershell
# Prompt the user for the application name
$appName = Read-Host "Enter the name of the application"

# Create the app datea directory name
$appDataDir = "${appName}_Data"

# Create a new directory
New-Item -Path $appDataDir -ItemType "Directory"

# Create a configuration text File and wirte Content
New-Item -Path "$appDataDir\Config.txt" -ItemType "File"

# Add content to "config.txt"
Add-Content -Path "$appDataDir\config.txt" -Value "Hello, this is a configuration file for the $appName app"
Add-Content -Path "$appDataDir\Config.txt" -Value "Current time: $(Get-Date)"
Add-Content -Path "$appDataDir\Config.txt" -Value "Last time of the modification: $((Get-Item "$appDataDir\Config.txt").LastWriteTime)"

# Verify that the directory was created
gci $appDataDir

# Verify that the contetn was added:
Get-Content -Path "$appDataDir\config.txt"
```
Execution
```powershell
.\Desktop\PlayGround\script\server_setup.ps1
```

##  Implementation Walkthrough
### Environment + variable use
- $PWD shows the current working directory as an object (Path).


- $Home shows the current user’s home directory path.


- $server = "8.8.8.8" stores a target in a variable.


- Test-Connection -ComputerName $server -Count 1 performs a one-packet ping-style test using that variable.


### Script behavior (server_setup.ps1)
- Prompts for an application name using Read-Host.


- Builds a folder name: <appName>_Data.


- Creates the folder and a Config.txt file.


- Writes three lines into the config:


	- 1.App-specific message


	- 2.Current time


	- 3.LastWriteTime pulled from the file metadata


- Validates results by listing the directory (gci) and printing file contents (Get-Content).



##  Key Takeaways (What I Learned)
- User input can drive automation.
 Read-Host lets a single script generate different folders/files depending on the input.


- New-Item + Add-Content is a simple pattern for configuration generation.
 Create a directory/file, then write structured lines into it.


- PowerShell makes it easy to embed command output into strings.
 $(Get-Date) and $((Get-Item ...).LastWriteTime) inject live values into the config file.


- Validation can be built into the script.
 Printing gci output and then Get-Content proves the script worked.



##  Results & Validation
### Console output (proof)
```powershell
PS C:\Users\tech\Desktop\PlayGround> $PWD

Path
----
C:\Users\tech\Desktop\PlayGround

PS C:\Users\tech\Desktop\PlayGround> $Home

PS C:\Users\tech\Desktop\PlayGround> $server ="8.8.8.8"

PS C:\Users\tech\Desktop\PlayGround> Test-Connection -ComputerName $server -Count 1

Source        Destination     IPV4Address      IPV6Address                              Bytes    Time(ms)
------        -----------     -----------      -----------                              -----    --------
ARTHUR        8.8.8.8         8.8.4.4                                                   32       3

PS C:\Users\tech> .\Desktop\PlayGround\script\server_setup.ps1
Enter the name of the application: test1

   Directory: C:\Users\tech
Mode   LastWriteTime         Length Name
----   -------------         ------ ----
d----- 2/25/2026 2:30 PM            test1_Data

   Directory: C:\Users\tech\test1_Data
Mode   LastWriteTime         Length Name
----   -------------         ------ ----
-a---- 2/25/2026 2:30 PM           142 Config.txt

Hello, this is a configuration file for the test1 app
Current time: 02/25/2026 14:30:16
Last time of the modification: 02/25/2026 14:30:16
```

##  Validation Walkthrough
```powershell
# 1) Optional: confirm location + test connectivity
$PWD
$server = "8.8.8.8"
Test-Connection -ComputerName $server -Count 1

# 2) Run the script and enter an app name when prompted
.\Desktop\PlayGround\script\server_setup.ps1

# 3) Confirm folder/file outputs (example app name: test1)
gci .\test1_Data
Get-Content .\test1_Data\Config.txt
```
