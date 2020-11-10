---
title: Traditional (Legacy) Installer Guide
sequence: 500
keywords: proget, installation, legacy, traditional, silent install
---

::: {.attention .best-practice} 
For new installations on Windows, we recommend installation using the Inedo Hub. See the [Inedo Hub Installation Guide](/docs/proget/installation/installation-guide) for more information.
:::

*We are planning to stop shipping legacy installer in 2021*

For the most convenient way to install ProGet is to use the [InedoHub](https://my.inedo.com/downloads). It is primarily used to install, update, and otherwise service applications in the Inedo Suit. You can learn more about it on how the InedoHub can help you save time by reading our [guide](https://docs.inedo.com/docs/desktophub/overview).

Please also note that if you have the InedoHub installed you will NOT be able to use the traditional install method without first completely removing the InedoHub and any products installed using it.

If you require a legacy installer for any version of ProGet, you can on the [ProGet All Version](https://my.inedo.com/proget/versions) page, and clicking on the “Traditional Installer” for the version you want.

Once you have downloaded and opened the installer, you’ll be asked to review and accept out [license agreement](https://inedo.com/proget/license-agreement) before you can use ProGet

Complete the rest of the installation by selecting each setting appropriate for your system. We have updated our [detailed installation guide](https://docs.inedo.com/docs/proget/installation/installation-guide) to reflect the new streamlined layout of the InedoHub, but the options are still the same.

    <h1>ProGet Silent Installation</h1>
    <p>
        ProGet supports silent installation using both the <a href="/docs/desktophub/overview">Inedo Hub</a> and the 
        <a href="/docs/proget/installation/traditional-installer">Traditional Installer</a>. 
        To perform silent installation with the Inedo Hub, refer to the <a href="/docs/desktophub/inedo-hub-cli">Inedo Hub CLI</a> documentation.
        Otherwise, you can perform silent installation with the Traditional Installer using the following arguments:
    </p>

    <h3 id="silent-install-options">Silent Install Options</h3>
    <table>
        <tr>
            <td>/S</td>
            <td><b>Required for a silent install.</b> If this is not specified, the UI will be displayed.</td>
        </tr>
        <tr id="edition-arg">
            <td>/Edition=&lt;value&gt;</td>
            <td><b>Required for a silent install.</b> The desired ProGet edition: May be <i>Express</i>, <i>Trial</i>, or <i>LicenseKey</i>.</td>
        </tr>
        <tr>
            <td>/EmailAddress=&lt;value&gt;</td>
            <td>The email address used for requesting a license key. Required if <a href="#edition-arg">Edition</a> is <i>Express</i> or <i>Trial</i>.</td>
        </tr>
        <tr>
            <td>/FullName=&lt;value&gt;</td>
            <td>The user's full name used for requesting a license key. Required if <a href="#edition-arg">Edition</a> is <i>Express</i> or <i>Trial</i>.</td>
        </tr>
        <tr>
            <td>/LicenseKey=&lt;value&gt;</td>
            <td>License key to use. Only used if <a href="#edition-arg">Edition</a> is <i>LicenseKey</i>.</td>
        </tr>
        <tr id="targetpath-arg">
            <td>/TargetPath=&lt;value&gt;</td>
            <td>Root path where ProGet will be installed. The default is [ProgramFiles]\ProGet.</td>
        </tr>
        <tr>
            <td>/PackagesPath=&lt;value&gt;</td>
            <td>Root path where ProGet will store its feed packages. The default is [CommonApplicationData]\ProGet\Packages.</td>
        </tr>
        <tr>
            <td>/ASPNETTempPath=&lt;value&gt;</td>
            <td>Path where ASP.NET will store temporary files for the ProGet web application. The default is [CommonApplicationData]\ProGet\Temporary ASP.NET Files.</td>
        </tr>
        <tr>
            <td>/WebAppPath=&lt;value&gt;</td>
            <td>Path where the ProGet web application is installed. The default is [<a href="#targetpath-arg">TargetPath</a>]\WebApp.</td>
        </tr>
        <tr>
            <td>/ServicePath=&lt;value&gt;</td>
            <td>Path where the ProGet service is installed. The default is [<a href="#targetpath-arg">TargetPath</a>]\Service.</td>
        </tr>
        <tr>
            <td>/Extensions=&lt;value&gt;</td>
            <td>Path where the extension library is located. The default is [<a href="#targetpath-arg">TargetPath</a>]\Extensions.</td>
        </tr>
        <tr>
            <td>/PackagesPath=&lt;value&gt;</td>
            <td>Path where the packages root path is located. The default is [<a href="#targetpath-arg">TargetPath</a>]\Packages.</td>
        </tr>
        <tr>
            <td>/ConnectionString=&lt;value&gt;</td>
            <td>SQL Server connection string ProGet will use to connect to the database. The default is <i>Data Source=localhost; Initial Catalog=ProGet; Integrated Security=True;</i>.</td>
        </tr>
        <tr id="port-arg">
            <td>/Port=&lt;value&gt;</td>
            <td>Port number the ProGet web application will listen to. The default is <i>8624</i>.</td>
        </tr>
        <tr id="useintegratedwebserver-arg">
            <td>/UseIntegratedWebServer=&lt;value&gt;</td>
            <td>When <i>true</i>, the integrated web server is used to host ProGet; when <i>false</i>, IIS is used. The default is <i>true</i>.</td>
        </tr>
        <tr>
            <td>/WebServerPrefixes=&lt;value&gt;</td>
            <td>Prefixes used by the integrated web server. The default is http://*:[<a href="#port-arg">Port</a>]/.</td>
        </tr>
        <tr>
            <td>/InstallSqlExpress</td>
            <td>Downloads and installs a SQL Express instance named ProGet. The default is <i>false</i>.</td>
        </tr>
        <tr id="useraccount-arg">
            <td>/UserAccount=&lt;value&gt;</td>
            <td>User account of the ProGet service and web application. The default is <i>LocalSystem</i>.</td>
        </tr>
        <tr id="password-arg">
            <td>/Password=&lt;value&gt;</td>
            <td>Password of the user account of the ProGet service and web application. Only used when <a href="#useraccount-arg">UserAccount</a> is not one of the standard service accounts.</td>
        </tr>
        <tr>
            <td>/WebAppUserAccount=&lt;value&gt;</td>
            <td>User account of the ProGet web application. The default is [<a href="#useraccount-arg">UserAccount</a>].</td>
        </tr>
        <tr>
            <td>/WebAppUserAccountPassword=&lt;value&gt;</td>
            <td>Password of the user account of the ProGet web application. The default is [<a href="#password-arg">Password</a>].</td>
        </tr>
        <tr>
            <td>/ServiceUserAccount=&lt;value&gt;</td>
            <td>User account of the ProGet service. The default is [<a href="#useraccount-arg">UserAccount</a>].</td>
        </tr>
        <tr>
            <td>/ServiceUserAccountPassword=&lt;value&gt;</td>
            <td>Password of the user account of the ProGet service. The default is [<a href="#password-arg">Password</a>].</td>
        </tr>
        <tr>
            <td>/ConfigureIIS</td>
            <td>Creates an IIS website and application pool to host the ProGet web application. The default is <i>false</i>.</td>
        </tr>
        <tr>
            <td>/LogFile=&lt;value&gt;</td>
            <td>If specified, writes out a detailed log file of the installation process to the specified path value.</td>
        </tr>
    </table>
    <h4>Example</h4>
    <p style="margin-bottom:5px;">Install ProGet using the defalt settings with license key XXXXX:</p>
    <div style="font-family: Consolas, 'Courier New'; background: black; color:#ccc; width: 400px; border: solid #aaa; border-radius: 5px; padding: 5px;">
        ProGetSetup.exe /S /LicenseKeys=XXXXX
        
     <h4>NOTE</h4>
     We are no longer actively supporting the traditional installer, as such you may encounter errors during the installation process such as the ability to complete the installation using 
      <div style="font-family: Consolas, 'Courier New'; background: black; color:#ccc; width: 400px; border: solid #aaa; border-radius: 5px; padding: 5px;">
        ProGetSetup.exe /S /Edition=Express /EmailAddress=<value> /FullName=<value>      
    </div>
    <h3 id="silent-upgrade-options" style="margin-top: 20px;">Silent Upgrade Options</h3>
    <table>
        <tr>
            <td>/S</td>
            <td>Required for a silent upgrade. If this is not specified, the UI will be displayed.</td>
        </tr>
        <tr>
            <td>/Upgrade</td>
            <td>Required to perform an upgrade. If this is not specified, the upgrade is not performed.</td>
        </tr>
        <tr>
            <td>/BackupDatabase</td>
            <td>Backs up the ProGet database before the upgrade begins. The default is <i>true</i>.</td>
        </tr>
        <tr>
            <td>/DatabaseBackupPath=&lt;value&gt;</td>
            <td>The path to save the backup of the ProGet database. The default is <i>[SysDrive]:\ProGetBackups</i>.</td>
        </tr>
        <tr>
            <td>/ConnectionString=&lt;value&gt;</td>
            <td>SQL connection string used for upgrading ProGet's database schema. The default is whatever connection string the ProGet service is currently using.</td>
        </tr>
        <tr>
            <td>/LogFile=&lt;value&gt;</td>
            <td>If specified, writes out a detailed log file of the installation process to the specified path value.</td>
        </tr>
    </table>
    <h4>Example</h4>
    <p style="margin-bottom:5px;">Upgrade ProGet using the default settings:</p>
    <div style="font-family: Consolas, 'Courier New'; background: black; color:#ccc; width: 400px; border: solid #aaa; border-radius: 5px; padding: 5px;">
        ProGetSetup.exe /S /Upgrade
    </div>
