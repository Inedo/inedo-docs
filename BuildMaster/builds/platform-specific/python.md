# Building and Deploying Python Applications in BuildMaster

Python has become a popular development platform for a wide range of applications—from websites using a flask/uwsgi stack to desktop-based tools as well as scripts for machine learning and data processing.

Because Python is an interpreted language, it's very easy to share Python scripts and applications: Just send them a zip file to the end user or copy it to a share drive, and *voila*!

For simple applications, this is sharing method is generally functional, although it can sometimes prove less-than-optimal. But applications become more important to the business, a more formal deployment and delivery process is essential to reducing the problems of time and patience lost to working with the wrong zip files.  
  
## How BuildMaster Can Help with Python Applications
  
BuildMaster keeps deployments consistently fast and reliable, regardless of scale. BuildMaster can create builds, with versioning, that produce a single artifact. You can test these builds in BuildMaster, deploy to your servers, and send to end users.

  
## Configuring BuildMaster to Build Python Applications  

**See it live!** Create a new application using the *Python CI/CD template* in your own instance of BuildMaster.

The process of using Python in BuildMaster is relatively simple:

- Start by getting code from your [source control repository](/docs/buildmaster/builds/continuous-integration/source-control) (from GitLab, GitHub, SVN, etc.).
- You can also could use a [drop folder](/docs/buildmaster/builds/external-systems/drop-folder).
- Next [install dependent packages using pip](#using-pip-and-buildmaster).
- Then [execute unit tests against your code](#using-pyunit-and-buildmaster).

This gives you a package as an artifact or universal package.  

## Python Deployment Anti-Pattern: Pull and Pray  

Many Python developers deploy their code the same way they develop it: the “pull and pray.” This means cloning the code using Git and then installing dependencies via pip.

A deploy script might look something like this:  

````  
git clone [https://githome.local/kramerica-industries/profitcalc.git](https://githome.local/kramerica-industries/profitcalc.git)  
pip install -r requirements.txt  
python run_profitcalc.py  
````  

While it's possible to do this in BuildMaster, we don't recommend it. The Git+pip strategy causes problems, especially for apps running across multiple servers and apps that people really depend on because code built on separate hosts can create configuration inconsistencies that mess up existing dependencies.

You can also see deployment failures if your Git server is down. pip install and git pull oftentimes depend on external servers. You can choose to use third party systems (e.g. Github, PyPI) or setup your own servers.

Whether you’re relying on external systems like Github or PyPI for your pip and Git pulls or a self-managed system, you want to have the same up-time and scale for everything, especially large deployments.  
  
  
## Managing your Python Application Configuration in BuildMaster  

Python applications rely on configuration values that can change from environment to environment, and even contain sensitive data such as database connection strings, third-party service URLs, API keys, and so on.

There are different approaches for handling this sort of configuration in Python:

- [https://martin-thoma.com/configuration-files-in-python/](https://martin-thoma.com/configuration-files-in-python/)
- .py files that are included and directly incorporate those configuration values as runtime variables.
- .json, .yaml, .ini, or other data files that are parsed and read by the application.

Both methods are equally popular; it’s generally a matter of your Development team’s preferences. 

Either way, BuildMaster manages both in the same way, by using configuration file assets. Configuration file assets store multi-instance configuration files and provide a multi-tabled editor to edit and compare these instances. (See [http://docs.inedo.test/docs/buildmaster/deployments/configuration-files](http://docs.inedo.test/docs/buildmaster/deployments/configuration-files) to learn more.)  
  
## Deploying Python Web Applications with BuildMaster  

Python applications can be run by a variety of hosts, from the `python` runtime itself to application servers like apache, nginx, or IIS.You can also host in containers, though this involves a different process (for more on this, check out [container docs]).

We recommend following this process:

- stop
- deploy files
- start

The simplest version of this looks like this:  
  
````  
exec systemctl stop uwsgi;  
Deploy-Artifact Hdars;  
exec systemctl start uwsgi;  
````  
  
Since you can't overwrite code that's currently executing, you have to stop it before making your changes, then restart the code for the changes to take effect. 

There are also advanced patterns like [blue/green](inedo.com/docs/buildmaster/deployments/patterns/blue-green).  
  
  
## Deploying Python Desktop/Utility Applications with BuildMaster  

When you build a Python desktop application or utility, there are a lot of different ways you can distribute it to end users. By far the most common way is to send these in a zip file, but as we said above, this can get messy.

With BuildMaster, you can simply point to the Build page and tell the recipient to click on the artifact. If they don't have access to BuildMaster, you could use the `Send-Email` operation and attach the artifact as a zip file. Even though you’re still using a zip file, BuildMaster remains the single “source of truth” for that particular build, eliminating the headaches of working with different versions of a build.  
  
### Distributing as an Executable Windows Programs  

While source distribution is easy, it does require that your end users have Python installed. However, you can use [py2exe]([http://www.py2exe.org/](http://www.py2exe.org/)) to distribute as an executable without requiring Python. If you're unfamiliar with p2exe, follow [this tutorial](http://www.py2exe.org/index.cgi/Tutorial).

To use p2exe with BuildMaster, simply use the `Exec` operation and capture an artifact using the `Create-Artifact` operation from the `dist` subfolder:  
  
````  
exec python setup.py py2exe;  
  
Create-Artifact HDarsDist  
(  
From: dist  
);  
````  
Now that you've got an artifact with your bundled Python executable, you can send your end-users links to it or email the artifact as a zip.  
  
  
## Running Unit Tests against Your Python Application  

BuildMaster supports [automatically running unit tests](https://docs.inedo.com/docs/buildmaster/builds/tests/unit-tests) for your Python applications.  

Unit testing offers several benefits, including automatic detection of problems before human testing, saving time and pushing problems further left in your pipeline.

To test with BuildMaster, use the `Python::Execute-PyUnit` operation. You can also see this live in the `Build-Advanced` OtterScript plan in the template application.  
  
## Using pip with BuildMaster  

To install packages in BuildMaster, you can use the `Python::Install-Packages` operation.

As general best practice, you should "freeze" your dependencies using the `pip freeze` command and check in your `requirements.txt` file along with the rest of your code. This will ensure that you have a consistent and repeatable build from the same set of source code. If you don't freeze your dependencies, every time you build from the exact same code, it might use different versions of packages, which can introduce bugs that aren’t always easy to fix (especially if coming from a third party). Remember, you should run pip _before_ capturing the artifact.  
  
### Reporting on Packages Used and Their Licenses  
BuildMaster also lets you report on packages and licenses used by those packages, which protects your company from possible liability due to hard-to-find licensing info.

Using third-party, open source packages brings their licensing terms along for the ride. For example, using a GNU3-licensed package in your application requires you to open source your application and license it under GNU3. If you don’t do this, your organization could face a lawsuit from the package authors. Even worse: finding these licenses is challenging. You have to dig through source control and then go to each package to uncover the information you need.

BuildMaster can automatically capture the licenses that third-party packages are using:

- Use the Python::Capture-PackageInfo

You can also add a manual approval, so a human must verify the licenses before deploying to production.

You can see both of these **live** in the `Build-Advanced` OtterScript plan in the template application.  
  
  
## CI/CD for Python Applications 
 
Once you've set build and deploy, using Continuous Integration and Continuous Delivery for Python is easy. Just configure a [build trigger](http://docs.inedo.test/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors) to automatically monitor for changes, and you’re good to go!  

> Written with [StackEdit](https://stackedit.io/).