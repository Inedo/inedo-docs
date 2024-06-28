---
title: "Unit Tests"
order: 1
---

Unit tests are one of the earliest stages of automated testing that take place during a build and deployment pipeline and are typically run by a third-party tool or framework such as Visual Studio, NUnit, and others. In BuildMaster, unit tests are executed with specific operations in a deployment plan. The results are recorded and linked to the build in context.

Unit tests runs are organized into groups, a convenient mechanism to separate results of different test projects, consolidate common tests for historical comparison, or identify specific tests to use in a [pipeline stage approval](#pipeline-stage-approval).

### Running Unit Tests

Unit tests are run and recorded with specific operations depending on the unit test framework. The following test frameworks are supported by BuildMaster through the following extensions and operations:


| Framework | Extension | Operation | GitHub |
| --- | --- | --- | --- |
| Visual Studio Test | [Windows SDK](https://proget.inedo.com/feeds/Extensions/inedox/WindowsSDK) _(formerly MSTest)_ | `Execute-VSTest` | [source](https://github.com/inedo/inedox-windowssdk) | [docs](https://github.com/inedo/inedox-windowssdk/wiki) |
| NUnit | [NUnit](https://proget.inedo.com/feeds/Extensions/inedox/NUnit/) | `Execute-NUnit` | [source](https://github.com/inedo/inedox-nunit) | [docs](https://github.com/inedo/inedox-nunit/wiki) |
| JUnit | Java | `Execute-JUnit` | [source](https://github.com/inedo/inedox-java) | [docs](https://github.com/inedo/inedox-java/wiki) |

For instructions on usage of a specific unit test operation, visit the auto-generated documentation in your BuildMaster instance or the associated extension's GitHub wiki.

### Pipeline Stage Approval

To prevent a build with failed unit tests from being deployed to a pipeline stage, add a Unit Test automated approval to the target stage. This approval verifies that the tests recorded at the last execution in the previous pipeline stage either all passed or none failed, with inconclusive tests that depend on the approval configuration.

### Viewing Test Results and History

All executed tests are recorded and displayed on the overview page of the associated build, organized by group. Logs and histories for specific tests are available on the Unit Test Details page.

#### Custom Unit Tests

Because operations are extensible, it is relatively easy to create custom operations for any unit test framework. For examples, see the [source code](https://github.com/inedo) for any of the Inedo supported extensions and the [`IUnitTestRecorder` documentation](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Agents/IUnitTestRecorder) of the Inedo.SDK.