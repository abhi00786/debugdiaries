# Debug Diary: Jenkins Not Loading on Windows After Reboot

## 🗓 Date

2025-11-16

## 🧩 Issue

After rebooting the Windows system, Jenkins UI was not loading at
`http://localhost:8080`.

## 🔍 Investigation Steps

1.  Ran:

        netstat -ano | findstr :8080

    -   Found **java.exe** (PID 7036) listening on port 8080.
    -   Connection state showed multiple **CLOSE_WAIT** entries →
        indicating a hung Jenkins instance.

2.  Verified PID:

        tasklist /FI "PID eq 7036"

    -   Confirmed it was Jenkins' `java.exe`.

3.  Attempted to kill the process:

        taskkill /PID 7036 /F

    -   Failed with **Access is denied** because the process was running
        as a Windows Service.

4.  Identified that the Jenkins Windows Service needed to be stopped.

## ✅ Resolution

1.  Opened **PowerShell as Administrator**.

2.  Stopped Jenkins service:

        net stop jenkins

3.  Restarted the service:

        net start jenkins

4.  Jenkins UI successfully loaded at:

        http://localhost:8080

## 🧠 Root Cause

Jenkins service got stuck in an unhealthy state after reboot, leaving
the Java process running but unresponsive.

## 🚀 Final Status

Jenkins operational after service restart.
