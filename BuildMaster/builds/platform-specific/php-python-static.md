---
title: PHP, Python & More
subtitle: Building a PHP, Python, or Other Static Application
keywords: php, python, buildmaster
sequence: 600
---

A lot of developers work with source code that does not require a typical "build" task, but still requires packaging. If you have source code written in PHP or Python you'll know that you don't require a build step in your deployment process. However, you'll still want to implement a pipeline for packaging and deploying your code to different stages. 

An artifact is just a zip file that contains the entire application or component that will be deployed to each environment. All of the files! Not just the ones that changed

## Packge an Application's Source Code as a Build Artifact

If your source code is stored a Git repository you can create your git credentials and have them point directly to your repository. 
```
Git::Get-Source
(
    Credentials: hdars-git,    
    Branch: dev
);

Create-Artifact HDarsPhpSite
```

Now that you have your deployable artifact you can utilize a new BuildMaster plan to deploy your artifact to a different target in your pipeline. 

```
Deploy-Artifact HDarsPhpSite
(
    To: /var/www/HdarsPhpSite
);
```