---
layout: documentation
tags: docs
---

# Configurations

`atbuild` supports a variety of built-in configurations:

* `debug`, the default, a configuration for interactive debugging
* `release`, a configuration for production-quality, released builds
* `test`, a configuration for automated testing
* `bench`, a configuration for performance-sensitive testing or benchmarking
* `none`, a "plain" or "no-magic" configuration.  Use this to opt out of any configuration behavior

`atbuild` also supports custom configurations.

# Use

To specify a configuration, pass it on the command line:

`atbuild --configuration release`.

Tools adjust their behavior to your chosen configuration automatically.  You can also provide your own settings in an `atbuild.configuration.[configurationname]` overlay.

# Internal API

`atbuild` provides a rich internal API.  Properties are exposed on the configuration object as well as via environment variable for shell / external tools.

Other than the first one, these are boolean properties that can be `true`, `false`, or `nil`/absent (meaning we don't know the correct value).

## `ATBUILD_CONFIGURATION` / `currentConfiguration`

This is the active configuration.  Note that using this value directly is discouraged; it's preferred to use one of the other APIs which are more future-proof to changes in the configuration definitions.

## `ATBUILD_CONFIGURATION_OPTIMIZE`

Whether to produce optimized software.

## `ATBUILD_CONFIGURATION_FAST_COMPILE` / `fastCompile`

Whether to prioritize compile speed over compile quality.  When this is `true`, you might be in the middle of a compile-run-test loop, so avoid doing anything expensive.

## `ATBUILD_CONFIGURATION_TESTING_ENABLED` / `testingEnabled`

Whether to compile "for testing".  This may control behaviors such as whether tests are compiled at all, whether the build product can be imported with `@testable`, or unlock other behavior related to testing.

##  `ATBUILD_CONFIGURATION_NO_MAGIC` / `noMagic`

Whether we should "opt out" of all magical behavior and rely only on explicit task options.  For example, no SDK might be selected, requiring the user to specify one manually.  Builds may be single-threaded.  Etc.