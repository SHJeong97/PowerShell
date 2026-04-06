# PowerShell Automation Project: CHKDSK scan with Input Validation + Timestamped Logs

This project runs `chkdsk <drive> /scan`, captures the raw output, interprets the exit code, and writes a timestamped scan report.  
It also creates separate logs for **input errors** (invalid drive) and **runtime errors** (non-zero exit code or exceptions).

Everything below is based on the script you provided. (No execution output was included in the message.)

---

## Skills Practiced

- Advanced parameters with `[CmdletBinding()]` + validation (`[ValidatePattern()]`)
- Defensive scripting (`Set-StrictMode`, `$ErrorActionPreference = 'Stop'`)
- Reliable path handling using `$MyInvocation.MyCommand.Path`
- Creating timestamped log files with `Join-Path` and `New-Item`
- Input normalization (accept `C` or `C:` and normalize to `C:`)
- Drive existence validation (`Get-PSDrive`)
- Running native commands and capturing stderr (`2>&1`) + reading `$LASTEXITCODE`
- Exit-code interpretation with `switch`
- Structured logging for input and runtime errors (`Add-Content`, here-strings)

---

## Full Implementation

### Script: `chkdsk_scan_report.ps1` (example name)

```powershell
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [ValidatePattern("^[A-Za-z]:?$")]  # allows C or C:
    [string]$DriveLetter
)

Set-StrictMode -Version Latest
$ErrorActionPreference = 'Stop'

# Reliable script directory
$scriptPath = $MyInvocation.MyCommand.Path
$scriptDir  = if ($scriptPath) { Split-Path -Path $scriptPath -Parent } else { (Get-Location).Path }

# Timestamped logs (create FIRST)
$timestamp  = Get-Date -Format "yyyyMMdd_HHmmss"
$runTime    = Get-Date
$logFolder  = Join-Path $scriptDir "Logs"
New-Item -Path $logFolder -ItemType Directory -Force | Out-Null

$scanLogPath  = Join-Path $logFolder "${timestamp}_CHKDSK_ScanReport.txt"
$inputLogPath = Join-Path $logFolder "${timestamp}_CHKDSK_InputError.log"
$errorLogPath = Join-Path $logFolder "${timestamp}_CHKDSK_RuntimeError.log"

New-Item -Path $scanLogPath  -ItemType File -Force | Out-Null
New-Item -Path $inputLogPath -ItemType File -Force | Out-Null
New-Item -Path $errorLogPath -ItemType File -Force | Out-Null

function Write-InputErrorLog {
    param([Parameter(Mandatory)][string]$Message)
    Add-Content -Path $inputLogPath -Value @"
==============================
INPUT ERROR
Time:  $(Get-Date)
Input: $DriveLetter
Error: $Message
==============================

"@
}

function Write-RuntimeErrorLog {
    param([Parameter(Mandatory)][string]$Message)
    Add-Content -Path $errorLogPath -Value @"
==============================
RUNTIME ERROR
Time:  $(Get-Date)
Drive: $DriveLetter
Error: $Message
==============================

"@
}

# Normalize (c -> C:)
$DriveLetter = $DriveLetter.Trim()
if ($DriveLetter -match '^[A-Za-z]$') {
    $DriveLetter = "$DriveLetter`:"
}
$DriveLetter = $DriveLetter.ToUpper()

# Validate drive exists
if (-not (Get-PSDrive -Name $DriveLetter.TrimEnd(':') -ErrorAction SilentlyContinue)) {
    Write-Host "Drive does not exist: $DriveLetter" -ForegroundColor Red
    Write-InputErrorLog "Drive does not exist on this system."
    Write-Host "Input error log saved to: $inputLogPath" -ForegroundColor Cyan
    return
}

Write-Host "Running CHKDSK on drive $DriveLetter. This may take some time..." -ForegroundColor Yellow

try {
    $chkdskOutput = chkdsk $DriveLetter /scan 2>&1
    $exitCode = $LASTEXITCODE

    $interpretation = switch ($exitCode) {
        0 { "No errors found. Disk is healthy." }
        1 { "Errors were found and corrected." }
        2 { "Disk cleanup performed." }
        3 { "Scan completed with warnings/minor issues." }
        4 { "Errors found but not fully fixed. Further action required." }
        default { "Unexpected exit code. Review output." }
    }

    @"
==============================
        CHKDSK Scan Report
==============================
Report Created: $runTime
Drive Scanned:  $DriveLetter
Exit Code:      $exitCode
Interpretation: $interpretation

----- Raw CHKDSK Output -----
$chkdskOutput
"@ | Out-File -FilePath $scanLogPath -Encoding UTF8

    Write-Host "Scan report saved to: $scanLogPath" -ForegroundColor Cyan

    if ($exitCode -ne 0) {
        Write-RuntimeErrorLog "CHKDSK returned exit code $exitCode. Interpretation: $interpretation"
        Write-Host "Runtime error log saved to: $errorLogPath" -ForegroundColor Cyan
    }
}
catch {
    Write-RuntimeErrorLog $_.Exception.Message
    Write-Host "Fatal error. See runtime error log: $errorLogPath" -ForegroundColor Red
}
```
##  Implementation Walkthrough
- Input validation + normalization


	- Accepts C or C: using [ValidatePattern("^[A-Za-z]:?$")]


	- Normalizes to uppercase C: for consistent use.


- Logging first


	- Creates a Logs folder next to the script and three timestamped log files:


		- Scan report


		- Input error log


	- Runtime error log


- Pre-check: drive exists


	- Uses Get-PSDrive to confirm the drive is present.


	- If missing, writes an input error log and exits cleanly.


- Run CHKDSK and interpret


	- Executes chkdsk <drive> /scan and captures output (2>&1).


	- Reads $LASTEXITCODE and maps it to a human message via switch.


- Write report + errors


	- Writes a full report including:


		- drive scanned, time, exit code, interpretation, raw output.


	- If exit code isn’t 0, writes a runtime error log entry.



##  Key Takeaways (What I Learned)
- Validating input prevents dangerous or confusing failures before running disk tools.


- Creating logs first makes scheduled/automated runs diagnosable even if the scan fails.


- Native command exit codes can be translated into friendly summaries for fast interpretation.


- Separating input errors from runtime errors makes troubleshooting clearer.



##  Results & Validation
(No execution output was provided in the message.)
 Expected artifacts created by the script run:
- .\Logs\<timestamp>_CHKDSK_ScanReport.txt


- .\Logs\<timestamp>_CHKDSK_InputError.log (only when input is invalid / drive missing)


- .\Logs\<timestamp>_CHKDSK_RuntimeError.log (when exit code != 0 or exceptions occur)



##  Validation Walkthrough
```powershell
# Example usage (valid drive)
.\chkdsk_scan_report.ps1 -DriveLetter C

# Or with colon
.\chkdsk_scan_report.ps1 -DriveLetter C:

# Confirm logs were created (from the script folder)
Get-ChildItem .\Logs | Sort-Object LastWriteTime -Descending | Select-Object -First 10

# View the latest scan report
Get-ChildItem .\Logs\*_CHKDSK_ScanReport.txt | Sort-Object LastWriteTime -Descending | Select-Object -First 1 | ForEach-Object { Get-Content $_.FullName -TotalCount 60 }
```

