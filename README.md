# 🧩 PowerShell Practice & Projects

Hands-on PowerShell labs, scripts, and mini-projects focused on **automation**, **Windows administration**, and **security/IR workflows**.  
This repo is built for learning-by-doing: every project includes goals, usage steps, and (when possible) sample output.

---

## 🎯 Goals
- Build strong PowerShell fundamentals (objects, pipelines, functions, modules)
- Automate common Windows admin tasks
- Practice security-focused scripting (event logs, triage, detection helpers)
- Create GitHub-ready projects with clear documentation and repeatable results

---

## 🧰 What’s Inside
- **Fundamentals**: pipelines, `Where-Object`, `Select-Object`, `Sort-Object`, `Format-*`, parameters
- **Automation**: file cleanup, reporting, scheduled tasks, basic tooling
- **Security / IR**: event log filtering, process inspection, simple triage scripts
- **Projects**: larger scripts with structured output (CSV/JSON), logging, and error handling

---

## ✅ Featured Projects (Examples)
> Replace these with your real script names as you add them.

| Project | Description | Output |
|---|---|---|
| **System Audit Report** | Collects host details (OS, patch, users, services, startup, network) | CSV / JSON |
| **Event Log Hunter** | Filters Security/Application logs by Event ID, keywords, timeframe | Console + Export |
| **Process & Parent Tree Check** | Lists suspicious processes with parent/command line relationships | Table |
| **Windows Baseline Snapshot** | Saves a “known-good” snapshot for comparison later | Files in `/output` |

---

## 🚀 Getting Started

### Requirements
- Windows 10/11 or Windows Server
- PowerShell **5.1** (built-in) or **PowerShell 7+** (recommended)

Check your version:
```powershell
$PSVersionTable.PSVersion
