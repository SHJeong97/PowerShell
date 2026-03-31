# PowerShell Triage Reporting Portfolio (Services • Processes • Exporting • Grouping • Measuring)

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- Service inventory and formatting (`Get-Service`, `Format-Table`)
- Process triage by CPU/memory (`Get-Process`, `Sort-Object`, `Select-Object`)
- Output formatting (`Format-Table`, `Format-List`)
- Visual review (`Out-GridView`)
- Exporting results to files (`Out-File`, `Tee-Object`, `ConvertTo-Html`)
- File inventory and categorization (`Get-ChildItem`, `Group-Object Extension`)
- Basic statistics and summarization (`Measure-Object`)

---

## Full Implementation

### 1) List services (Name, Status, StartType)
```powershell
Get-Service | Format-Table Name, Status, StartType
```

### 2) Show low-memory processes (WorkingSet) and format as a table
```powershell
Get-Process | Sort-Object WorkingSet | Select-Object -First 5 | Format-Table Name, Id, CPU
```
### 3) Show top CPU processes and format as a list
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Format-List Name, Id, CPU
```
### 4) Visualize results with GridView
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Out-GridView
```
### 5) Export process output to TXT
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Out-File "CPU_Usage.txt"
```
### 6) Duplicate output to file while also displaying (Tee-Object) + GridView
```powershell
Get-Process | Tee-Object -FilePath .\CPU_Usage.txt | Out-GridView
```
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Tee-Object -FilePath ".\CPU_Usage1.txt" | Out-GridView
```
### 7) Export top 5 CPU processes as HTML
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | ConvertTo-Html | Out-File Top5_CPU_Usage.html
```
### 8) Group files by extension and summarize
```powershell
gci | Group-Object Extension
```
### 9) Measure CPU stats (top 5) and file size stats (directory)
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Measure-Object CPU -Sum -Average -Maximum -Minimum
gci | Measure-Object Length -Sum -Average -Maximum -Minimum
```

##  Implementation Walkthrough
### Services inventory
- Get-Service returns service objects.


- Format-Table Name, Status, StartType creates a readable service snapshot.


### Process triage (memory + CPU)
- Sorting by WorkingSet helps identify small-footprint processes (or confirm baseline).


- Sorting by CPU (descending) highlights the processes consuming the most CPU time.


### Output methods
- Format-Table vs Format-List changes readability depending on how many fields you want to see.


- Out-GridView opens an interactive window for quick filtering/sorting visually.


### Exporting reports
- Out-File writes output to disk (you created CPU_Usage.txt).


- Tee-Object writes to disk and continues the pipeline (useful for “save + view” workflows).


- ConvertTo-Html | Out-File creates a shareable HTML report.


### Folder insights
- Group-Object Extension quickly shows how many files exist per extension type.


- Measure-Object gives basic statistics (sum/avg/min/max) for CPU values and file sizes.



##  Key Takeaways (What I Learned)
- PowerShell can generate quick triage reports in one-liners.
 Services and processes can be listed, filtered, and formatted without extra tools.


- Choosing the right output format matters.
 Format-Table is compact; Format-List is better for detail-per-item.


- GridView is great for interactive review.
 Out-GridView is a fast way to visually inspect results during troubleshooting.


- Exporting is easy—and flexible.
 I saved output as TXT and HTML, and used Tee-Object to save while still viewing.


- Basic stats help you summarize quickly.
 Measure-Object let me calculate CPU totals/average and file size summary for the directory.



##  Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> Get-Service | Format-Table name, Status, StartType
Name                                         Status StartType
----                                         ------ ---------
AarSvc_4f469ce4                             Stopped    Manual
AIPluginService                             Running Automatic
...

PS C:\Users\tech\Desktop\PlayGround> Get-Process | Sort-Object WorkingSet | Select-Object -first 5 | Format-Table Name, ID, CPU
Name                 Id CPU
----                 -- ---
Idle                  0
smss                412 0.21875
...

PS C:\Users\tech\Desktop\PlayGround> Get-Process | Sort-Object CPU -Descending | Select-Object -first 5 | Format-List Name, ID, CPU
Name : svchost
Id   : 3012
CPU  : 17656.6875
...

PS C:\Users\tech\Desktop\PlayGround> Get-Process | Sort-Object CPU -Descending | Select-Object -first 5 | Out-File "CPU_Usage.txt"

PS C:\Users\tech\Desktop\PlayGround> gci
... CPU_Usage.txt
... CPU_Usage1.txt
... Top5_CPU_Usage.html

PS C:\Users\tech\Desktop\PlayGround> gci | Group-Object Extension
Count Name   Group
----- ----   -----
2           {test1, test2}
4 .txt      {CPU_Usage.txt, CPU_Usage1.txt, New Text Document.txt, TEM.txt}
1 .docx     {New Microsoft Word Document.docx}
1 .html     {Top5_CPU_Usage.html}

PS C:\Users\tech\Desktop\PlayGround> Get-Process | Sort-Object CPU -Descending | Select-Object -first 5 | Measure-Object CPU -Sum -Average -Maximum -Minimum
Count    : 5
Average  : 7529.009375
Sum      : 37645.046875
Maximum  : 17905.765625
Minimum  : 1928.8125

PS C:\Users\tech\Desktop\PlayGround> Gci | Measure-Object Length -Sum -Average -Maximum -Minimum
Count    : 6
Average  : 18576.1666666667
Sum      : 111457
Maximum  : 67774
Minimum  : 91
```

##  Validation Walkthrough
```powershell
Get-Service | Format-Table Name, Status, StartType

Get-Process | Sort-Object WorkingSet | Select-Object -First 5 | Format-Table Name, Id, CPU
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Format-List Name, Id, CPU
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Out-GridView

Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Out-File "CPU_Usage.txt"
Get-Process | Tee-Object -FilePath .\CPU_Usage.txt | Out-GridView
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Tee-Object -FilePath ".\CPU_Usage1.txt" | Out-GridView

Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | ConvertTo-Html | Out-File Top5_CPU_Usage.html

gci | Group-Object Extension
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 | Measure-Object CPU -Sum -Average -Maximum -Minimum
gci | Measure-Object Length -Sum -Average -Maximum -Minimum
```
