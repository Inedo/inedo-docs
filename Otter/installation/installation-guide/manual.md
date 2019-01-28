---
title: Manual Installation
keywords: otter
---

:::manual-install
**Note:** The manual installation instructions are for informational purposes only and
is not fully supported. Please visit the [Download](/otter/download) page to download the installer. {.announcement}


These instructions will describe how to manually install each of [Otter's deployable components](/support/documentation/otter/installation/architecture). The manual installation packages for these components can be found on the [All Versions](/otter/versions) page.

Prior to installation of Otter or an Otter Agent, an Otter "home" directory needs to be created. We recommend `C:\Otter` when installing manually, because `Program Files` tend to have special permissions, and because a number of 3rd-party tools that you may integrate with might have issues with very long path names. Replace any instances of `{Otter.RootDir}` in the following documentation with the actual path you have selected. {.info}

## Otter's Identity {#identity}

Otter may be configured to use its own domain account, or may run as [NetworkService](https://msdn.microsoft.com/en-us/library/windows/desktop/ms684272(v=vs.85).aspx) or [LocalSystem](https://msdn.microsoft.com/en-us/library/windows/desktop/ms677973(v=vs.85).aspx).

## Otter's Encryption Key {#encryption-key}

In order to store sensitive data, such as [resource credentials](/support/documentation/otter/global-components/resource-credentials), a 32-character hex string is used for AES encryption. You can generate this however you'd like, including by copying the pseudorandom string below, which is regenerated every time you refresh this page:

<pre id="random-aes-key"></pre>
<script>
var aesKey;
if (window.crypto &amp;&amp; crypto.getRandomValues) {
    aesKey = new Uint8Array(16);
    crypto.getRandomValues(aesKey);
    aesKey = aesKey.reduce(function(str, byte) {
        return str + ('0' + byte.toString(16)).substr(-2);
    }, '').toUpperCase();
} else {
    aesKey = Array(32).map(function() {
        return Math.floor(Math.random() * 16).toString(16);
    }).join('').toUpperCase();
}
document.getElementById('random-aes-key').textContent = aesKey;
</script>

Take note of this encryption key; if you do not set one, then sensitive data will be stored unencrypted.

## Database Installation {#database}

1. Create a database named Otter on any SQL Server database server (see [Supported SQL Server editions](/kb/1046){target="_blank"} for more information.)
2. Unzip `Otter-DbChangeScripts.zip` into any directory and run the executable with the following arguments:

    ```
    bmdbupdate.exe Update /conn=[connection string] /init=yes
    ```
3. Take note of the connection string used in this step

## Extension & Executions Directories {#directories}

1.  Create a directory to store extensions (default `Extensions`) under `{Otter.RootDir}`, which will be referred to as `{Otter.Extensions.RootDir}` for the remainder of these instructions
2. Create a directory to store unpackaged extensions (default `ExtensionsTemp`) under `{Otter.RootDir}`, which will be referred to as `{Otter.ExtensionsTemp.RootDir}` for the remainder of these instructions
3. Create a directory for local agents to use as the default working directory (default `Executions`) under `{Otter.RootDir}`, which will be referred to as `{Otter.Executions.RootDir}` for the remainder of these instructions

## Web Application Installation {#web-application}

1. Create a web root directory (`WebApp` is recommended) in `{Otter.RootDir}`, which will be referred to as `{Otter.WebApp.RootDir}` for the remainder of these instructions
2. Unzip `Otter-WebApp.zip` into `{Otter.WebApp.RootDir}`
3. Edit `Web.config` to ensure two settings:

    - `InedoLib.DbConnectionString` - the same one entered into the database installation earlier
    - `Persistence.EncryptionKey` - the same encryption key generated earlier

    **Note:** Make sure to double-check that the values of these configuration settings are correct! {.info}

4. To install on IIS (recommended for manual installation), perform the following steps:

    - Create an application pool in IIS to run under the appropriate identity using a classic (non-integrated) pipeline and v4.0 managed code.
    - Create a website that points to `{Otter.WebApp.RootDir}` using whichever host name configuration or ports preferred. **Note**: A virtual directory is not supported at this time.

#### Service Installation {#service}

1. Create a service root directory (`Service` is recommended) under `{Otter.RootDir}`, which will be referred to as `{Otter.Service.RootDir}` for the remainder of these instructions
2. Unzip `Otter-Service.zip` into **{Otter.Service.RootDir}**
3. Edit `App_appSettings.config` to ensure one setting:

    - `InedoLib.DbConnectionString` - the same one entered into the database installation earlier
    - `Persistence.EncryptionKey` - the same encryption key generated earlier

    **Note:** Make sure to double-check that the values of these configuration settings are correct! {.info}

4. Install the Windows service by running the following command from an elevated command prompt or PowerShell session in `{Otter.Service.RootDir}`:

    ```
    OtterService.exe install
    ```

5. If you did **not** install to IIS, install the Web server by running the following command from an elevated command prompt or PowerShell session in `{Otter.Service.RootDir}`:

    ```
    OtterService.exe installweb
    ```

    Note that `{Otter.WebApp.RootDir}` must be a sibling with the default name (i.e. `..\WebApp`)

---

## Manual Agent Installation {#agent}

**Note:** agents should be installed on remote servers that Otter will orchestrate or configure, and are not required to be installed on the Otter server itself. {.info}

1. Download the [Otter manual installation package](/otter/versions).
2. Extract the **Otter-Agent.zip** file from the archive, then extract the files from **Otter-Agent.zip** to the directory where you want to install the agent (for example, C:\Otter\Agent).
3. Edit the **InedoAgentService.exe.config** file and change the value of the **Otter.RootPath** configuration item to a directory where the Otter Agent will be allowed to write files (example, C:\Otter\AgentData).
4. Open a command prompt window as Administrator in the directory where the Otter agent was extracted and type **InedoAgentServer.exe install**
5. Start the service by typing **sc \\\\localhost start INEDOAGENTSVC**
6. That's it! You'll also want to make sure **port 46336** is open in your firewall. Currently the port is not configurable, but it will be in a future version.
7. By default, the agent is installed to run as **Local System**, giving it full access to the machine. If you do not want to give it full control, you may change the service account to something more suitable in the services.msc dialog.
:::
