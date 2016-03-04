---
layout: documentation
tags: docs
---

# Tasks

All tasks have the following options:

```clojure
taskname: {
    ;;the name of a tool.  Valid tools are shell, atllbuild, nop
    :tool "tool"

    ;;What other tasks should run before this one.
    :dependencies []

    ;;see docs/overlays.md for more information about overlays
    :use-overlay [] ;;what overlays to apply to this task
    :overlays { } ;;what overlays can be applied to this task

    :required-overlays [] ;;what overlays are required for this task
}
```
