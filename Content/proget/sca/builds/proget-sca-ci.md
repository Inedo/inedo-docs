---
title: "SCA and Continuous Integration (CI)"
order: 1
---

The easiest way to perform a Software Composition Analysis (SCA) on your applications is to run a command line tool called [pgutil](/docs/proget/reference-api/proget-pgutil) during your Build/Continuous Integration (CI) process.  For SCA integration with Java, see our [ProGet SCA Integration with Java](/docs/proget/feeds/maven/proget-sca-java) guide.

Running the `pgutil builds scan` command will scan the code you just built, identify the open-source components used, and publish this information to the ProGet server for further analysis.

This article explains how `pgutil` works, where it can be integrated into your CI process, and provides a sample PowerShell script.

:::(info) (💡 Learn How to do this in Minutes with BuildMaster)
BuildMaster makes it easy to integrate SCA into your CI process. See this [HOWTO: SCA Integration with BuildMaster](/docs/proget/sca/builds/proget-sca-howto-buildmaster) tutorial to get it set up in minutes!
:::

## Identifying Components with pgutil
[pgutil](/docs/proget/reference-api/proget-pgutil) is a free and open-source tool with a number of commands designed to automate and interact with ProGet. The `pgutil builds scan` command is designed to run immediately after building your application, generally as part of the Continuous Integration (CI) process.

When the `pgutil builds scan` command is run, it will scan the build outputs; identify open-source components in .NET, npm, and Python projects; and publish that information to your ProGet server. See [Getting Started with pgutil](/docs/proget/reference-api/proget-pgutil) to learn how to download and install.

### HOWTO: Use pgutil Command-line Interface (CLI)
Before installing `pgutil` on your build server, we recommend [creating an API Key](/docs/proget/reference-api/proget-apikeys) with "Upload SBOM" permissions for `pgutil` to use.

The easiest way to install `pgutil` on your server is to follow the instructions in the [Getting Started with pgutil](/docs/proget/reference-api/proget-pgutil) guide.

Once `pgutil` is installed, simply invoke the `pgutil builds scan` command after building your application. For example:

```
pgutil builds scan
 --api-key=mySecretKey1000
 --input=MyApplication.csproj 
 --project-name="My Application"
 --version=5.00.21
 ```
 
After running this command, `pgutil` publishes the third-party, open-source components found in the application to the target ProGet server. 

In the example above, the ProGet server would create a project named `MyApplication` and publish those packages to the `5.00.21` release (which it would also create, if it didn't exist).

Note that the `project-name` and `version` command-line arguments will generally be passed in as variables from the build/CI process.

See the [pgutil GitHub Repository](https://github.com/Inedo/pgutil) for more details on command-line arguments.

## Using CycloneDX Instead of pgutil
If you're already using a tool like [CycloneDX](https://cyclonedx.org/) in your CI/CD pipeline to generate SBOM documents, you can simply upload those SBOMs to ProGet using the [SCA API](/docs/proget/reference-api/proget-api-sca).
```
curl 
  -X POST
  -d @output/MySBomFile.xml
  -H "Content-Type: text/xml"
  -H "X-ApiKey: mySecretKey1000"
   https://proget.corp.local/api/sca/import
```
Behind the scenes, this is what `pgutil builds scan` will do: generate an SBOM file, then upload it to the API.

## Integrating pgutil into Your CI Process

Continuous Integration (CI) is very specific to the technology your application uses (e.g. NET, Python, Java, etc.) and the CI tool (Jenkins, Azure DevOps, TeamCity, etc.) you use. However, at a high level, they all follow a very similar process of retrieving source code, building it, and packaging it.

### General CI Process
The following steps illustrate a "generic" continuous integration process, and show where you'll want to run the `pgutil builds scan` command:

1. **Get Source Code**; download/retrieve the desired source code from a source control repository like Git
2. **Prepare Source Code**; prior to compiling the code, you'll often have steps that will prepare the code to be built by your build tool
    * **Modify Source Files**;  for exampe, editing the `AssemblyInfo.cs`
    * **Restore Packages**;  using a command like `nuget restore`, this will download the packages required to build/compile the code from your repository (e.g. ProGet)
4. **Build/Compile Code**; using a tool like `dotnet build`, this will run your build/compilation script
5. **<span style="background-color:#9FF">NEW STEP</span> Run `pgutil builds scan`**; this will inspect the built/compiled code, generate an SBOM document and publish it to ProGet
6. **Automated Unit Testing**; an optional step that involves running code to test your code before it's packaged for deployment
6. **Package/Capture Artifacts**; saves the required build output (such as `.exe`) that you will deploy later

## Sample PowerShell Script For pgutil
This is a script that you can adapt and incorporate:

:::(Internal) (TODO: Verify Accuracy)
:::

```(PowerShell)
# variables set by your CI process
$ProjectPath = "MyDotNetProject1\something.csproj"
$ApplicationName = "MyDotNetProject1"
$ReleaseNumber = "4.3.2"

# configuration
$Source = "MyProGetServer"

# ensure that pgutil is installed
dotnet tool install --global pgutil

# run pgutil...
pgutil builds scan
 --source=$Source `
 --input=$ProjectPath `
 --type=nuget `
 --project-name=$ApplicationName `
 --version=$ReleaseNumber
```

## Modifying Script for Your CI Server

### Azure DevOps
Use a local `dotnet tool` action to run `pgutil` on Windows and Linux build agents.

1. Create a [ProGet API key](/docs/proget/reference-api/proget-apikeys)
2. Once the API Key is created in ProGet, you will need to add it as a secret variable on your pipeline.
   1. Navigate to your pipeline in Azure DevOps
   2. Click Edit
   3. Click Variables and then the plus icon
   4. Enter a name (ex: `PROGETAPIKEY`) and your API key as the value
   5. Check "Keep this value secret"
   6. Click OK
3. Commit a dotnet tool manifest
   1. At the root of your repository, run `dotnet new tool-manifest` (see [Microsoft's dotnet local tool](https://docs.microsoft.com/en-us/dotnet/core/tools/local-tools-how-to-use#create-a-manifest-file) documentation for more information)
   2. Commit this to your git repository
4. Add .NET 6.0 in your pipeline
   - If you are already using dotnet 6 in your pipeline, go to the next step.
   - Add the following to your workflow:
    ```yaml
    - task: UseDotNet@2
      inputs:
        packageType: 'sdk'
        version: '6.0.x'
    ```
    - This can be added anywhere before the `pgutil` steps, but is typically added at the beginning
4. Add the `pgutil` steps after build/publish steps of your code
   ```yaml
   - script: dotnet tool install pgutil
   - script: dotnet tool run pgutil builds scan --type=nuget --input=MyProject.csproj --project-name=MyProject --version=1.0.0 --project-type=application --source=MProGetServer
   ```

### GitHub Actions
Use a local `dotnet tool` action to run `pgutil` on Windows and Linux build agents.

1. Create a [ProGet API key](/docs/proget/reference-api/proget-apikeys)
   1. Once the API Key is created in ProGet, you will need to add it as a secret on your GitHub project
   2. Navigate to your project in GitHub
   3. Click "Settings"
   4. Navigate to "Secrets > Actions" on the right
   5. Click "New repository secret"
   6. Enter a name (ex: `PROGETAPIKEY`) and your API key as the secret value
2. Commit a dotnet tool manifest
   1. At the root of your repository, run `dotnet new tool-manifest` (see [Microsoft's local tool](https://docs.microsoft.com/en-us/dotnet/core/tools/local-tools-how-to-use#create-a-manifest-file) documentation for more information)
   2. Commit this to your git repository
3. Setup .NET 6.0 in your workflow
   - If you are already using dotnet 6 in your workflow, go to the next step.
   - Add the following to your workflow:
    ```yaml
        - name: Setup .NET
          uses: actions/setup-dotnet@v2
          with:
            dotnet-version: 6.0.x
    ```
    - This can be added anywhere before the `pgutil` steps, but is typically added at the beginning
4. Add the `pgutil` steps after build/publish steps of your code
```yaml
    - name: Install pgutil
      run: dotnet tool install pgutil
    - name: Run pgutil
      working-directory: ProfiteCalcNet.Console
      run: dotnet tool run pgutil builds scan --type=nuget --input=MyProject.csproj --project-name=MyProject --version=1.0.0 --project-type=application --source=default --api-key=${{ secrets.PROGETAPIKEY }}
```

## SCA Integration on Other CI Servers

### Jenkins

#### Step 1: Enable the PowerShell plugin 
From the main dashboard, go to "Manage Jenkins" > "Manage Plugins" and search “PowerShell.” 

![](https://149882660.v2.pressablecdn.com/wp-content/uploads/2023/01/PowerShell-plugin.png)

Add the plugin and enable it. This will let Jenkins and PowerShell work together without a different console.

#### Step 2: Create a PowerShell Project (Job) and Add Parameters

From the main dashboard, go to “New Item,” and enter a name based on the script you want to run. Select “Freestyle project,” then click OK to create the project.

![](https://149882660.v2.pressablecdn.com/wp-content/uploads/2023/01/Freestyle-Project.png)

After creating the project, this will bring you to the “Configure Project” page.

Enter the description and then check “This project is a parameterized item” from the list below. Then, start adding the parameters to your PowerShell script.

![](https://149882660.v2.pressablecdn.com/wp-content/uploads/2023/01/String-Parameters.png)

Although Jenkins offers several options, when it comes to PowerShell scripts, you’ll mostly use Boolean (checkboxes), Choice (dropdown list), and String (Textbox) parameters.

#### Step 3: Prep Jenkins to Run PowerShell
In the “Build” part of the project settings, click “Add Build Step” and select “PowerShell”. This allows Jenkins to run the PowerShell script that we’ll paste in.

![](https://149882660.v2.pressablecdn.com/wp-content/uploads/2023/01/Build-Step.png)

### TeamCity

To integrate SCA into your TeamCity CI, you can use [PowerShell Build Runner](https://www.jetbrains.com/help/teamcity/powershell.html)
