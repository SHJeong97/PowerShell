# PowerShell File Content & FileOps Portfolio (Get/Set/Add-Content • Move/Copy/Rename/Remove • Compress/Expand)

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- Reading file content (full file, first N lines, last N lines)
- Writing and appending to text files
- Core file operations: move, copy, rename, delete
- Creating and updating ZIP archives
- Restoring files from an archive (expand/extract)

---

## Full Implementation

### 1) Read file contents (all, head, tail)
```powershell
Get-Content '.\Desktop\PlayGround\New Text Document.txt'
Get-Content '.\Desktop\PlayGround\New Text Document.txt' -TotalCount 2
Get-Content '.\Desktop\PlayGround\New Text Document.txt' -Tail 2
```
### 2) Write / append to a text file
```powershell
cd .\Desktop\PlayGround\

Set-Content test1.txt            # prompted because -Value wasn’t provided
Set-Content -Path test1.txt -Value "Hello, Powershell"
Get-Content -Path .\test1.txt

Add-Content -Path .\test1.txt -Value "Additional Line"
Get-Content -Path .\test1.txt
```
### 3) Move, copy, rename, and delete files
```powershell
Move-Item -Path .\test1\my_secret.txt -Destination .\test2\
Get-ChildItem -Path .\test2\

Copy-Item -Path .\test2\my_secret.txt -Destination .\test1\
Get-ChildItem -Path .\test1\

Copy-Item -Path .\test2\my_secret.txt -Destination .\test1\my_new_secret.txt
Get-ChildItem -Path .\test1\

Rename-Item -Path .\test1\my_secret.txt -NewName my_old_secret.txt
gci -Path .\test1\

Remove-Item -Path .\test1\my_old_secret.txt -Recurse
gci -Path .\test1\
```
### 4) Create/update a ZIP archive and restore files
```powershell
Compress-Archive -Path .\test1\my_new_secret.txt -DestinationPath .\test1\Secret.zip
gci .\test1\

Compress-Archive -Path .\test1\My_Friends_Secret.txt -DestinationPath .\test1\Secret.zip -Update
gci .\test1\

Remove-Item -Path .\test1\My_Friends_Secret.txt
Remove-Item -Path .\test1\my_new_secret.txt
gci .\test1\

Expand-Archive -Path .\test1\Secret.zip -DestinationPath .\test1\
gci .\test1\
```

## Implementation Walkthrough
### Reading content with Get-Content
- Get-Content <path> reads the whole file line-by-line.


- -TotalCount 2 returns only the first 2 lines (quick “head” preview).


- -Tail 2 returns only the last 2 lines (quick “tail” preview).


### Writing content with Set-Content and Add-Content
- Running Set-Content test1.txt without -Value causes PowerShell to prompt you for Value[0].


- Set-Content -Value ... writes/replaces the file content.


- Add-Content -Value ... appends new lines to the end of the file.


### File operations: move/copy/rename/remove
- Move-Item relocates a file to another folder.


- Copy-Item duplicates the file (optionally with a new name).


- Rename-Item changes a file’s name in place.


- Remove-Item deletes the file; you verified changes using Get-ChildItem / gci.


### Archiving: Compress-Archive and Expand-Archive
- Compress-Archive creates a ZIP from the specified file(s).


- -Update adds/updates files in an existing ZIP.


- After deleting the originals, Expand-Archive restored them from Secret.zip.



## Key Takeaways (What I Learned)
- PowerShell makes file previews easy without opening editors.
 -TotalCount and -Tail are fast ways to verify the beginning/end of a file.


- Set-Content overwrites, Add-Content appends.
 This is a common pattern for logging and report-building scripts.


- Verification matters after every file change.
 I repeatedly used Get-ChildItem (gci) to confirm moves/copies/renames/deletes.


- Archives can be used as a simple backup/restore workflow.
 I zipped files, removed originals, then restored them using Expand-Archive.



## Results & Validation
```powershell
PS C:\Users\tech> Get-Content '.\Desktop\PlayGround\New Text Document.txt'
https://www.cve.org/
https://www.exploit-db.com/
https://www.shodan.io/
https://search.censys.io/
https://www.virustotal.com/gui/home/upload
https://haveibeenpwned.com/

PS C:\Users\tech> Get-Content '.\Desktop\PlayGround\New Text Document.txt' -TotalCount 2
https://www.cve.org/
https://www.exploit-db.com/

PS C:\Users\tech> Get-Content '.\Desktop\PlayGround\New Text Document.txt' -Tail 2
https://www.virustotal.com/gui/home/upload
https://haveibeenpwned.com/

PS C:\Users\tech\Desktop\PlayGround> Set-Content -Path test1.txt -Value "Hello, Powershell"
PS C:\Users\tech\Desktop\PlayGround> Get-Content -Path .\test1.txt
Hello, Powershell

PS C:\Users\tech\Desktop\PlayGround> Add-Content -Path .\test1.txt -Value "Additional Line"
PS C:\Users\tech\Desktop\PlayGround> Get-Content -Path .\test1.txt
Hello, Powershell
Additional Line

PS C:\Users\tech\Desktop\PlayGround> Move-Item -Path .\test1\my_secret.txt -Destination .\test2\
PS C:\Users\tech\Desktop\PlayGround> Get-ChildItem -Path .\test2\
... my_secret.txt

PS C:\Users\tech\Desktop\PlayGround> Copy-Item -Path .\test2\my_secret.txt -Destination .\test1\my_new_secret.txt
PS C:\Users\tech\Desktop\PlayGround> Rename-Item -Path .\test1\my_secret.txt -NewName my_old_secret.txt
PS C:\Users\tech\Desktop\PlayGround> Remove-Item -Path .\test1\my_old_secret.txt -Recurse

PS C:\Users\tech\Desktop\PlayGround> Compress-Archive -Path .\test1\my_new_secret.txt -DestinationPath .\test1\Secret.zip
PS C:\Users\tech\Desktop\PlayGround> Compress-Archive -Path .\test1\My_Friends_Secret.txt -DestinationPath .\test1\Secret.zip -Update

PS C:\Users\tech\Desktop\PlayGround> Remove-Item -Path .\test1\My_Friends_Secret.txt
PS C:\Users\tech\Desktop\PlayGround> Remove-Item -Path .\test1\my_new_secret.txt

PS C:\Users\tech\Desktop\PlayGround> Expand-Archive -Path .\test1\Secret.zip -DestinationPath .\test1\
```

## Validation Walkthrough
### To reproduce similar results:
```powershell
Get-Content '.\Desktop\PlayGround\New Text Document.txt'
Get-Content '.\Desktop\PlayGround\New Text Document.txt' -TotalCount 2
Get-Content '.\Desktop\PlayGround\New Text Document.txt' -Tail 2

cd .\Desktop\PlayGround\
Set-Content -Path test1.txt -Value "Hello, Powershell"
Add-Content -Path .\test1.txt -Value "Additional Line"
Get-Content -Path .\test1.txt

# Move/copy/rename/delete (requires your test folders/files to exist)
Move-Item -Path .\test1\my_secret.txt -Destination .\test2\
Copy-Item -Path .\test2\my_secret.txt -Destination .\test1\my_new_secret.txt
Rename-Item -Path .\test1\my_secret.txt -NewName my_old_secret.txt
Remove-Item -Path .\test1\my_old_secret.txt -Recurse

# Archive workflow
Compress-Archive -Path .\test1\my_new_secret.txt -DestinationPath .\test1\Secret.zip
Compress-Archive -Path .\test1\My_Friends_Secret.txt -DestinationPath .\test1\Secret.zip -Update
Expand-Archive -Path .\test1\Secret.zip -DestinationPath .\test1\
```
