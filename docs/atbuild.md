---
layout: documentation
tags: docs
---

# atbuild

[`atbuild`](https://github.com/AnarchyTools/atbuild) is a command-line build tool, that builds software packages.

The Anarchy Tools Build Tool.

`atbuild` is a cross-platform build system, primarily for Swift-language projects, following the [Anarchy Tools philosophy](http://anarchytools.org/) of simple, unopinionated, hackable tools.

# Tasks

With `atbuild` you define a set of *tasks*, representing high-level operations like building, running, and cleaning.  Then you can use these tasks from the command line.

```bash
$ atbuild build
$ atbuild build-tests
$ atbuild run-tests
$ atbuild #runs a task named "default"
```


Tasks are defined in a clojure-like format.  Here's a simple example:

```clojure
;; This is a comment

(package
  :name "foo"

  ;;These "tasks" are just entrypoints on the CLI.
  ;;For example, `atbuild build` runs the `build` task.
  :tasks {
    :run {
      :tool "shell"               ;;Tools are functions built into atbuild.
      :script "echo Hello world!" ;;The shell tool requires a script
    }
  }
)
```

Name that `build.atpkg`.  Now we can call 

```bash
$ atbuild run
Building package foo...
Running task run...
Hello world!
Completed task run.
Built package foo.
```

# Building Swift code

How do we build a Swift project?  There's a built-in tool called `atllbuild`, which is our *low-level build system*.

```clojure
(package
  :name "foo"

  ;;These "tasks" are just entrypoints on the CLI.
  ;;For example, `atbuild build` runs the `build` task.
  :tasks {
    :build {
      :tool "atllbuild"
      :sources ["src/**.swift"] ;;walk the src directory, looking for Swift files
      :output-type "executable"
      :name "example"
    }
  }
)
```

That's all you need to get started!  `atbuild` supports many more usecases than can fit in a README.  For more information, browse our [documentation](http://anarchytools.org).

# Options

`atbuild` supports several command-line options:

* `--use-overlay [overlay]`, which you can read more about in our [overlays](overlays.html) documentation.
* `-f [atpkg-file]` which builds a package file other than `build.atpkg`
* `--help`, which displays a usage message
* `--clean`, which forces a clean build
* `--toolchain` which specifies a nonstandard toolchain (swift installation).  By default we try to guess, but you can override our guess here.  The special string `xcode` uses "xcode swift" for building.  (Swift 2.2 does not contain all the tools we use, so you need to have a 3.x snapshot installed as well.  However, this is a "mostly" xcode-flavored buildchain.)

# Building

We publish [binary releases](https://github.com/AnarchyTools/atbuild/releases), which are the easiest way to get started.

`atbuild` is self-hosting, so building it with an existing copy is usually your best bet.

That said, if you're doing something fancy, or are bootstrapping onto a new platform, use `./bootstrap/build.sh`.

Then you can check the program was built successfully:

```bash
$ ./atbuild --help
atbuild - Anarchy Tools Build Tool 0.1.0-dev
https://github.com/AnarchyTools
© 2016 Anarchy Tools Contributors.

Usage:
atbuild [task]
    task: ["default", "helloworld", "bootstrap"]
```

*Maintainer note: if you edit this file, edit the one in [this repo](https://github.com/AnarchyTools/atbuild) as well.*