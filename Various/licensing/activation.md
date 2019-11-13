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

## Automatic Activation {#automatic}

Generally, activation happens automatically in the background upon first use of an Inedo product. Automatic activation will also be attempted if any activation code becomes invalid. If activation cannot be performed automatically, the software will function normally (albeit with a warning notification) during the 7-day grace period from the day the license key was created. Once the grace period expires, if the product is still unable to establish a connection to the internet to perform the activation, ***manual activation*** is required.

## Manual Activation {#manual}

When an Inedo product is unable to access the internet and requires activation, a manual activation of the product must be performed. By visiting the "Licensing & Activation" page in the product, there will be a message indicating that the activation is invalid. There will be a link to activate it; once clicked, it will display the following instructions needed to manually activate:

### Step 1:

Log in to [MyInedo](https://my.inedo.com){target="_blank"} and click the *Manually Activate License Key* button:

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
