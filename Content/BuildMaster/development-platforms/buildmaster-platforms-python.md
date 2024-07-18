---
title: "Python"
order: 5
---


Python has become a popular development platform for a wide range of applications, from websites with a Flask/uWSGI stack to desktop-based tools to machine learning and data processing scripts.

Because Python is an interpreted language, it's very easy to share Python scripts and applications: just send them to the end user as a .zip file or copy them to a shared drive.

For simple applications, this sharing method is generally functional, although it sometimes proves suboptimal. However, as applications become more important to the business, a more formal deployment and delivery process is essential to reduce the time and patience wasted working with incorrect .zip files.
  
## How BuildMaster Can Help with Python Applications 
  
BuildMaster ensures consistently fast and reliable deployment, regardless of scope. BuildMaster can create builds with versioning that produce a single artifact. You can test these builds in BuildMaster, deploy them to your servers, and send them to end users.
  
### Configuring BuildMaster to Build Python Applications  

::: (INFO)
**See it live!** Create a new application using the *Python CI/CD template* in your own instance of BuildMaster.
:::

The process of using Python in BuildMaster is simple:

 - Start by getting code from your [source control repository](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control/buildmaster-ci-cd-continuous-integration-server-source-control) (from GitLab, GitHub, SVN, etc.)
 - You can also use a [drop folder](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts/buildmaster-drop-folders)
 - Next [install dependent packages using pip](#using-pip-and-buildmaster)
 - Then [execute unit tests against your code](#using-pyunit-and-buildmaster)

This gives you a package as an artifact or universal package.  

## Python Deployment Anti-Pattern: Pull and Pray

Many Python developers deploy their code the same way they develop it: the "pull and pray" method. This means that the code is cloned with Git and then the dependencies are installed with pip.

A deploy script might look something like this:

````  
git clone https://githome.local/kramerica-industries/profitcalc.git
pip install -r requirements.txt  
python run_profitcalc.py  
````  

Although it's possible to do this in BuildMaster, we don't recommend it. The Git+pip strategy leads to problems, especially for applications running on multiple servers and for applications that you really depend on, as code built on different hosts can lead to configuration inconsistencies that mess up existing dependencies.

You can also see deployment failures if your Git server is down because `pip install` and `git pull` often rely on different external servers. You can choose to use third-party systems (e.g., Github, PyPI) or set up your own servers.

Whether you rely on external systems like Github or PyPI for your pip and git pulls or use a self-managed system, you want to have the same uptime and scalability for everything, especially for large deployments.
  
## Managing your Python Application Configuration in BuildMaster 

Python applications rely on configuration values that can change from environment to environment and even include sensitive data such as database connection strings, third-party service URLs, API keys, and so on.

There are several approaches to handling this type of configuration in Python:


 - https://martin-thoma.com/configuration-files-in-python
 - .py files that are included and directly incorporate those configuration values as runtime variables
 - .json, .yaml, .ini, or other data files that are parsed and read by the application

Both methods are equally popular; it's generally a matter of preference for your development team. Regardless, BuildMaster manages both in the same way, using configuration files. Configuration file assets store configuration files with multiple instances and provide a multi-table editor for editing and comparing those instances. (See the [Configuration Files documentation](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files) to learn more.)  
  
## Deploying Python Web Applications with BuildMaster 

Python applications can be run from a variety of hosts, from the `python` runtime itself to web servers like Apache, nginx, or IIS. We recommend following this process:


 - Stop hosting services
 - Deploy application files
 - Start hosting services

The simplest version looks like this:
  
````  
exec systemctl stop uwsgi;  
Deploy-Artifact Hdars;  
exec systemctl start uwsgi;  
````  
  
Since you cannot overwrite code that is running, you must stop it before you make your changes, then restart the code for the changes to take effect.

There are also advanced patterns like [blue/green](/docs/buildmaster/deployment-continuous-delivery/buildmaster-ci-cd-deployment-patterns/buildmaster-ci-cd-deployment-patterns-blue-green).  
    
### Deploying Python Desktop/Utility Applications with BuildMaster 

When you build a Python desktop application or utility, there are many different ways you can distribute it to end users. By far the most common way is to send it in a .zip file, but as we said above, that can get messy.

With BuildMaster, you can simply point to the build page and tell the recipient to click on the artifact. If they don't have access to BuildMaster, you could use the `Send-Email` operation and attach the artifact as a .zip file. Even though you're using a .zip file, BuildMaster is the single source of truth for that particular build, eliminating the headaches of working with different versions of a build.  
  
### Distributing as an Executable Windows Program 

While source distribution is easy, it does require that your end users have Python installed. However, you can use [py2exe](http://www.py2exe.org/) to distribute as an executable without requiring Python. If you're unfamiliar with py2exe, follow [this tutorial](http://www.py2exe.org/index.cgi/Tutorial).

To use py2exe with BuildMaster, simply use the `Exec` operation and capture an artifact using the `Create-Artifact` operation from the `dist` subfolder:  
  
````  
exec python setup.py py2exe;  
  
Create-Artifact HDarsDist  
(  
    From: dist  
);  
````  

Now that you have an artifact with your bundled Python executable, you can send links to it to your end users or email the artifact as a .zip file.
  
## Running Unit Tests Against Your Python Application

BuildMaster supports [automatically running unit tests](/docs/buildmaster/builds-continuous-integration/automated-testing-verification/buildmaster-ci-cd-testing-and-verification-unit-tests) for your Python applications.  
Unit testing offers several benefits, including automatically detecting issues before human testing, which saves time and moves issues further to the left in your pipeline.

To test with BuildMaster, use the `Python::Execute-PyUnit` operation. You can also see this live in the `Build-Advanced` OtterScript plan in the template application.  
  
## Using pip with BuildMaster 

To install packages in BuildMaster, you can use the `Python::Install-Packages` operation.

As general best practice, you should freeze your dependencies using the `pip freeze` command and check in your `requirements.txt` file along with the rest of your code. This ensures that you have a consistent and repeatable build from the same set of source code. If you don't freeze your dependencies, you may end up using different versions of packages each time you build from the same code, which can lead to bugs that aren't always easy to fix (especially if they come from a third party). Remember that you should run pip _before_ capturing the artifact.
  
### Reporting on Packages Used and Their Licenses 

BuildMaster also allows you to run reports on packages and licenses used by those packages, which protects your organization from potential liability due to hard-to-find license information.

Using third-party open-source packages comes with their license terms. For example, if you use a GNU3-licensed package in your application, you must open source your application and license it under GNU3. If you do not, your company could face a lawsuit from the package authors. Worse, it's difficult to find these licenses. You have to dig through source control and then go to each package to find the information you need.

BuildMaster can automatically capture the licenses that third-party packages are using with the `Python::Capture-PackageInfo` operation. You can also add a manual approval, so a human must verify the licenses before deploying to production.

::: (INFO)
You can see both of these **live** in the `Build` OtterScript plan in the template application.  
:::  
  
## CI/CD for Python Applications
 
Once you've set up your build and deploy process, using Continuous Integration and Continuous Delivery for Python is easy. Just configure a [build trigger](/docs/buildmaster/administration/buildmaster-resource-monitors) to automatically monitor for changes and you're good to go! 