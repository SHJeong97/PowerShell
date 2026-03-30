# PowerShell File Creation & Links Portfolio (New-Item • SymbolicLink • HardLink)

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- Creating folders and files with `New-Item`
- Verifying filesystem changes with `Get-ChildItem`
- Creating a **symbolic link** (directory shortcut) with `-ItemType SymbolicLink`
- Creating a **hard link** (second filename to the same file data) with `-ItemType HardLink`
- Understanding how links affect paths and file references

---

## Full Implementation

### 1) Create a new directory and a file inside it
```powershell
New-Item -Path .\Desktop\PlayGround -ItemType Directory
New-Item -Path .\Desktop\PlayGround\test.docx
```
### 2) Verify contents
```powershell
Get-ChildItem .\Desktop\PlayGround\
```
### 3) Create a directory symbolic link in Documents pointing to Desktop\PlayGround
```powershell
New-Item -ItemType SymbolicLink -Path ".\Documents\PlayGroud_ShortCut" -Target .\Desktop\PlayGround\
Get-ChildItem .\Documents\PlayGroud_ShortCut\
```
### 4) Create a hard link to an existing file
```powershell
New-Item -ItemType HardLink -Path ".\Documents\Hardlink_test.docx" -Target ".\Desktop\PlayGround\test.docx"
```

##  Implementation Walkthrough
### New-Item -ItemType Directory + New-Item <file>
- Creates a new folder (PlayGround) and a new file (test.docx).


- Your output shows test.docx created with Length 0 (empty file).


### Get-ChildItem
- Confirms what exists inside PlayGround, including timestamps and file sizes.


### Symbolic Link (directory link)
- New-Item -ItemType SymbolicLink ... -Target ...


- Creates a linked folder entry in Documents that points to the real folder on the Desktop.


- Listing the symlink path shows the same contents as the target directory.


### Hard Link (file link)
- New-Item -ItemType HardLink ... -Target ...


- Creates a second filename pointing to the same underlying file data as test.docx.


- Both paths refer to the same file content on disk (not a copy).



##  Key Takeaways (What I Learned)
- New-Item can create both “things” and “references to things.”
 I used it to create folders/files and also to create links.


- A symbolic link acts like a pointer to a folder path.
 My PlayGroud_ShortCut in Documents shows the same directory contents as Desktop\PlayGround.


- A hard link is not a shortcut—it’s another name for the same file.
 Hardlink_test.docx and test.docx refer to the same data, meaning changes would be reflected through either path.


- Get-ChildItem is my confirmation tool.
 I used it after each action to prove the filesystem change actually happened.



##  Results & Validation
```powershell
PS C:\Users\tech> New-Item -Path .\Desktop\PlayGround -ItemType Directory

PS C:\Users\tech> New-Item -Path .\Desktop\PlayGround\test.docx

   Directory: C:\Users\tech\Desktop\PlayGround

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2/24/2026   2:34 PM              0 test.docx

PS C:\Users\tech> Get-ChildItem .\Desktop\PlayGround\

   Directory: C:\Users\tech\Desktop\PlayGround

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          2/4/2026   9:41 AM          25135 New Microsoft Word Document.docx
-a----         1/30/2026   4:14 PM            173 New Text Document.txt
-a----         2/24/2026  11:38 AM             91 TEM.txt
-a----         2/24/2026   2:34 PM              0 test.docx
-a----         2/24/2026   2:31 PM              0 test_folder

PS C:\Users\tech> New-Item -ItemType SymbolicLink -Path ".\Documents\PlayGroud_ShortCut" -Target .\Desktop\PlayGround\

   Directory: C:\Users\tech\Documents

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----l         2/24/2026   2:47 PM                PlayGroud_ShortCut

PS C:\Users\tech> Get-ChildItem .\Documents\PlayGroud_ShortCut\

   Directory: C:\Users\tech\Documents\PlayGroud_ShortCut

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          2/4/2026   9:41 AM          25135 New Microsoft Word Document.docx
-a----         1/30/2026   4:14 PM            173 New Text Document.txt
-a----         2/24/2026  11:38 AM             91 TEM.txt
-a----         2/24/2026   2:34 PM              0 test.docx
-a----         2/24/2026   2:31 PM              0 test_folder

PS C:\Users\tech> New-Item -ItemType HardLink -Path ".\Documents\Hardlink_test.docx" -Target ".\Desktop\PlayGround\test.docx"

   Directory: C:\Users\tech\Documents

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2/24/2026   2:34 PM              0 Hardlink_test.docx
```

##  Validation Walkthrough
### To reproduce similar results:
```powershell
New-Item -Path .\Desktop\PlayGround -ItemType Directory
New-Item -Path .\Desktop\PlayGround\test.docx
Get-ChildItem .\Desktop\PlayGround\

New-Item -ItemType SymbolicLink -Path ".\Documents\PlayGroud_ShortCut" -Target .\Desktop\PlayGround\
Get-ChildItem .\Documents\PlayGroud_ShortCut\

New-Item -ItemType HardLink -Path ".\Documents\Hardlink_test.docx" -Target ".\Desktop\PlayGround\test.docx"
```
