---
title: "HOWTO: Verify & Configure Local Windows Admins Accounts"
order: 2
---

This article offers step-by-step guidance on how to define a [desired configuration with OtterScript](/docs/otter/collecting-verifying-configuration/otter-desired-configuration-with-otterscript) that will verify that a user exists, is a local administrator, has the correct password, and, optionally, if the user should be enabled or disabled.

If the user doesn't exist, then Otter will report this as "configuration drift" and give you the option to [automatically remediate configuration drift](/docs/otter/drift-remediation-configuration-as-code/otter-automatically-remediate-configuration-drift) by ensuring a user exists in that desired state.

## Create Secure Credentials
Otter uses [Secure Credentials](/docs/otter/configuring-for-your-team/otter-global-components-resource-credentials) to store accounts, logins, and access keys that contain secrets. Because a local administrator account has a password, we will use these to store the desired password, and it will only be visible only to privileged users in Otter.

1. Navigate to _Administration > Secure Credentials_
2. Create a new _UserName and Password_ credential
3. Enter the name of the administrator account and password you'd like to create
4. Repeat for as many accounts as you'd like to create

## Create "EnsureLocalAdmin" Script
Otter uses [Scripts](/docs/otter/scripting-in-otter/otter-core-concepts-assets) to store PowerShell scripts. When you're using your own scripts, you may need to [modify them to detect & remediate drift](/docs/otter/drift-remediation-configuration-as-code/otter-remediating-drift-with-powershell-psensure#converting-existing-powershell-to-detect-remediate-drift); however the script below will work as is.

1. Navigate to _Scripts_ and create a new PowerShell script named `EnsureLocalAdmin`
2. Click on `EnsureLocalAdmin` to edit it
3. Copy/paste the [contents of the EnsureLocalAdmin PowerShell script from below](#ensurelocaladmin-powershell-script)
4. Save the Script

## Create "LocalAdmins" Server Role
Otter uses server roles to define desired configuration states using OtterScript.

1. Navigate to _Roles_ and create a new role named `LocalAdmins`
2. Navigate to the _Desired Configuration_ tab of the new Role
3. Click _create_ to the right of Configuration Plan
4. Add the  `EnsureLocalAdmin` powershell script operation to your OtterScript plan.
   a. Select `Collect & Remediate configuration (PSEnsure)` for the _Script execution mode_ option if you'd like Otter to remediate, or `Verify configuration (PSVerify)`if you'd only like to report drift
   b. For the User option, enter `$PSCredential(«YOUR_SECURE_CREDENTIAL_NAME»)`
   c. Enter `false` only if you want the account disabled

Next, assign the LocalAdmins server role to your servers and check for drift! You can also remediate the drift if you configured to do so.

## Example OtterScript Desired Configuration
This is only an example, and your desired configuration will look different.
```OtterScript
PSEnsure EnsureLocalAdmin
(
  User: $PSCredential(bobAdminAccount)
);
PSEnsure EnsureLocalAdmin
(
  User: $PSCredential(jimAdminAccount)
);
PSEnsure EnsureLocalAdmin
(
  User: $PSCredential(defaultAdminAccount),
  Enabled: false
);
```

## EnsureLocalAdmin PowerShell Script

You can copy/paste this script directly, or modify it as needed.

```powershell
<# 
.SYNOPSIS
Ensures the specified account exists in the Administrators group

.PARAMETER User
PSCredential with the username/password

.AHCONFIGTYPE
Local Admins

.AHCONFIGKEY 
$Username

.AHEXECMODE 
$ExecutionMode
#>

param ([System.Management.Automation.PSCredential]$User, [bool]$Enabled = $true)

if ($Enabled -eq $true)
{
    $Username = $User.UserName
}
else
{
    $Username = $User.UserName + " (disabled)"
}

if ($ExecutionMode -eq "Collect") {
    
    $currentUser = Get-LocalUser -Name $User.UserName -ErrorAction Ignore

    if($currentUser -eq $null)
    {
        if($Enabled -eq $true)
        {
            Write-Information "User does not exist." -InformationAction Continue
            return $false
        }
        else {
            Write-Information "User does not exist." -InformationAction Continue
            return $true
        }
    }
    else {
        if($Enabled -eq $false){
            if( $currentUser.Enabled -eq $Enabled){
                Write-Information "User exists, but disabled." -InformationAction Continue
                return $true
            }
            else {
                Write-Information "User exists and enabled." -InformationAction Continue
                return $false
            }
        }

        if($currentUser.Enabled -ne $Enabled)
        {
            return $false;
        }

        $userInAdmin = Get-LocalGroupMember -Name "Administrators" -Member $User.UserName  -ErrorAction Ignore
         if($userInAdmin -eq $null)
        {
            Write-Information "User is not an administrator." -InformationAction Continue
            return $false
        }
        else {
            Write-Information "User is an administrator." -InformationAction Continue
           
        }
        Add-Type -assemblyname system.DirectoryServices.accountmanagement 
        $DS = New-Object System.DirectoryServices.AccountManagement.PrincipalContext([System.DirectoryServices.AccountManagement.ContextType]::Machine)
        
        if($DS.ValidateCredentials($user.UserName, $User.GetNetworkCredential().password) )
        {
            Write-Information "User credentials are valid." -InformationAction Continue
            return $true
        }
        else {
            Write-Information "User credentials are not valid." -InformationAction Continue
            return $false
        }
        
    }
}
elseif ($ExecutionMode -eq "Configure") { 
    $currentUser = Get-LocalUser -Name $User.UserName -ErrorAction Ignore

    if($currentUser -eq $null) {
        if($Enabled -eq $true){
            Write-Information "Adding new user." -InformationAction Continue
            New-LocalUser -Name $User.UserName -Password $User.Password -PasswordNeverExpires
            Add-LocalGroupMember -Name "Administrators" -Member $User.UserName
        }
        else{
            return
        }
    }
    else{
        if($Enabled -eq $false -and $currentUser.Enabled -eq $true)
        {
           Write-Information "Disabling user." -InformationAction Continue
            Disable-LocalUser -Name $User.UserName
        }
        elseif($Enabled -eq $true -and $currentUser.Enabled -eq $false)
        {
           Write-Information "Enabling user." -InformationAction Continue
           Enable-LocalUser -Name $User.UserName
        }
        if($Enabled -eq $true)
        {
            $userInAdmin = Get-LocalGroupMember -Name "Administrators" -Member $User.UserName  -ErrorAction Ignore
            if($userInAdmin -eq $null)
            {
                Write-Information "Adding user to group." -InformationAction Continue
                Add-LocalGroupMember -Name "Administrators" -Member $User.UserName
            }

            Add-Type -assemblyname system.DirectoryServices.accountmanagement 
            $DS = New-Object System.DirectoryServices.AccountManagement.PrincipalContext([System.DirectoryServices.AccountManagement.ContextType]::Machine)
        
            if($DS.ValidateCredentials($user.UserName, $User.GetNetworkCredential().password) -eq $false)
            {
                Write-Information "Updating user password." -InformationAction Continue
                Set-LocalUser -Name $User.UserName -Password $User.Password -PasswordNeverExpires $true
            }
        }
    }
}
else {
    Write-Information "Unknown operation." -InformationAction Continue
}
```