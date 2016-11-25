---
layout: post
title: Gradle Modules in Unusual Places
---
I was recently involved in consolidating a number of related projects into a single repository, primarily to remove the scourge of versioning internal modules which were of no interest to anyone outside the project. As they were all using Gradle, we changed these to become [multi-project builds](https://gradle.org/docs/current/userguide/multi_project_builds.html). However, given the aforementioned libraries were used by multiple applications, they didn't fit into the traditional tree structure for these.

While Gradle is quite happy to pick up submodules structured as siblings, it does lead to a somewhat uncomfortable reference style. For instance, let's assume a structure of:

    app-project
    + build.gradle
    + settings.gradle
    lib-project
    + build.gradle
    
You can create the dependency of *app-project* on *lib-project* by creating a *settings.gradle* with the contents of:
```
include ":../lib-project"
```
And then reference this as a dependency in *build.gradle*:
```
dependencies {
  compile project(":../lib-project")
}
```
While this works, you do end cluttering up the build output, comlicated the configuration and tying the library to it's filesystem location. 

Luckily, with a little fun in the *settings.gradle* you can break the link:
```
include ":lib-project"
project(":lib-project").projectDir = new File(settingsDir, "../lib-project")
```
And then simplify *build.gradle* to use:
```
dependencies {
  compile project(":lib-project")
}
```
As an extra bonus, because we turned this into a multi-project build rather late in the process, we didn't take advantage of sharing configuration between the builds, this leaving them decoupled. This means we can make use of [parallel project execution](https://gradle.org/docs/current/userguide/multi_project_builds.html#sec:parallel_execution), which gives a very decent speed boost for project with multiple dependencies.