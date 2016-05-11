---
layout: documentation
tags: docs
---

# package-homebrew

[package-homebrew](https://github.com/AnarchyTools/package-homebrew) is an [atbin](atbin.html) packager that builds homebrew packages.  You can use it to create free homebrew packaging as an output from the AT toolchain.

# Complete example

```clojure
:tasks {

    ;; Build our Swift executable
    :build {
        :tool "atllbuild"
        :sources ["src/**.swift"]
        :name "foo"
        :output-type "executable"
    }

    ;; Package for the atbin format
    :atbin {
      :tool "packageatbin"
      :name "foo"
      :atllbuild-task "build"
      :platforms ["all"]
    }

    ;; Repackage atbin for homebrew
    :package-osx {
      :tool "package-homebrew.attool"

      ;;The name of an atbin to repackage
      :name "foo"

      ;;Ensure the atbin was built by the time we got here 
      :dependencies ["atbin"]

      ;; The location of the project on GitHub.
      ;; If specified, we predict the url from GitHub format.
      ;; Either this or a url must be specified.
      :github-project "AnarchyTools/MyGreatProject"

      ;; URL where atbin.tar.xz will be hosted
      :url "https://www.example.com/foo-1.0-osx.tar.xz"
    }
}
```