---
layout: documentation
tags: docs
---

# packageframework

The `packageframework` tool allows you to package an existing dynamic library into a Mac framework.

This tool is only suported on OSX.

```clojure
:taskname {
    :tool "packageframework"

    ;;This value is required.  No other module map types are supported.
    :module-map-type "synthesized"

    ;;The name of a module previously published to `bin/`
    ;;Also used as the framework name.
    ;;If built with atllbuild, make sure to specify `:framework true` in the `atllbuild` task.
    :name "module_name"

    ;;Add at least an info plist.  Other resources can also be declared here.
    :resources ["Info.plist"]
}
```