### Version Catalogs without rootProject.name
This repository reproduces the caching issue observed when we enable 
`enableFeaturePreview("TYPESAFE_PROJECT_ACCESSORS")` in a project without `rootProject.name` set.

The component showing the cache issues was the Kotlin Gradle Plugin, the task was KotlinCompiler, and the 
input properties affected are:
* classpathSnapshotProperties
* kotlinJavaToolchainProvider

### Project
Simple project with Gradle 7.4 and Kotlin Gradle Plugin 1.6.10.

### Steps to Reproduce

1- Clone repository

2- Execute:  `./gradlew :lib:compileKotlin -Dorg.gradle.caching.debug=true`

3- Trigger Github Actions linked to this repo to execute the build in different environments

### Expected output


### Current output



### Previous Behavior
Branch `no-feature` contains the same configuration(Gradle 7.4 and
KGP 1.6.10) without `enableFeaturePreview("TYPESAFE_PROJECT_ACCESSORS")` 
in settings.gradle
