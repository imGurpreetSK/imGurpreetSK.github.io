+++
date = '2026-01-03T16:55:42-08:00'
draft = false
title = 'Experimental Feature Flag Cascading in Kotlin Modules'
author = "Gurpreet"
tags = ["kotlin"]
+++

While working today with a Kotlin Multiplatform project, I decided to use Kotlin 2.3.0 to able to use explicit backing fields in my view model to succinctly expose state objects. The use of the [experimental feature](https://kotlinlang.org/docs/whatsnew23.html#explicit-backing-fields) is straightforward and heavily requested by the community. 

TLDR, if you enable explicit backing fields in the KMP module, but not in the Android (or another upstream) module, the build fails with error stating "_class `X.Y.Z` was compiled by a pre-release version of Kotlin and cannot be loaded by this version of compiler_".

This points to a Kotlin version mismatch between compiled artifact and after looking a bit into it, the root code essentially is that Kotlin marks the compiled bytecode as 'pre-release', prevents other modules from loading these classes unless they also add this compiler flag for the experimental feature.

The most straightforward fix was to add the flag to compile options for my Android app and things started working

```
// :androidApp:build.gradle.kts

kotlin {
    compilerOptions {
        jvmTarget = JvmTarget.JVM_17
        freeCompilerArgs.add("-Xexplicit-backing-fields") // Ensuring pre-release classes are loaded during build.
    }
}
```

Of course, you can remove the experimental feature flag and migrate to kotlin 2.3.0 to a later point in time once the feature is out of experimental phase, however [that might take quite a while](1).

----------------------------

### Footnotes

There is no fixed timeline. Kotlin's stability levels (Experimental → Alpha → Beta → Stable) do not indicate how soon a feature will stabilize. Duration varies based on:
  - Community feedback
  - Design complexity
  - Implementation challenges

  Some features stabilize within 1-2 minor versions; others take years across multiple major versions.

  | Feature                           | Introduced                 | Status                        | Duration                     |
  |-----------------------------------|----------------------------|-------------------------------|------------------------------|
  | Inline Classes                    | 1.2.30 (2018) Experimental | Stable in 1.5 (2021)          | ~3 years                     |
  | Value Classes                     | 1.5 (2021)                 | Stable (single property only) | Multi-property still pending |
  | Data Objects                      | 1.7.20 (2022) Experimental | Stable in 1.9 (2023)          | ~1 year                      |
  | Generic Inline Classes            | 1.7.20 (2022) Experimental | Stable in 2.0 (2024)          | ~2 years                     |
