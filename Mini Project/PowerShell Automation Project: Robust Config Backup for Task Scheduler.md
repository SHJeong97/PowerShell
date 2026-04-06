# PowerShell Automation Project: Robust Config Backup for Task Scheduler (Logging • Transcript • Fallback Copy)

This project is a PowerShell 5.1 script designed to run reliably via **Task Scheduler**.  
It validates paths, creates required folders/files, backs up a configuration file with a timestamped name, writes structured logs, captures a transcript, and returns clear exit codes for success/failure.

Everything below is based on the script and Task Scheduler arguments you provided.

---

## Skills Practiced

- Advanced script structure: `#Requires`, `[CmdletBinding()]`, `param()`, validation attributes
- Defensive scripting: `Set-StrictMode`, `$ErrorActionPreference = 'Stop'`
- Idempotent setup helpers (ensure folder/file exists)
- Structured logging (INFO/WARN/ERROR) + separate error log
- Detailed error capture using `ErrorRecord` and `InvocationInfo`
- Transcripts for scheduled runs (`Start-Transcript` / `Stop-Transcript`)
- Reliable backup workflow with fallback method
- Operational readiness: exit codes (`0` success, `1` failure) for Task Scheduler

---

## Full Implementation

### Script: `backup.ps1`

```powershell
#Requires -Version 5.1
[CmdletBinding()]
param(
    [Parameter()]
    [ValidateNotNullOrEmpty()]
    [string]$ConfigPath   = "C:\ImportantApp\settings.txt",

    [Parameter()]
    [ValidateNotNullOrEmpty()]
    [string]$BackupFolder = "C:\Backups",

    [Parameter()]
    [ValidateNotNullOrEmpty()]
    [string]$LogFolder    = "C:\Logs"
)

Set-StrictMode -Version Latest
$ErrorActionPreference = 'Stop'

function Ensure-Folder {
    param([Parameter(Mandatory)][string]$Path)
    if (-not (Test-Path -Path $Path -PathType Container)) {
        New-Item -Path $Path -ItemType Directory -Force | Out-Null
    }
}

function Ensure-File {
    param([Parameter(Mandatory)][string]$Path)
    if (-not (Test-Path -Path $Path -PathType Leaf)) {
        New-Item -Path $Path -ItemType File -Force | Out-Null
    }
}

# Ensure folders exist first (so we can log / transcript)
Ensure-Folder -Path $LogFolder
Ensure-Folder -Path $BackupFolder

# Log paths
$InfoLogPath     = Join-Path $LogFolder "backup_log.txt"
$ErrorLogPath    = Join-Path $LogFolder "backup_error_log.txt"
$TranscriptPath  = Join-Path $LogFolder ("backup_transcript_{0}.txt" -f (Get-Date -Format "yyyyMMdd_HHmmss"))

Ensure-File -Path $InfoLogPath
Ensure-File -Path $ErrorLogPath

function Write-Log {
    param(
        [Parameter(Mandatory)][string]$Message,
        [ValidateSet('INFO','WARN','ERROR')]
        [string]$Level = 'INFO'
    )

    $line = "{0} [{1}] {2}" -f (Get-Date -Format "yyyy-MM-dd HH:mm:ss"), $Level, $Message
    Add-Content -Path $InfoLogPath -Value $line
    if ($Level -eq 'ERROR') { Add-Content -Path $ErrorLogPath -Value $line }
    Write-Verbose $line
}

function Log-ErrorDetail {
    param(
        [Parameter(Mandatory)][System.Management.Automation.ErrorRecord]$ErrorRecord,
        [Parameter(Mandatory)][string]$CustomMessage
    )

    $details = @"
==================== ERROR ====================
Date:              $(Get-Date -Format "yyyy-MM-dd HH:mm:ss")
Custom Message:    $CustomMessage
Error Message:     $($ErrorRecord.Exception.Message)
Error Type:        $($ErrorRecord.Exception.GetType().FullName)
Script Name:       $($ErrorRecord.InvocationInfo.ScriptName)
Line Number:       $($ErrorRecord.InvocationInfo.ScriptLineNumber)
Position Message:  $($ErrorRecord.InvocationInfo.PositionMessage)
Stack Trace:       $($ErrorRecord.ScriptStackTrace)
================================================

"@
    Add-Content -Path $ErrorLogPath -Value $details
}

# Start transcript so Task Scheduler has full output saved
Start-Transcript -Path $TranscriptPath -Append | Out-Null

try {
    Write-Log -Level INFO -Message "Backup job started. ConfigPath=$ConfigPath BackupFolder=$BackupFolder LogFolder=$LogFolder"
    Write-Log -Level INFO -Message "Transcript: $TranscriptPath"

    # Validate config file
    if (-not (Test-Path -Path $ConfigPath -PathType Leaf)) {
        throw "Configuration file not found at: $ConfigPath"
    }

    # Build backup path early (so it's available even if copy fails)
    $timestamp  = Get-Date -Format "yyyyMMdd_HHmmss"
    $configItem = Get-Item -Path $ConfigPath
    $backupPath = Join-Path $BackupFolder ("{0}_{1}{2}" -f $configItem.BaseName, $timestamp, $configItem.Extension)

    # Primary method
    try {
        Copy-Item -Path $ConfigPath -Destination $backupPath -Force
        Write-Log -Level INFO -Message "Backup created (Copy-Item): $backupPath"
    }
    catch {
        Write-Log -Level WARN -Message "Copy-Item failed, trying fallback. Reason: $($_.Exception.Message)"

        # Fallback method
        Get-Content -Path $ConfigPath -Raw | Out-File -FilePath $backupPath -Encoding UTF8 -Force
        Write-Log -Level INFO -Message "Backup created (fallback Out-File): $backupPath"
    }

    Write-Log -Level INFO -Message "Backup job SUCCESS."
    exit 0
}
catch {
    Write-Log -Level ERROR -Message "Backup job FAILED: $($_.Exception.Message)"
    Log-ErrorDetail -ErrorRecord $_ -CustomMessage "Backup job failed"
    Write-Error "Backup job failed. See: $InfoLogPath and $ErrorLogPath"
    exit 1
}
finally {
    Write-Log -Level INFO -Message "Backup job completed."
    Stop-Transcript | Out-Null
}
```
### Task Scheduler action arguments (copy/paste)
```powershell
-NoProfile -ExecutionPolicy Bypass -File "C:\Scripts\backup.ps1" -ConfigPath "C:\ImportantApp\settings.txt" -BackupFolder "C:\Backups" -LogFolder "C:\Logs" -Verbose
```

##  Implementation Walkthrough
- Parameters + validation


	- Script accepts ConfigPath, BackupFolder, and LogFolder with defaults and ValidateNotNullOrEmpty().


- Reliability mode


	- Set-StrictMode -Version Latest catches common scripting mistakes.


	- $ErrorActionPreference = 'Stop' converts non-terminating errors into terminating errors so they can be caught.


- Idempotent setup


	- Ensure-Folder and Ensure-File guarantee folders/log files exist before any write operations.


- Logging strategy


	- Write-Log writes consistent timestamped lines to backup_log.txt.


	- Errors are also written to backup_error_log.txt.


- Diagnostics for scheduled runs


	- Start-Transcript captures everything printed during execution into a timestamped transcript file.


- Backup workflow


	- Validates the config file exists.


	- Creates a timestamped backup filename.


	- Attempts Copy-Item first.


	- If Copy-Item fails, falls back to reading/writing file content with Get-Content -Raw | Out-File.


- Exit codes


	- exit 0 on success and exit 1 on failure for Task Scheduler reporting.



##  Key Takeaways (What I Learned)
- Production-style scripts need logging + transcripts to troubleshoot scheduled jobs.


- StrictMode + Stop-on-error makes failures predictable and catchable.


- Fallback logic improves resilience when the primary copy method fails.


- Exit codes matter because Task Scheduler uses them to determine success/failure.



##  Results & Validation
(Execution output/log contents were not provided in the message.)
 Expected artifacts after a run:
- C:\Backups\<settings_yyyyMMdd_HHmmss>.txt (timestamped backup)


- C:\Logs\backup_log.txt (info log)


- C:\Logs\backup_error_log.txt (error log)


- C:\Logs\backup_transcript_yyyyMMdd_HHmmss.txt (transcript)



##  Validation Walkthrough
```powershell
# 1) Run manually to validate behavior (same parameters as Task Scheduler)
powershell -NoProfile -ExecutionPolicy Bypass -File "C:\Scripts\backup.ps1" `
 -ConfigPath "C:\ImportantApp\settings.txt" `
 -BackupFolder "C:\Backups" `
 -LogFolder "C:\Logs" `
 -Verbose

# 2) Confirm backup created
Get-ChildItem "C:\Backups" | Sort-Object LastWriteTime -Descending | Select-Object -First 5

# 3) Review logs
Get-Content "C:\Logs\backup_log.txt" -Tail 50
Get-Content "C:\Logs\backup_error_log.txt" -Tail 50

# 4) Review the latest transcript
Get-ChildItem "C:\Logs\backup_transcript_*.txt" | Sort-Object LastWriteTime -Descending | Select-Object -First 1
```
