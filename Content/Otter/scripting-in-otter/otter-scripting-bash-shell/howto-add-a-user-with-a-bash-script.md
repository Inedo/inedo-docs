---
title: "HOWTO: Add a User With a Bash Script"
order: 2
---

Otter is designed to [run your existing scripts](/docs/otter/orchestration-server-automation/otter-jobs-templates) -- PowerShell, Bash, Python, Windows Batch, etc. -- against any number of servers.  This example adds a user to a Linux server using a Shell script.  This is best to be used with a Job Template or executing the shell script directly.  The password input parameter will then be marked as a sensitive input that will be masked when entering the value.   When using this in an OtterScript orchestration, the password will be specified as plain text unless a secure variable or Secure Credential is used.

## Prerequisites
Bash scripts only work against Linux Servers and require a Linux (SSH) Server to be added in Otter.

The only prerequisite is to have v1.14.0+ of the Scripting extension installed in Otter v3.0.20+.  

## Create the AddUser.sh Shell Script
This shell script will allow users to add users to a Linux server by running the script directly or in a [Job Template](/docs/otter/orchestration-server-automation/otter-jobs-templates).

### Step 1: Create a new Shell Script in Otter
You will first need to add a new script to Otter.  To create a blank script:
1. Navigate to the  _Scripts_ page
2. Click the "Add Script" button
3. Select "New Blank Script"
    1. Select "Shell" for the _Type_
    2. Enter a Name of `AddUser.sh`
    3. Click OK

Once a new blank script has been created in Otter. You will then need to edit the script and paste in the following script.  This script includes [Augmented Help](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) documentation at beginning to help define parameters that will need to be specified.

#### AddUser.sh
```bash
   #!/bin/bash
   # AhDescription: This script will add a new user
   # 
   # AhParameter: user_name
   # AhParameter: password (sensitive)
   
   useradd $user_name -m
   echo $user_name:$password | sudo chpasswd
   ```

### Step 2: Run the AddUser.sh Script
Once the script has been added, you can now run the script by clicking the green play button to the right of the script name and then select "Run as ad-hoc job".  When you run the script, you will be prompted to select a server to run this on, the "user_name" to add, and the "password" to be set.  Since the password parameter was defined as "sensitive" in the augmented help, Otter will mask the password input field.

![Ad-Hoc Job](/resources/docs/otter-bash-runjob.png){height="" width="50%"}

## Create a Job Template
You can also create a [Job Template](/docs/otter/orchestration-server-automation/otter-jobs-templates) based on this script.  This allows you to specify more detail in a customized GUI and allows you to control which servers and environments this script can target.

### Step 1: Create Job Template
You will first need to create a new job template based on this script.  To do this:
1. Navigate to the _Scripts_ page
2. Click the green play button to the right of `AddUser.sh`
3. Select "Create a Job Template"
4. Select the desired "Server targeting"
5. Click "Create Job Template"

Once you have created your job template, you can then customize any details (server targeting, usage restrictions, template variable descriptions, etc...) to simplify the GUI for your users.

### Step 2: Run Job Template
Once you have created the Job Template, now you can run it.  Navigate to the _Jobs_ page and locate your template, by default the name is "Run AddUser.sh".  When you click on the name, you are prompted with a similar screen to the ad-hoc job page, but now it has any customized details that were made in step 1.  Now you will just need to enter the user_name and password for the user to create and click "Run".

## Leverage AddUser.sh from OtterScript
You can also leverage the AddUser.sh Shell script in OtterScript to add users as part of your server orchestration.  Since the password parameter is not masked in OtterScript, we recommend storing the user credentials in a [Secure Credential](/docs/otter/configuring-for-your-team/otter-global-components-resource-credentials).

### Step 1: Add Secure Credential
You will first need to add a secure credential.
1. Navigate to _Administration -> Secure Credentials_
2. Click "Create Secure Credential"
3. Select "Username & Password"
    1. Enter a Name (ex: JohnDoeCredential)
    2. Enter a User name
    3. Enter a Password
    4. Check "OtterScript usage"

### Step 2: Create OtterScript Orchestration
Create a new OtterScript Otrchesctration
1. Navigate to the _Scripts_ page
2. Click "Add Script"
3. Click "New Blank Script"
    1. Type: OtterScript
        1. If you are using Otter 3.0, you will need to select "OtterScript Ochestration Plan"
    2. Name AddUser
4. Edit script you just created
    1. Change to Text Mode and paste in the following script (this script assumes the credential name is JohnDoeCredential):
   ```otterscript
   set $username = $SecureCredentialProperty(JohnDoeCredential, Username);
   set $password = $SecureCredentialProperty(JohnDoeCredential, Password);
   SHCall AddUser.sh
   (
      Parameters: %(user_name: $username, password: $password)
   );
   ```
   
### Step3: Run OtterScript Orchestration
Once you have created the OtterScript orchestration script, now you can run it.  Navigate to the _Scripts_ page and locate your `AddUser.otter` script and run the script by clicking the green play button to the right of the script name, select "Run as ad-hoc job", then click "Create Job".  Now the OtterScript will use the Secure Credential you created and add that user to a server using your Shell script.