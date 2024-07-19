---
title: "HOWTO: Verify & Configure Remote Desktop Connections"
order: 1
---

This article offers step-by-step guidance on how to define a [desired configuration with OtterScript](/docs/otter/collecting-verifying-configuration/otter-desired-configuration-with-otterscript) that will verify that remote desktop connections are enabled in Windows and allowed in the Windows Defender Firewall.  

If remote desktop connections are not enabled, then Otter will report this as "configuration drift" and give you the option to [automatically remediate configuration drift](/docs/otter/drift-remediation-configuration-as-code/otter-automatically-remediate-configuration-drift) by ensuring remote desktop connections are enabled.

## Create "EnsureRemoteDesktopConnections" Script

Otter uses [Scripts](/docs/otter/scripting-in-otter/otter-core-concepts-assets) to store PowerShell scripts. When you're using your own scripts, you may need to [modify them to detect & remediate drift](/docs/otter/drift-remediation-configuration-as-code/otter-remediating-drift-with-powershell-psensure#converting-existing-powershell-to-detect-remediate-drift); however the script below will work as is.

1. Navigate to _Scripts_ and create a new PowerShell script named `EnsureRemoteDesktopConnections`
2. Click on `EnsureRemoteDesktopConnections` to edit it
3. Copy/paste the [contents of the EnsureRemoteDesktopConnections](#ensureremotedesktopconnections-powershell-script) PowerShell script from below
    a. Alternatively, you can use the [EnsureDisabledRemoteDesktopConnections script](#alternative-ensuredisabledremotedesktopconnections-powershell-script) to disable remote desktop connections
4. Save the Script

## Create "WindowsRemoteSettings" Role

Otter uses server roles to define desired configuration states using OtterScript.

1. Navigate to _Roles_ and create a new role named `WindowsRemoteSettings`
4. Navigate to the _Desired Configuration_ tab of the new Role
5. Click _create_ to the right of Configuration Plan
6. Add your `EnsureRemoteDesktopConnections` powershell script operation to your OtterScript plan.
   a. Select `Collect & Remediate configuration (PSEnsure)` for the _Script execution mode_ option if you'd like Otter to remediate, or `Verify configuration (PSVerify)` if you'd only like to report drift

Next, assign the WindowsRemoteSettings server role to your servers and check for drift! You can also remediate the drift if you configured to do so.

## Example OtterScript Desired Configuration

This is only an example, and your desired configuration may look different.

```OtterScript
PSEnsure EnsureRemoteDesktopConnections;
```

## EnsureRemoteDesktopConnections PowerShell Script

You can copy/paste this script directly, or modify it as needed.

:::(Info) (NOTE:)
This example also disables Network Level Authentication in the remote desktop connection settings, this can easily be updated to enable that setting if needed.  This is configured via `UserAuthenticationRequired` on the WmiObject.
:::

```powershell
<# 
.SYNOPSIS
Ensures Remote Desktop Connections are allowed on the server

.AHCONFIGKEY
Enable Remote Desktop Connection

.AHCONFIGTYPE
Windows Settings

.AHEXECMODE 
$ExecutionMode
#>

if ($ExecutionMode -eq "Collect") {
    $remoteDesktopConnectionDeniedValue = (Get-ItemPropertyValue -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections")
    
    $rdpNetworkLevelAuthenticationValue = (Get-WmiObject -class "Win32_TSGeneralSetting" -Namespace root\cimv2\terminalservices -Filter "TerminalName='RDP-tcp'").UserAuthenticationRequired
    
    $firewallRulesEnabled = (Get-NetFirewallRule -DisplayGroup "Remote Desktop" | Where-Object {!$_.Enabled}).Count -eq 0
    
    if( $remoteDesktopConnectionDeniedValue -eq 0 -and $rdpNetworkLevelAuthenticationValue -eq 0 -and $firewallRulesEnabled -eq $true)
    {
        Write-Information "Remote Desktop Connection is enabled." -InformationAction Continue
        return $true
    }
    else
    {
        Write-Information "Remote Desktop Connection is disabled." -InformationAction Continue
        return $false
    }
}
elseif ($ExecutionMode -eq "Configure") { 
    Write-Information "Enable Remote Desktop Connection." -InformationAction Continue
    Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 0 -ErrorAction Continue
    
    Write-Information "Diable Network Level Authentication." -InformationAction Continue
    (Get-WmiObject -class "Win32_TSGeneralSetting" -Namespace root\cimv2\terminalservices -Filter "TerminalName='RDP-tcp'"  -ErrorAction Continue).SetUserAuthenticationRequired(0)
    
    Write-Information "Enable RDP Firewall Rules." -InformationAction Continue
    Enable-NetFirewallRule -DisplayGroup "Remote Desktop"  -ErrorAction Continue
}
```

## Alternative: EnsureDisabledRemoteDesktopConnections PowerShell Script

You can copy/paste this script directly, or modify it as needed.

:::(Info) (NOTE:)
Use this script if you would like to ensure remote desktop connections are disabled on a server
:::

```powershell
<# 
.SYNOPSIS
Ensures Remote Desktop Connections are not allowed on the server

.AHCONFIGKEY
Disable Remote Desktop Connection

.AHCONFIGTYPE
Windows Settings

.AHEXECMODE 
$ExecutionMode
#>

if ($ExecutionMode -eq "Collect") {
    $remoteDesktopConnectionDeniedValue = (Get-ItemPropertyValue -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections")
    
    $firewallRulesEnabled = (Get-NetFirewallRule -DisplayGroup "Remote Desktop" | Where-Object {!$_.Enabled}).Count -eq 0
    
    if( $remoteDesktopConnectionDeniedValue -eq 0 -or $firewallRulesEnabled -eq $true)
    {
        Write-Information "Remote Desktop Connection is enabled." -InformationAction Continue
        return $false
    }
    else
    {
        Write-Information "Remote Desktop Connection is disabled." -InformationAction Continue
        return $true
    }
}
elseif ($ExecutionMode -eq "Configure") { 
    Write-Information "Disable Remote Desktop Connection." -InformationAction Continue
    Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 1 -ErrorAction Continue
    
    Write-Information "Disable RDP Firewall Rules." -InformationAction Continue
    Disable-NetFirewallRule -DisplayGroup "Remote Desktop"  -ErrorAction Continue
}
```