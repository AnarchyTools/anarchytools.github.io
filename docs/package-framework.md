---
layout: documentation
tags: docs
---

# package-framework

[`package-framework`](https://github.com/AnarchyTools/package-framework) is an [`atbin`](atbin.html) packager that builds Mac/iOS Frameworks.  You can use it to convert the atbin format into `Framework`s.

# Complete example

```clojure
(package
    :name "FooFramework"
    :tasks {
            ;; build the Swift code as a dynamic library
            :default {
                :tool "atllbuild"
                :output-type "dynamic-library"
                :sources ["src/**.swift"]
                :name "FooFramework"

                ;; always use this option when building code for frameworks
                :framework true
            }

            ;; package into atbin format
            :packageatbin {
                :tool "packageatbin"
                :name "FooFramework"
                :platforms ["all"]
                :atllbuild-task "default"
            }

            ;; convert to framework
            :package {
                :tool "package-framework.attool"

                ;; specifies the atbin to package
                ;; and the resulting framework name
                :name "FooFramework"

                ;; path to the info plist for this framework
                :info-plist "Info.plist"

                :dependencies ["packageatbin"]
            }
    }
)
```

# Remarks

It's worth pointing out a few idiosynchrocies related to frameworks:

* You probably want to build with the xcode toolchain as most framework consumers are using Xcode as their build tool and they must match.  See the [atbuild](atbuild.html) documentation for more information on toolchains.
* iOS users typically expect bitcode to be enabled, see the [atllbuild](atllbuild.html) documentation to enable it

# Deprecation

`atbuild` has a built-in `packageframework` tool, this is deprecated, and will be removed from a future version of atbuild.