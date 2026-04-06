# PowerShell Mini Project: Interactive File Inventory + Sorting (Array • Sort-Object • Validation)

This mini project prompts for a directory, collects file metadata (name, extension, size, last write time), then lets the user choose how to sort the output (field + ascending/descending). It prints a readable file-by-file report.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- User input with `Read-Host`
- Directory validation with `Test-Path -PathType Container`
- File enumeration using `Get-ChildItem -File`
- Selecting object properties with `Select-Object`
- Menu-driven choices + input casting (`[int](Read-Host ...)`)
- Mapping menu options to sort properties using `switch`
- Sorting objects with `Sort-Object -Descending:$descending`
- Iterating objects and printing structured output (`ForEach-Object`)

---

## Full Implementation

### Script: `file_info_array.ps1`

```powershell
$directory = Read-Host "Enter the directory"

if (-not (Test-Path -Path $directory -PathType Container)) {
    Write-Host "Directory not found: $directory"
    return
}

$fileInfo = Get-ChildItem -Path $directory -File -ErrorAction SilentlyContinue |
    Select-Object Name, Extension, Length, LastWriteTime

Write-Host "1. Name"
Write-Host "2. Extension"
Write-Host "3. Length"
Write-Host "4. Last Write Time"
$sortedBy = [int](Read-Host "Select sort field (1-4)")

$property = switch ($sortedBy) {
    1 { "Name" }
    2 { "Extension" }
    3 { "Length" }
    4 { "LastWriteTime" }
    default { Write-Host "Invalid option"; return }
}

Write-Host "1. Ascending"
Write-Host "2. Descending"
$sortOrder = [int](Read-Host "Select sort order (1-2)")

$descending = switch ($sortOrder) {
    1 { $false }
    2 { $true }
    default { Write-Host "Invalid option"; return }
}

$sorted = $fileInfo | Sort-Object -Property $property -Descending:$descending

$sorted | ForEach-Object {
    Write-Host "File: $($_.Name)"
    Write-Host "Extension: $($_.Extension)"
    Write-Host "Size: $($_.Length) bytes"
    Write-Host "Last Modified: $($_.LastWriteTime)"
    Write-Host ""
}
```
### Execution
```powershell
.\script\file_info_array.ps1
```

## Implementation Walkthrough
- Directory input + validation


	- Prompts for a directory path.


	- Uses Test-Path -PathType Container to ensure the folder exists.


- Collect file metadata


	- Get-ChildItem -File gathers only files (no folders).


	- Select-Object creates a clean object set with: Name, Extension, Length, LastWriteTime.


- Interactive sorting


	- User chooses a sort field (1–4).


	- A switch maps that choice to an actual property name.


	- User chooses sort order (ascending/descending).


	- Sort-Object -Descending:$descending applies the selection.


- Display report


	- Loops through each file and prints details in a consistent format.



## Key Takeaways (What I Learned)
- Scripts can be interactive and user-friendly using menus + Read-Host.


- switch is a clean way to map user choices to actual property names.


- Sorting objects is flexible when the sort property and direction are controlled by variables.


- Selecting only a few properties makes output clearer than dumping full FileInfo objects.



## Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> .\script\file_info_array.ps1
Enter the directory: C:\Users\tech\Desktop\PlayGround
1. Name
2. Extension
3. Length
4. Last Write Time
Select sort field (1-4): 1
1. Ascending
2. Descending
Select sort order (1-2): 2
File: TEM.txt
Extension: .txt
Size: 91 bytes
Last Modified: 02/24/2026 11:38:34

File: New Microsoft Word Document.docx
Extension: .docx
Size: 0 bytes
Last Modified: 02/26/2026 12:36:41
```

## Validation Walkthrough
```powershell
# Run the script
.\script\file_info_array.ps1

# Example inputs used in this run:
# Directory: C:\Users\tech\Desktop\PlayGround
# Sort field: 1 (Name)
# Sort order: 2 (Descending)
```
