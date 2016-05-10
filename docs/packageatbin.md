---
layout: documentation
tags: docs
---

# `packageatbin`

`packageatbin` is a builtin `atbuild` tool that packages binaries for the [`atbin`](atbin.html) format.

```clojure
:package {
   :tool "packageatbin"

   ;; Generate a mypayload.atbin
   :name "mypayload"

   ;; Builds all platforms allowed by the `--platform` CLI argument
   ;; You can list specific platforms to narrow (not change) the `--platform` argument.
   :platforms ["all"]

   ;; The atllbuild task to package.
   ;; Special logic will re-run this task for each platform and merge the resulting output.
   :atllbuild-task "myatllbuildtask"
}
```

