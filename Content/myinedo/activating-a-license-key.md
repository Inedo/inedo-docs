---
title: "License Keys & Activation"
order: 3
---

All Inedo products require a license key and activation prior to use.

* Paid and trial edition license keys have an expiration date, which you can see on MyInedo or within the product under Admin > License Key and Activation.
* Free edition license keys do not expire, nor will license keys for paid perpetual licenses.

:::(warning) (⚠ Manual Activation)
If your product is unable to access the internet, you can [manually activate your product](#manual-activation) instead. This will provide you with a "Machine UID" that you can use together with your License Key to activate your product on MyInedo.
:::

After you [enter a license key](#entering-your-license-key) in your Inedo product, the software will automatically activate itself by connecting to `my.inedo.com` and downloading an activation code. 

:::(Error)
**If your license key expired**, not to worry. You can be back up and running in minutes by [requesting a stopgap license key](/docs/myinedo/stopgap-license-keys), and sort out the renewal issues later.

**If your activation code is invalid**, you just need to [manually activate your product](#manual-activation). This happened for one of several reasons; see [Re-activation of a Product](#reactivation-of-a-product).
:::

Without a valid license key or activation, the product will effectively cease to function, and will only allow very limited usage (such as adding a new license key or activating). However, your data will not be locked or erased - you just need to enter a license key or activate again.

## Entering Your License Key
When you receive a license key from purchasing or requesting a trial of an Inedo product, you will need to enter and activate within the software, 

### Step 1:  Navigate to License Key & Activation
Navigate to `Administration > License Key & Activation` by clicking on the Gear Icon in the top-right corner, then clicking on "License Key & Activation" under "Software Configuration".

![click license key and activation button](/resources/docs/myinedo-licensekeysandactivation-softwareconfiguration.png)

### Step 2: Click [change]

![click change button](/resources/docs/myinedo-licensekeysandactivation-change.png)

### Step 3: Enter License Key
This will change the license key or allow you to add one for the first time.

![change license key](/resources/docs/myinedo-licensekeysandactivation-changelicensekey%285%29.png)

### Step 4: Click [activate]
After saving the License Key, click [activate] under Activation Status.

![click activate button](/resources/docs/myinedo-licensekeysandactivation-activate.png)

### Step 5: Verify Activation
License is now Active

![license is active](/resources/docs/myinedo-licensekeysandactivation-licenseactive.png)

:::(Warning)
If this does not take effect immediately, you may need to restart the web application. You can do this by stopping/starting the Application Pool (IIS Hosted), or stopping/starting the Windows Service for the web application.
:::

## Manual Activation

When an Inedo product is unable to access the internet and requires activation, a manual activation of the product must be performed. You will know a manual activation is required if you receive the ["Manual Activation Required"](/resources/docs/myinedo-licensekeysandactivation-manualactivation%283%29.png) message when attempting to activate your license.

The instructions needed to manually activate are as follows:

### Step 1:  Navigate to License Key & Activation
Navigate to `Administration > License Key & Activation` by clicking on the Gear Icon in the top-right corner, then clicking on "License Key & Activation" under "Software Configuration".

![click license key and activation button](/resources/docs/myinedo-licensekeysandactivation-softwareconfiguration.png)

### Step 2: Click [change]

![click change button](/resources/docs/myinedo-licensekeysandactivation-change.png)

### Step 3: Enter License Key
This will change the license key or allow you to add one for the first time.

![change license key](/resources/docs/myinedo-licensekeysandactivation-changelicensekey%285%29.png)

### Step 4: Recieve your Machine UID

ProGet will be unable to activate automatically, and present you with the following dialog box. You will need both your license key and the machine UID from this screen later, so make sure to copy it to the clipboard.

![manual activation popup](/resources/docs/myinedo-licensekeysandactivation-manualactivation%283%29.png)

### Step 5: Click Manually Activate on MyInedo

Log in to [MyInedo](http://my.inedo.com){target="_blank"} and click the *Manually Activate License Key* button:

![click manually activate license key](/resources/docs/myinedo-licensekeysandactivation-manuallyactivatelicensekey%281%29.png)

### Step 6: Enter License Key & Machine UID in MyInedo
:::(Warning)
Note that you will need the machine UID from your Inedo product to perform this step.
:::
Enter the license key and the machine UID on the manual activation page.

![enter the license key and the machine UID](/resources/docs/myinedo-licensekeysandactivation-activatelicensekey%281%29.png)

### Step 7: Enter Activation Code in Product
Copy and paste the resulting activation code into the software and click save:

![Enter activation code](/resources/docs/myinedo-licensekeysandactivation-activationcode2.png)

If you cant find where to  paste the activation code, try activating your license key again and it will prompt you to enter the activation code.

:::(Warning)
If this does not take effective immediately, you may need to restart the web application. You can do this by stopping/starting the Application Pool (IIS Hosted), or stopping/starting the Windows Service for the web application.
:::

##  Re-activation of a Product
Inedo products are activated using an "activation code" that is generated by `my.inedo.com`. This code is based on your license key, your server's hardware, and the product's version number. If any of these change, then the product will need to be reactivated. 

This reactivation usually happens automatically, but if your Inedo product is unable to access the internet, then you will need to [manually activate](#manual-activation) the software again.

There is no limit on the number of activations that may occur on a specific license key, but we may contact you if we notice unusual activation activity that indicating multiple machines are using the same license key.

### Common causes for re-activation
Inedo products generate a machine-unique identifier (Machine UID) by hashing information about the CPU (vendor ID, model, family, and stepping info) and the and the major version of the Inedo software (e.g. 5.1, 5.3, 2022, 2023). As such, the following events will require reactivation:

- The software was moved to a different system
- Changing or adding a license key
- The software was installed on virtual machine that changes CPU IDs when it restarts (to prevent this, configure the CPU of the virtual machine hosting the software to be a static value)
- Upgrading to a major version of the software
