---
layout: documentation
tags: docs
---

# Platforms

Platforms in `atbuild` are more explicit than in other tools you may have used.

atbuild supports these "specific" platforms:

* `linux`, which means Linux x64
* `osx` which means OSX x64
* `ios-x86_64` which is a 64-bit iOS Simulator
* `ios-i386` which is a 32-bit iOS Simulator
* `ios-armv7` which is a 32-bit iOS device
* `ios-arm64` which is a 64-bit iOS device

These platforms can be used anywhere and identify an exact device target.  Our low-level build tools work with these values to generate code for the chosen ISA.

In addition, atbuild supports the following "generic" platforms:

* `ios`, an iOS platform of unspecified architecture

These platforms are valid for tools that need to know the OS, but don't need to know the exact ISA.  However,  `[atllbuild](atllbuild.html)`, our *low level build tool*, compiles for a single, *specific* platform.  So it will not accept a generic platform directly.

To create a fat binary for all architectures in a generic platform, simply chain [`atllbuild`](atllbuild.html) with [`packageatbin`](packageatbin.html) as shown:

```clojure
(package
  :name "atbin"
  
  :tasks {
    :lib {
      :tool "atllbuild"
      :name "foo"
      :output-type "static-library"
      :sources ["lib/**.swift"]
    }
    :package {
      :tool "packageatbin"
      :name "foo"
      :atllbuild-task "lib"
      :platforms ["all"]
    }
  }
)
```

and run with `atbuild --platform ios`.  This will re-run the `lib` task for each specific platform in the `ios` generic platform.