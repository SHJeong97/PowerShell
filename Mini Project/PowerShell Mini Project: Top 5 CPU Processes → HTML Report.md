# PowerShell Mini Project: Top 5 CPU Processes → HTML Report

This mini project generates an **HTML report** showing the **top 5 processes by CPU** on a Windows machine using PowerShell.

Everything below is based on the script and commands I actually used.

---

## Skills Practiced

- PowerShell pipelines (`|`) and object sorting
- Process triage with `Get-Process`
- Output/report generation with `ConvertTo-Html` and `Out-File`
- Basic validation steps (`gci`, opening the output)

---

## Full Implementation

### Script: `Top5_CPU_Usage_to_HTML.ps1`

```powershell
#Change the directory
cd C:\Users\tech\Desktop\PlayGround

#Get today's Date
Get-Date

#Get Top 5 most CPU usage process, Conver to HTML Format, and create an output file
Get-Process | Sort-Object CPU -Descending | Select-Object -first 5 | ConvertTo-Html | Out-File Top5_CPU_Usage.html

#Check the file is Created
gci

#Check the content
.\Top5_CPU_Usage.HTML
```

### Execution
```powershell
.\script\Top5_CPU_Usage_to_HTML.ps1
```

##  Implementation Walkthrough
- cd C:\Users\tech\Desktop\PlayGround
 Sets the working directory so the report file is created in the intended folder.


- Get-Date
 Prints the current date/time (useful as a run timestamp in the console output).


- Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
 Pulls all processes, sorts by CPU time (highest first), then selects the top 5.


- ConvertTo-Html | Out-File Top5_CPU_Usage.html
 Converts those objects into an HTML table and writes it to Top5_CPU_Usage.html.


- gci
 Lists files in the directory to confirm the HTML report was created.


- .\Top5_CPU_Usage.HTML
 Attempts to open the HTML file directly from PowerShell using the default associated app (usually a browser).



##  Key Takeaways (What I Learned)
- A full report can be produced with one pipeline.
 Sorting + selecting + HTML conversion is fast and repeatable.


- PowerShell outputs objects that convert cleanly into reports.
 ConvertTo-Html turns structured process data into a readable table.


- Validation matters.
 gci confirms the file exists, and opening the file confirms it renders.



##  Results & Validation
Script file: Top5_CPU_Usage_to_HTML.ps1


Execution command:
```powershell
PS C:\Users\tech\Desktop\PlayGround> .\script\Top5_CPU_Usage_to_HTML.ps1
```

##  Validation Walkthrough
### Run the script, then confirm the output exists and opens:
```powershell
cd C:\Users\tech\Desktop\PlayGround
.\script\Top5_CPU_Usage_to_HTML.ps1
gci | Select-String -Pattern "Top5_CPU_Usage.html"  # optional quick check
.\Top5_CPU_Usage.html
```
