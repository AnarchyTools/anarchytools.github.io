---
layout: documentation
tags: docs
---

# shell

The shell tool allows you to call a shell command.

# API

```clojure
:taskname {
    :tool "shell"

    ;;run the following script in /bin/sh.
    ;;A non-zero return code indicates that build should halt.
    ;;this field supports variable expansion.
    :script "echo hello world"
}
```

# Environment variables
 
## `ATBUILD_USER_PATH`
 
The `ATBUILD_USER_PATH` contains the path to a "user" directory.  You can use this directory however you like.
 
The directory is preserved across all tasks that are part of the same dependency chain, and is cleared between invocations to `atbuild`.
 
A common use of the `ATBUILD_USER_PATH` is to specify include information; see atllbuild's `includeWithUser` [documentation](/docs/atllbuild.md) for more information.

## `ATBUILD_PLATFORM`

The platform currently being targeted.  Valid values are the same as those passed to `--platform` from [atbuild](atbuild.html)

## `ATBUILD_PACKAGE_VERSION`

The version of the package for the current task.  If the package has no version, this environment variable will not be present.

## `ATBUILD_BIN_PATH`

The absolute path to the `bin` folder for the current execution.  Note that this will be different than `workingDirectory+"/bin"` in the case that the task is imported.

## Configurations

The internal configuration API is also exposed via environment variable.  See [configurations](configurations.html) for more details.

