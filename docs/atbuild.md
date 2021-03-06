---
layout: documentation
tags: docs
---

# atbuild

[![Anarchy Tools compatible](https://img.shields.io/badge/Anarchy%20Tools-compatible-4BC51D.svg?style=flat)](http://anarchytools.org)

The Anarchy Tools Build Tool.

atbuild is a small, configurable, mature, and boring Swift buildsystem.

1.  It just builds your code.  That's it.  No bells, whistles, file downloads, or feature creep.
2.  It is extensible and can be easily customized to work how *you* want.
3.  It has no magic.  It doesn't guess.  It does what you tell it to do.
4.  It has no opinions.  It does not look down on you for writing code "wrong".
5.  It is used to build many large production projects.

It follows the [Anarchy Tools philosophy](https://github.com/AnarchyTools/anarchytools.github.io) of simple, unopinionated, hackable tools.

Key atbuild features:

* Builds Swift 2 & Swift 3 projects
* Can even build with Xcode's Swift
* Builds projects for OSX, Linux, and iOS (experimental)
* Can run custom scripts before, after, and during builds
* Customize all compile and link flags
* Powerful imports system for managing dependencies
* Overlays, custom tools, and external scripts allow maximum customization
* Uses the next-generation `swift-llbuild` engine
* Extensive [documentation](http://anarchytools.org)
* It's 1.0!

atbuild pairs well with:

* [atpm](https://github.com/AnarchyTools/atpm), the Anarchy Tools Package Manager
* [xcode-emit](https://github.com/AnarchyTools/xcode-emit) which emits Xcode projects from an atbuild configuration
* [SublimeAnarchy](https://github.com/AnarchyTools/SublimeAnarchy), the Swift IDE for Sublime Text 3
* Many [other AnarchyTools projects](https://github.com/AnarchyTools)!

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

* `--use-overlay [overlay]`, which you can read more about in our [overlays](http://anarchytools.org/docs/overlays.html) documentation.
* `-f [atpkg-file]` which builds a package file other than `build.atpkg`
* `--help`, which displays a usage message
* `--clean`, which forces a clean build
* `--toolchain` which specifies a nonstandard toolchain (swift installation).  By default we try to guess, but you can override our guess here.  The special string `xcode` uses "xcode swift" for building.  (Swift 2.2 does not contain all the tools we use, so you need to have a 3.x snapshot installed as well.  However, this is a "mostly" xcode-flavored buildchain.)
* `--platform` which specifies the target platform.  By default, this is the current platform.  Pass a different value to cross-compile, see [platforms](http://anarchytools.org/docs/platforms.html) for details.
* `--configuration`, which specifies the active [configuration](http://anarchytools.org/docs/configurations.html)

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
