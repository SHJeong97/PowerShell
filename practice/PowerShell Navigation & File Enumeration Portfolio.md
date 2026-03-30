# PowerShell Navigation & File Enumeration Portfolio (Get-ChildItem • cd • Push/Pop-Location)

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- File and folder enumeration with `Get-ChildItem`
- Absolute vs relative paths
- Directory navigation (`Set-Location` / `cd`)
- Using the location stack (`Push-Location`, `Pop-Location`) to “bookmark” and return

---

## Full Implementation

### 1) List contents using an absolute path
```powershell
Get-ChildItem "C:\Users\tech\Desktop\extra\"
```
### 2) Move to Desktop and list contents using a relative path
```powershell
Set-Location .\Desktop\
Get-ChildItem .\extra\
```
### 3) Use location changes + location stack
```powershell
cd "C:\Users\tech\Desktop\extra\"
Push-Location
cd ..
cd ..
cd ..
cd ~
Pop-Location
```

## Implementation Walkthrough
### Get-ChildItem "C:\Users\tech\Desktop\extra\"
- Lists files and folders in the target directory.


- Output includes file mode, last write time, size, and name.


### Set-Location .\Desktop\ then Get-ChildItem .\extra\
- Changes current working directory to Desktop using a relative path.


- Lists the same folder (extra) using a relative path from Desktop.


### Push-Location and Pop-Location
- Push-Location saves your current location onto a stack (like a bookmark).


- After moving around with cd .. and cd ~, Pop-Location returns you to the saved location.


- Your final prompt shows you returned to: C:\Users\tech\Desktop\extra



## Key Takeaways (What I Learned)
- Absolute and relative paths both work for navigation and listing.
 I listed the same folder using the full path and using relative paths after moving directories.


- Get-ChildItem is the core cmdlet for quick file inventory.
 It provides a fast snapshot of what’s inside a directory (including timestamps and sizes).


- The location stack is useful for “temporary detours.”
 Push-Location + Pop-Location lets me jump away and reliably return without retyping long paths.



## Results & Validation
```powershell
PS C:\Users\tech> Get-ChildItem "C:\Users\tech\Desktop\extra\"

   Directory: C:\Users\tech\Desktop\extra

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         1/13/2026   4:30 PM                avery templates
d-----         8/25/2023   4:31 PM                led sign files
-a----         2/19/2020  12:31 PM        3153872 AnyDesk.exe
-a----         4/15/2025   3:42 PM           1957 AnyDesk.lnk
-a----         11/7/2023  12:51 PM            964 Brother iPrint&Scan.lnk
-a----         5/24/2023   2:03 PM            877 DiskGenius.lnk
-a----         2/21/2023  11:46 AM           1208 GOM Player.lnk
-a----         5/18/2023   2:06 PM           2206 Google Chrome.lnk
-a----         10/3/2023   4:49 PM           1580 IE.lnk
-a----         5/15/2023   9:06 AM           2276 Microsoft Edge.lnk
-a----         4/26/2023   3:26 PM           9643 NVR and DVR list.xlsx
-a----          5/2/2023  12:54 PM            953 OpenVPN GUI.lnk
-a----         4/26/2023   3:27 PM         233376 P-N9000-20210120&20220914-Latest-Firmware-for-DVR&NVRUI1-A.xlsx
-a----         8/25/2023   4:24 PM        2974792 PL2303LegacyUpdaterSetup-1.1.0.exe

PS C:\Users\tech> Set-Location .\Desktop\
PS C:\Users\tech\Desktop> Get-ChildItem .\extra\

   Directory: C:\Users\tech\Desktop\extra
... (same directory listing)

PS C:\Users\tech\Desktop\extra> Push-Location
PS C:\Users\tech\Desktop\extra> cd ..
PS C:\Users\tech\Desktop> cd ..
PS C:\Users\tech> cd ..
PS C:\Users> cd ~
PS C:\Users\tech> Pop-Location
PS C:\Users\tech\Desktop\extra>
```

## Validation Walkthrough
### To reproduce similar output:
```powershell
Get-ChildItem "C:\Users\tech\Desktop\extra\"
Set-Location .\Desktop\
Get-ChildItem .\extra\

cd "C:\Users\tech\Desktop\extra\"
Push-Location
cd ..
cd ..
cd ..
cd ~
Pop-Location
```
