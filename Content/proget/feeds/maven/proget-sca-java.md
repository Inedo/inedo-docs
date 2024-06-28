---
title: "ProGet SCA Integration with Java"
order: 1
---

[Projects and Releases](/docs/proget/sca/builds) in ProGet let you track the open-source and third-party components (packages) that your organization uses, and help you identify issues like vulnerabilities, license violations, and missing packages.

While other languages, like C#, NodeJs, and Python, can use Inedo's [pgscan](https://github.com/inedo/pgscan) to scan for dependencies and import them to ProGet, Maven works best using the [CycloneDX Maven Plugin](https://github.com/CycloneDX/cyclonedx-maven-plugin) directly.

## Configuring Your Maven Project
Use the [CycloneDX Maven Plugin](https://github.com/CycloneDX/cyclonedx-maven-plugin) to generate a Software Bill Of Materials (SBOM) and import that file into [ProGet's SCA Projects](https://inedo.com/proget/software-composition-analysis) using the [Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/index.html).

### Configuring CycloneDX
Add the [CycloneDX Maven Plugin](https://github.com/CycloneDX/cyclonedx-maven-plugin) plugin to the `plugins` node in your project `pom.xml`.
```xml
<plugin>
	<groupId>org.cyclonedx</groupId>
	<artifactId>cyclonedx-maven-plugin</artifactId>
	<version>2.7.5</version>
	<configuration>
		<projectType>library</projectType>
		<schemaVersion>1.4</schemaVersion>
		<includeBomSerialNumber>true</includeBomSerialNumber>
		<includeCompileScope>true</includeCompileScope>
		<includeProvidedScope>true</includeProvidedScope>
		<includeRuntimeScope>true</includeRuntimeScope>
		<includeSystemScope>true</includeSystemScope>
		<includeTestScope>false</includeTestScope>
		<includeLicenseText>false</includeLicenseText>
		<outputReactorProjects>true</outputReactorProjects>
		<outputFormat>xml</outputFormat>
		<outputName>bom</outputName>
		<outputDirectory>${project.build.directory}</outputDirectory>
		<verbose>false</verbose>
	</configuration>
	<executions>
		<execution>
			<phase>package</phase>
			<goals>
				<goal>makeAggregateBom</goal>
			</goals>
		</execution>
	</executions>
</plugin>
```

Once added, this plugin will generate a Software Bill of Materials (SBOM) XML file duing the package phase of the Maven lifecycle. 

This will also configure the deploy phase of the Maven lifecycle to append the bom.xml as an artifact on the pacakge with the name `<package_name>-<version>-cyclonedx.xml` (ex: `hello-world-maven-2.0.1-cyclonedx.xml`).

To prevent this file from being uploaded, add `<skipAttach>false</skipAttach>` to the `configuration` node of the CycloneDX Maven plugin.

::: (Warning) (SNAPSHOT Dependencies)
SNAPSHOT dependencies will be recorded as a snapshot version, not the actual version used (`2.1-SNAPSHOT` instead of `2.1-20230309.215451-1`).  This may cause difficulties in properly tracking dependencies because a new snapshot version may be deployed after this package is pushed.  The final release of a package should not use a SNAPSHOT version for any dependencies.
:::

### Configuring Maven to Import your SBOM
Once you have configured Maven to generate your SBOM file, you will need to configure the [Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/index.html) to execute curl to import it into your [project in ProGet](/docs/proget/sca/builds).

To do this, we recommend first [creating an API key](/docs/proget/reference-api/proget-apikeys) that has the appropriate permissions upload SBOM documents. Once you have that, you can edit the `plugins` node of your `pom.xml` file as follows.

```xml
<plugin>
	<groupId>org.codehaus.mojo</groupId>
	<artifactId>exec-maven-plugin</artifactId>
	<version>3.1.0</version>
	<executions>
		<execution>
		<id>publish-project</id>
		<phase>deploy</phase>
		<goals>
			<goal>exec</goal>
		</goals>
		<configuration>
			<executable>curl</executable>
			<arguments>
				<argument>-k</argument>
				<argument>-i</argument>
				<argument>-X</argument> 
				<argument>POST</argument> 
				<argument>-H</argument>
				<argument>"Content-Type: text/xml"</argument>
				<argument>-H</argument>
				<argument>"X-Apikey: «api key»"</argument>
				<argument>--fail-with-body</argument>
                <argument>-s</argument>
				<argument>-d</argument>
				<argument>@bom.xml</argument>
				<argument>«proget host URL»/api/sca/issues</argument>
			</arguments>
            <useMavenLogger>true</useMavenLogger>
			<workingDirectory>${project.build.directory}</workingDirectory>
		</configuration>
		</execution>
	</executions>
</plugin>
```
Once added, this plugin will call curl to import the file `sbom.xml` in the build directory into ProGet during the deploy phase of the Maven lifecycle. 

#### Best Practice: Use Properties for the API and API Key
Instead of specifying the repository URL directly, we recommend using a property. This allows you to not only override the setting at when running the `mvn deploy` command, but to specify the property in the `settings.xml` file instead of in each project.

To do this, add (or edit) the `properties` node under the ProGet profile in your `settings.xml` file:

```xml
<profiles>
    <profile>
        <id>ProGet</id>
        <properties>
            <proget-sca-api>http://my.proget.server/api/sca/import</proget-sca-api>
	        <proget-sca-api-key>«api key»</proget-sca-api-key>
        </properties>
        ... snip ...
```

Then, you can use a property in your POM file like this:
```xml
<plugin>
	<groupId>org.codehaus.mojo</groupId>
	<artifactId>exec-maven-plugin</artifactId>
	<version>3.1.0</version>
	<executions>
		<execution>
		<id>publish-project</id>
		<phase>deploy</phase>
		<goals>
			<goal>exec</goal>
		</goals>
		<configuration>
			<executable>curl</executable>
			<arguments>
				<argument>-k</argument>
				<argument>-i</argument>
				<argument>-X</argument> 
				<argument>POST</argument> 
				<argument>-H</argument>
				<argument>"Content-Type: text/xml"</argument>
				<argument>-H</argument>
				<argument>"X-Apikey: ${proget-sca-api-key}"</argument>
				<argument>--fail-with-body</argument>
                <argument>-s</argument>
				<argument>-d</argument>
				<argument>@bom.xml</argument>
				<argument>${proget-sca-api}</argument>
			</arguments>
            <useMavenLogger>true</useMavenLogger>
			<workingDirectory>${project.build.directory}</workingDirectory>
		</configuration>
		</execution>
	</executions>
</plugin>
```

## Publish Your Dependencies
Once the plugins have been configured, your dependencies will then be published to a ProGet SCA project when `mvn deploy` is ran.




