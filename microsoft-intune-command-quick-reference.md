**Microsoft Intune Command Quick Reference**

dsregcmd /status 
Verifies current registration state with Microsoft Entra ID, validates tenant details, and confirms Primary Refresh Token (PRT) presence.

dsregcmd /refreshprt 
Forces an immediate PRT renewal from Entra ID to resolve Single Sign-On (SSO) latency and Conditional Access compliance state issues without requiring a full user logoff.

Restart-Service IntuneManagementExtension 
Restarts the primary client-side agent responsible for Win32 apps, PowerShell scripts, and remediation policies to force immediate schedule re-evaluation.

mdmdiagtool.exe -area DeviceEnrollment;DeviceProperties -zip C:\Logs\MDMDiag.zip 
Compiles enrollment records, policy reports, and system event logs into a single CAB archive for escalation and Microsoft Premier Support tickets.

explorer C:\ProgramData\Microsoft\IntuneManagementExtension\Logs 
Opens the direct directory to inspect Win32 app delivery failures, detection script logs, and remediation outputs.

Get-MpComputerStatus 
Audits real-time threat protection state, engine versions, and signature update status via PowerShell.

manage-bde -status 
Audits volume encryption percentage, key protection status, and cipher strength across all attached drives.

w32tm /resync 
Resynchronizes system clocks with domain time servers to prevent Kerberos and SSL token invalidation.

ms-settings:workplace 
Launches the Windows Settings page directly to trigger manual MDM policy syncs.

Recommended Endpoint Remediation Workflow
Initial Sync: Trigger a manual policy sync using ms-settings:workplace.
Identity Check: Run dsregcmd /status to confirm active Entra ID join state and valid PRT tokens.

Restart Agent: Execute Restart-Service IntuneManagementExtension to re-evaluate pending scripts and app installations.

Compliance Validation: Run Get-MpComputerStatus to verify system health and security profile enforcement. 
