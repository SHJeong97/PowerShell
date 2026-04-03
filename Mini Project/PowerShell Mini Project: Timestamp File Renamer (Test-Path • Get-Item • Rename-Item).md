# PowerShell Mini Project: Timestamp File Renamer (Test-Path • Get-Item • Rename-Item)

This mini project prompts for a filename, checks if it exists, extracts the file’s **CreationTime**, and renames the file by appending a **yyyyMMdd** timestamp to the base name.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- User input with `Read-Host`
- File existence validation with `Test-Path`
- Retrieving file metadata with `Get-Item` (FileInfo object)
- Formatting dates with `.ToString("yyyyMMdd")`
- Building a new filename using `BaseName` + `Extension`
- Renaming files safely with `Rename-Item`
- Clear status messaging with `Write-Host`

---

## Full Implementation

### Script: `file_renamer.ps1`

```powershell
# Prompt the user for a filename to change the name
$fileName = Read-Host "Enter the name of the file you want to rename (including extension)"

#Check the file exists
if (Test-Path $fileName) {
    Write-Host "Found the file! Preparing to rename..."

    # Retrieve the file's object
    $fileInfo = Get-Item $fileName
    $timestamp = $fileInfo.CreationTime.ToString("yyyyMMdd")

    $newFileName = "$($fileInfo.BaseName)_$($timestamp)$($fileInfo.Extension)"
    
    # Rename the File
    Rename-Item $fileName $newFileName

    # Inform the user of successful
    Write-Host "File successfully renamed from $($fileInfo.name) to $newFileName"

}
else {
    Write-Host "Error $fileName does not exist in the current directory"
    exit
}
```
### Execution
```powershell
.\script\file_renamer.ps1
```

##  Implementation Walkthrough
- Prompt for file name


	- The script asks for the exact filename (including extension).


- Validate the file exists


	- Test-Path $fileName ensures the file is present in the current directory before doing anything.


- Pull metadata and build the new name


	- Get-Item returns a FileInfo object.


	- CreationTime.ToString("yyyyMMdd") produces a timestamp like 20260113.


	- The new name is assembled as:
 <BaseName>_<yyyyMMdd><Extension>


- Rename and report


	- Rename-Item applies the rename.


	- The script prints a confirmation message with the old and new names.



##  Key Takeaways (What I Learned)
- File renaming can be automated consistently with metadata.
 Using CreationTime creates a standard naming format.


- Validating input prevents errors.
 Test-Path ensures the script only runs when the file exists.


- Using BaseName and Extension avoids filename mistakes.
 It keeps the extension intact while changing only the base name.



##  Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> .\script\file_renamer.ps1
Enter the name of the file you want to rename (including extension): New Microsoft Word Document.docx
Found the file! Preparing to rename...
File successfully renamed from New Microsoft Word Document.docx to New Microsoft Word Document_20260113.docx
```

##  Validation Walkthrough
```powershell
# 1) Go to the directory containing the target file
cd C:\Users\tech\Desktop\PlayGround

# 2) Run the script
.\script\file_renamer.ps1

# 3) When prompted, enter a filename that exists, e.g.
# New Microsoft Word Document.docx

# 4) Confirm the rename
gci
```
