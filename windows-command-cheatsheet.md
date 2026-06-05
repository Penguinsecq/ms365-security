# Windows System Files Checking

## Check the Health of Windows System files

We are first going to do a quick scan of the Windows system:

```
DISM /Online /Cleanup-Image /CheckHealth
```

## Advanced system scan

The next step is to do a more advanced system. ScanHealth will check the component store and report any corruption in the log file: C:\Windows\Logs\CBS\CBS.log

```
DISM /Online /Cleanup-Image /ScanHealth
```

## Repair Windows with DISM Online Cleanup Image Restorehealth

When the ScanHealth or CheckHealth commands found any corruption then we can repair it with the RestoreHealth command:

```
DISM /Online /Cleanup-Image /RestoreHealth
```

## Repair the current Windows Installation

Run the command below to repair the files:

```
sfc /scannow
```

---

# Free up disk space with DISM

## Analyze the Component Store

The first step is to analyze the Windows Update component store. The command below will go through all the update files to see what can be removed.

1. Right-click on start (or press Windows key + X)
2. Choose Windows PowerShell (admin) or Windows Terminal (admin)
3. Enter the command below:

```
Dism /Online /Cleanup-Image /AnalyzeComponentStore
```

## Cleanup old files manually

On Windows 10 you might not get the option to restart your computer. In this case, we will need to start the clean-up manually with the command below:

```
Dism /Online /Cleanup-Image /StartComponentCleanup
```

Once completed use the command below to reduce the size even further:

```
Dism /Online /Cleanup-Image /StartComponentCleanup /ResetBase
```

The ResetBase command can take some time, just let it complete.

### Reference

https://lazyadmin.nl

---

# Users management

Set Password expire

```
wmic UserAccount where Name="user name" set PasswordExpires=False
```

Disable user

```
wmic UserAccount where Name="user name" set disabled=True
```

```
Net user "username" /active:no
```

---

# Program management

View installed apps

```
Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*, HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Where-Object { $_.DisplayName -ne $null } | Select-Object DisplayName, Publisher, InstallDate | Sort-Object InstallDate -Descending | Format-Table –AutoSize
```

To search all installed programs

```
$product get name
```

To search specific installed program

```
$ product where "Name like '%space%'" get name
```

---

# Services management

Query service info

```
sc.exe query AppIDSvc
```

```
sc.exe qc "AppIDSvc"
```

```
Get-Service -Name AppIDSvc
```

```
Get-Service -Name "AppIDSvc" | Select-Object Name, StartType
```

Config the service

```
sc.exe config "AppIDSvc" start=auto & net start "AppIDSvc"
```

```
Set-Service -Name "Spooler" -StartupType Automatic
```

---

# Schedule Tasks

Interesting website: https://lazyadmin.nl/powershell/how-to-create-a-powershell-scheduled-task/#use-task-scheduler-to-run-a-powershell-script

List all tasks

```
Get-ScheduledTask
```

List detailed info

```
Get-ScheduledTask | Get-ScheduledTaskInfo
```

View Details runtime info

```
Get-ScheduledTaskInfo -TaskName "Schedule to Collect AppLocker logs" -TaskPath "\AppLockerTasks\"
```

See Action Details

```
(Get-ScheduledTask -TaskName "Schedule to Collect AppLocker logs" -TaskPath "\AppLockerTasks\").Actions | Format-List *
```

Run task immediately

```
Start-ScheduledTask -TaskName "DailyBackup"
```

Stop running task

```
Stop-ScheduledTask -TaskName "DailyBackup"
```

Delete task

```
Unregister-ScheduledTask -TaskName "DailyBackup" -Confirm:$false
```

Disable / Enable task

```
Disable-ScheduledTask -TaskName "DailyBackup"
Enable-ScheduledTask -TaskName "DailyBackup"
```

Export as XML

```
Export-ScheduledTask -TaskName "Schedule to Collect AppLocker logs" -TaskPath "\AppLockerTasks\"
```

#### Example schedule task

```powershell
#Remove existing task if it exists
if (Get-ScheduledTask -TaskName 'Schedule to Collect AppLocker logs' -TaskPath '\AppLockerTasks\' -ErrorAction SilentlyContinue) {
    Unregister-ScheduledTask -TaskName 'Schedule to Collect AppLocker logs' -TaskPath '\AppLockerTasks\' -Confirm:$false
}

# Trigger at 11AM every Wed and Fri
$taskTrigger = New-ScheduledTaskTrigger `
    -Weekly `
    -DaysOfWeek Monday, Wednesday, Friday `
    -At 11:00AM

$taskAction = New-ScheduledTaskAction `
    -Execute "PowerShell.exe" `
    -Argument "-NoProfile -ExecutionPolicy Bypass -Command `"& 'C:\Windows\ExportAppLockerLogs2.ps1' *> 'C:\AppLockerLogs\debug.txt'`""

$Principal = New-ScheduledTaskPrincipal -UserId "SYSTEM" -LogonType ServiceAccount -RunLevel Highest
$Settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries -StartWhenAvailable

if (-not (Test-Path "C:\Windows\ExportAppLockerLogs2.ps1")) {
    Write-Error "Script not found."
    exit 1
}

Register-ScheduledTask -TaskName 'Schedule to Collect AppLocker logs' -TaskPath '\AppLockerTasks\' -Action $taskAction -Trigger $taskTrigger -Principal $Principal -Settings $Settings -Force
```
