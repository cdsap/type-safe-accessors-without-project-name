### Version Catalogs without rootProject.name
This repository reproduces the caching issue observed when we enable 
`enableFeaturePreview("TYPESAFE_PROJECT_ACCESSORS")` in a project without `rootProject.name` defined in 
settings.gradle.

In this example, the component affected is the Kotlin Gradle Plugin, the task KotlinCompiler, and the 
input properties with cache differences are:
* classpathSnapshotProperties
* kotlinJavaToolchainProvider

### Project
Simple project with Gradle 7.4 and Kotlin Gradle Plugin 1.6.10.

### Steps to Reproduce

1- Clone repository

2- Execute:  `./gradlew :lib:compileKotlin -Dorg.gradle.caching.debug=true`

3- Trigger Github Actions linked to this repo to execute the build in different environments


### Current output

|                             | Local                              | CI-Linux                                 | CI-Mac                             |
|-----------------------------|------------------------------------|------------------------------------------|------------------------------------|
| :lib:compileKotlin          | 8f55d208846f869e716079f8ce6d64db   | fa441df03e72302a4ad57f3d72afaa4c         | fa441df03e72302a4ad57f3d72afaa4c   |
| classpathSnapshotProperties | 794ee8836a93f7b521a667b6a231372e   | e5facd282957420c8bc21a116db6b1a5         | e5facd282957420c8bc21a116db6b1a5   |
| kotlinJavaToolchainProvider | 665d11ffe53493d810603c72f424ef67   | d92d280e3d121894ad311f582f7d688b         | d92d280e3d121894ad311f582f7d688b   |
| classpath                   | fa7bf5060d9977722cf303b812013991   | fa7bf5060d9977722cf303b812013991         | fa7bf5060d9977722cf303b812013991   |
| Build scan                  | https://gradle.com/s/qs634loojs3hy | https://scans.gradle.com/s/k32lsjeh4g34g | https://gradle.com/s/vi4yuyzgssgno |

### Expected output

Same Kotlin Compiler cache key for Local, CI-Linux and CI-Mac

### Previous Behavior
Branch `no-feature`(#2) contains the same configuration(no project name, Gradle 7.4 and
KGP 1.6.10) without `enableFeaturePreview("TYPESAFE_PROJECT_ACCESSORS")`
in settings.gradle. Executing the build in local/ci-mac/ci-linux returns the same cache key:
`6653987ca103a8f54e559b150035fb54`for `:lib:compileKotlin`.

|                             | Local                              | CI-Linux                           | CI-Mac                             |
|-----------------------------|------------------------------------|------------------------------------|------------------------------------|
| :lib:compileKotlin          | 6653987ca103a8f54e559b150035fb54   | 6653987ca103a8f54e559b150035fb54   | 6653987ca103a8f54e559b150035fb54   |
| classpathSnapshotProperties | 794ee8836a93f7b521a667b6a231372e   | e5facd282957420c8bc21a116db6b1a5   | e5facd282957420c8bc21a116db6b1a5   |
| kotlinJavaToolchainProvider | 665d11ffe53493d810603c72f424ef67   | d92d280e3d121894ad311f582f7d688b   | d92d280e3d121894ad311f582f7d688b   |
| classpath                   | fa7bf5060d9977722cf303b812013991   | fa7bf5060d9977722cf303b812013991   | fa7bf5060d9977722cf303b812013991   |
| Build scan                  | https://gradle.com/s/kdcbgbh37ztc2 | https://gradle.com/s/llzizgw2tpiik | https://gradle.com/s/6lan2iiopumqc |

### Fix 
Include `rootProject.name`

### Additional notes
Between test different scenarios I had to clean manually the `.gradle` folder to regenerate the 
generated code for TYPESAFE_PROJECT_ACCESSORS 
