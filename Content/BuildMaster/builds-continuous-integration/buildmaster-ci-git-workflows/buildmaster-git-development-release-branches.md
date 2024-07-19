---
title: "Development and Release Branches"
order: 2
hidden: true
---

# Develop/Release Branches
[Intro]
 - What is Develop and Release Branch
 - What is pipeline template
 - Two pipelines

  * in Git
    - branch named 'develop' that contains unstable, "working code"
      - potentially merged features branches
    - branch named 'main' and/or branches named 'release/*' for stable, release-targeting code
      - merge from develop or commit directly, depending on workflow

  * Pipelines
    - dev pipeline: Build > Int > Reject
    - rel pipeline: standard envir
   * Alternative: Single Pipeline
     - both can share
     - use a variable automated check to prevent builds from going to test
     - advantage: one pipeline
     - disadnvatge: not as clear (colors can be used, etc)
     - good when your whole team shifts from "dev" to "release" branch