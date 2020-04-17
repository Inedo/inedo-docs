---
title: License Key Activation
sequence: 20
keywords: license
---

::: {.attention .technical}
**Automatic License Activation Not Working?** See [Knowledge Base post 1758](https://inedo.com/support/kb/1758/automatic-license-activation-not-working). For more information on how to fix automatic license activation problems & errors.
:::

All licensed Inedo products require license activation regardless of the edition. License activation is the process of pairing a license key with a value that is unique per machine it is installed on. The machine-unique identifier is a hash of the following components:

{.docs}
-  MAC address of first operating-system-ordered operational non-loopback network interface
- Processor ID from SMBIOS specification, i.e. the CPU
- The minor version of the Inedo software

If any of these components change, re-activation would be required. There is no published limit on the number of activations that may occur on a specific license key.

## Causes for Re-activation {#re-activation}

{.docs}
- The software was moved to a different system
- Changing or adding a license key
- The software was installed on virtual machine that changes CPU IDs or network interfaces when it restarts (to prevent this, configure the network adapter of the virtual machine hosting the software to be a static value)
- A new network interface was added
- Upgrading to a minor or major version of the software
## Activating Your License Key

#### Step 1:
Go to: Administration (Gear Icon > Administration)
![administration portal](/resources/documentation/various/administration.png){.screenshot}

#### Step 2:
Under: System select License Key & Activation
![license key](/resources/documentation/various/system.png){.screenshot}

#### Step 3:
Under License key select [change]
![change key](/resources/documentation/various/license-key.png){.screenshot}

#### Step 4:
This will change the license key or allow you to add one for the first time
![change key](/resources/documentation/various/change-license-key.png){.screenshot}

#### Step 5:
Select Save

#### Step 6: 	
After Saving the License select [activate] under Activation Status
![activate](/resources/documentation/various/activate.png){.screenshot}
#### Step 7:
License is now Active
![active](/resources/documentation/various/activation.png){.screenshot}

## Automatic Activation {#automatic}

Generally, activation happens automatically in the background upon first use of an Inedo product. Automatic activation will also be attempted if any activation code becomes invalid. If activation cannot be performed automatically, the software will function normally (albeit with a warning notification) during the 7-day grace period from the day the license key was created. Once the grace period expires, if the product is still unable to establish a connection to the internet to perform the activation, ***manual activation*** is required.

## Manual Activation {#manual}

When an Inedo product is unable to access the internet and requires activation, a manual activation of the product must be performed. By visiting the "Licensing & Activation" page in the product, there will be a message indicating that the activation is invalid. There will be a link to activate it; once clicked, it will display the following instructions needed to manually activate:

### Step 1:

Log in to [MyInedo](https://my.inedo.com) and click the *Manually Activate License Key* button:

![Manually Activate](/resources/documentation/various/manually-activate.png){.screenshot}

### Step 2:

Enter the license key and the machine UID on the manual activation page within the software:

![Enter Key and UID](/resources/documentation/various/enter-key-uid.png){.screenshot}

Click the *Get Activation Code* button:

![Get Activation Code](/resources/documentation/various/get-code.png){.screenshot}

### Step 3:

Copy and paste the resulting activation code into the software and click save:

![Activation Code](/resources/documentation/various/activation-code.png){.screenshot}

Alternatively, if for some reason access to the manual activation page is blocked, the values for the license key and activation code are also configurable on the "Advanced Settings" page in each product.

## FAQ
#### Who can enter/change a key?
A license key may be changed at any time in the software by an administrator.

#### How do I activate my key?
In your ProGet instance, go to Administration - > Software Configuration -> License Key and Activation. Plug the key in, and once it’s marked “valid,” you can click “Activate.”

#### How do I view my key?
In your ProGet instance, go to Administration - > Software Configuration -> License Key and Activation. You can also view your key(s) on [My Inedo](https://my.inedo.com).

#### How much time is left on my key?
If your key expires, you can see the expiration date under “License Key and Activation” in your ProGet Instance. Your trial begins the day you receive your key. Free forever keys do not expire.

#### Does my key expire?
There are three types of keys: free forever, free trial, and paid. Free forever keys never expire. Trial keys expire 30 days from the date they key is generated and delivered (NOT activated), and paid keys expire at the end of your subscription period. Learn all about your ProGet free trial.

#### What happens when my trial key expires?
When your trial period comes to an end, your key “expires.” When a key expires, all of your applications and components remain in ProGet in exactly the same way – there is NO SCENARIO in which your data will be locked or erased from ProGet. However, ProGet does require you to enter a key with a valid license at the end of your trial. There are two ways to do this:
    + Enter a free forever key. If you had a free key before your trial, you can simply reenter it. If you have misplaced it or never had forever free key in the past, you can create a new one by [clicking here](https://my.inedo.com).
    + [Purchase ProGet](https://inedo.com/proget/pricing) and enter that key to continue enjoying the advanced features of your free trial.

[Learn More about ProGet Free Trials](https://inedo.com/proget/pricing/trial)
