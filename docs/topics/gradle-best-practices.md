[//]: # (title: Gradle best practices)

Getting the best out of Gradle is essential to help you spend less time managing and waiting for builds, and more time 
coding. Here we provide a set of best practices split into two key areas: **organizing** and **optimizing** your projects.

## Organize

This section focuses on structuring your Gradle projects to improve clarity, maintainability, and scalability.

### Use a version catalog

Use a version catalog in a `libs.versions.toml` file to centralize dependency management, enabling you to define and 
reuse versions, libraries, and plugins consistently across projects.

```none
[versions]
kotlinxCoroutines = "%coroutinesVersion%"

[libraries]
kotlinxCoroutines = { module = "org.jetbrains.kotlinx:kotlinx-coroutines-core", version.ref = "kotlinxCoroutines" }
```

With the following dependency added to your `build.gradle.kts` file:

```none
dependencies {
    implementation(libs.kotlinxCoroutines)
}
```

Learn more in Gradle's documentation about [Dependency management basics](https://docs.gradle.org/current/userguide/dependency_management_basics.html#version_catalog).

### Use Kotlin DSL

Use Kotlin DSL instead of the traditional Groovy DSL. You avoid learning another language and gain the benefits of strict
typing. Strict typing lets IDEs provide better support for refactoring and auto-completion, making development more efficient.

Find more information in [Gradle’s Kotlin DSL primer](https://docs.gradle.org/current/userguide/kotlin_dsl.html).

Read Gradle's [blog](https://blog.gradle.org/kotlin-dsl-is-now-the-default-for-new-gradle-builds) about Kotlin DSL becoming
the default for Gradle builds.

### Use convention plugins

<primary-label ref="advanced"/>

Use convention plugins to encapsulate and reuse common build logic across multiple build files. Moving shared configurations
into a plugin helps simplify and modularize your build scripts.

Although the initial setup may be time-consuming, you will find it easy to maintain and add new build logic once you complete it.

Learn more in Gradle's documentation about [Convention plugins](https://docs.gradle.org/current/userguide/custom_plugins.html#sec:convention_plugins).

## Optimize

This section provides strategies to enhance the performance and efficiency of your Gradle builds.

### Use local build cache

Use a local build cache to save time by reusing outputs produced by other builds. The build cache can retrieve outputs from 
any earlier build that you have already created.

Learn more in Gradle's documentation about their [Build cache](https://docs.gradle.org/current/userguide/build_cache.html).

### Use configuration cache

> The configuration cache doesn't support all core Gradle plugins yet. For the latest information, see Gradle's
> [table of supported plugins](https://docs.gradle.org/current/userguide/configuration_cache.html#config_cache:plugins:core).
>
{style="note"}

Use the configuration cache to significantly improve build performance by caching the result of the configuration phase
and reuse it for subsequent builds. If nothing changes in a build file (or its dependencies) the build file isn't
recompiled.

Learn more in Gradle's documentation about their [Configuration cache](https://docs.gradle.org/current/userguide/configuration_cache.html).

### Improve build times for multiple targets

When your multiplatform project includes multiple targets, tasks like `build` and `assemble` can compile the same code 
multiple times for each target, leading to longer compilation times.

If you're actively developing and testing a specific platform, run the corresponding `linkDebug*` task instead.

For more information, see [Tips for improving compilation time](https://kotlinlang.org/docs/native-improving-compilation-time.md#gradle-configuration).

### Migrate from kapt to KSP

If you're using a library that relies on the [kapt](kapt.md) compiler plugin, check whether you can switch to using the [Kotlin Symbol Processing (KSP) API](https://kotlinlang.org/docs/ksp-overview.html)
instead. The KSP API improves build performance by reducing annotation processing time. KSP is faster and more efficient
than kapt, as it processes source code directly without generating intermediary Java stubs.

To learn more about how KSP compares to kapt, check out [why KSP](ksp-why-ksp.md).

### Use modularization

<primary-label ref="advanced"/>

> Modularization only benefits projects of moderate to large size. It doesn't provide advantages for projects based
> on a microservices architecture.
>
{style="note"}

Use a modularized project structure to accelerate build speed and enable easier parallel development. Structure your
project into one root project and one or more subprojects. If there are changes in only one of the subprojects, Gradle
rebuilds only that specific subproject.

```none
.
└── root-project/
    ├── settings.gradle.kts
    ├── app subproject/
    │   └── build.gradle.kts
    └── lib subproject/
        └── build.gradle.kts
```

Learn more in Gradle's documentation about [Structuring projects with Gradle](https://docs.gradle.org/current/userguide/multi_project_builds.html).

### Set up CI/CD
<primary-label ref="advanced"/>

Set up a CI/CD process to significantly reduce build time by using incremental builds and caching dependencies. You can
do this by setting up persistent storage or using a remote build cache. This process doesn't have to be time-consuming, 
as some providers, like GitHub, offer this service almost out of the box.

Explore Gradle's community cookbook on [Using Gradle with Continuous Integration systems](https://community.gradle.org/cookbook/ci/).