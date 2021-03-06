---
id: PowerShell-Kill-a-Suspended-Process-on-Windows
title: PowerShell - Kill a Suspended Process on Windows
description: Killing a Windows process based on it's status
---

---

The powershell cmdlet **get-process\*** does not give you the option to query for processes that are in a specific state.
I needed to kill suspended chrome instance left behind by puppetteer ( the web automation library)

```powershell
# Get the pids for all open chrome isntances
$chromeProcesses = (Get-Process chrome).Id

# Loop through each
$chromeProcesses | ForEach-Object {
    # assign the id for later use
    $processId = $_
    # check its status by calling into the .Net Diagnostics namespace.
    $process=[System.Diagnostics.Process]::GetProcessById($_)
    $threads=$process.Threads
    foreach($thread in $threads) {
        if($thread.WaitReason -eq 'Suspended') {
            # command to kill the process if it's status is suspended
            Get-Process chrome | Where-Object {$_.id -eq $processId} | kill -Force
        }
    }
}
```
