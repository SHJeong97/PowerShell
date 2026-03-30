# PowerShell Process Triage Portfolio (Sort by CPU • Name • WorkingSet)

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- Process enumeration with `Get-Process`
- Sorting objects by different properties (`CPU`, `Name`, `WorkingSet`)
- Quick triage using pipelines (`|`) and selecting top results (`Select-Object -First`)
- Interpreting process output fields (Handles, memory, CPU seconds, PID, session)

---

## Full Implementation

### 1) Top 5 processes by highest CPU time
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
```

### 2) Top 5 processes by name (descending alphabetical)
```powershell
Get-Process | Sort-Object Name -Descending | Select-Object -First 5
```
### 3) Bottom 5 processes by memory usage (WorkingSet)
```powershell
Get-Process | Sort-Object WorkingSet | Select-Object -First 5
```

## Implementation Walkthrough
### Sort by CPU (descending)
- Get-Process returns process objects.


- Sort-Object CPU -Descending ranks by CPU time used (higher first).


- Select-Object -First 5 keeps only the top five rows for quick triage.


### Sort by Name (descending)
- Sorting by name is useful for quick browsing and spotting patterns (services/apps).


- -Descending reverses the normal A→Z order.


### Sort by WorkingSet (ascending)
- WorkingSet is the process’s current physical memory usage.


- Sorting ascending and taking the first 5 shows the smallest-memory processes.



## Key Takeaways (What I Learned)
- PowerShell pipelines make triage fast.
 I can take a full process list and turn it into a ranked “top/bottom” view in one line.


- The sort property changes the story.
 Sorting by CPU highlights long-running/heavy processes, while WorkingSet focuses on memory footprint.


- Quick output is better than long output during troubleshooting.
 Select-Object -First 5 keeps results readable and actionable.


- Interpreting the columns matters.
 CPU(s) is cumulative CPU time, WS(K) is memory, and Id is the PID.



## Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> Get-Process | Sort-Object CPU -Descending | Select-Object -first 5

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   333      16     4056       9048  17,448.25   3012   0 svchost
  1125     231   365480     322768  10,023.95   4584   0 MsMpEng
  1844      53   409092     176112   5,107.48   4468   0 splunkd
  1020      50   248924     143564   1,967.59  12808   0 VMS_CFGS
   581      55    48924     102272   1,696.08  27264   3 chrome

PS C:\Users\tech\Desktop\PlayGround> Get-Process | Sort-Object Name -Descending | Select-Object -first 5

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   121      12     2392       6128       1.14   4076   0 WsidService
   354      21    15060      14868      15.83   4572   0 WorkflowAppControl
   483      24    13912      27232     468.91   8128   0 WmiPrvSE
   180      10     2972       9340       8.53  21496   0 WmiPrvSE
   288      14     3028      11604       0.22  27088   3 winlogon

PS C:\Users\tech\Desktop\PlayGround> Get-Process | Sort-Object WorkingSet | Select-Object -first 5

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
     0       0       60          8                 0   0 Idle
    53       3     1064       1104       0.22    412   0 smss
   296      16     2780       3100      13.98  19152   3 WebClientAppV5
   297      16     2768       3104      14.02  13956   3 WebClientAppP2PV5
    50       7     2028       4220       1.03    996   0 fontdrvhost
```

## Validation Walkthrough
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
Get-Process | Sort-Object Name -Descending | Select-Object -First 5
Get-Process | Sort-Object WorkingSet | Select-Object -First 5
```
