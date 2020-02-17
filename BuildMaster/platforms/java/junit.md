---
title: JUnit
subtitle: JUnit Tests in BuildMaster
keywords: unit test, buildmaster, junit, junit test, java
sequence: 400
---

BuildMaster is a fully-functional Continuous Integration tool for a variety of platforms allowing your organization to start using CI/CD today. If your are writing __Java applications__ and want to implement unit testing as part of your deployment pipeline Buildmaster can quite easilty accomodate that. 

####  What is JUnit? 
JUnit is a unit testing framework for Java programming language. JUnit has been important in the development of test-driven development, and is one of a family of unit testing frameworks collectively known as xUnit, that originated with JUnit.

#### Why should you use JUnit with BuildMaster?
You can use this automation framework for both unit testing and UI testing. You can set up buildmaster to require unit tests to pass before the deployment can go to the next stage. 

Execute the JUnit peraration
```
 Execute-JUnit
 (
     Include: JUnitTest.class,
     From: /usr/share/java/
 );
```

#### Pass or Fail
If your tests all pass you will see the results in your execution logs.  If __any__ of the tests happen to fail the error will be logged and the rest of your plan will be skipped. You may also want to wrap this operation in a try/catch to handle the error more cleanly or to do more with the test results like notify any interested parties. 

#### Pipeline Promotion 
Add an _Automatic Gate Approval_ with the type of _Unit Tests_ to your integration stage in your pipeline to prevent promotion to a stage if a unit test fails. (The promotion can ultimately be manually forced if needed). This will ensure that if the application has automatically been promoted to a stage that requires gate approval, it has has passed all unit tests. 

