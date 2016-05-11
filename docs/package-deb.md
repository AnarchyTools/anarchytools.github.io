---
layout: documentation
tags: docs
---

# package-deb

[package-deb](https://github.com/AnarchyTools/package-deb) is an [atbin](atbin.html) packager that builds deb packages.  You can use it to create free deb packaging as an output from the AT toolchain.

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

    ;; Repackage atbin for Debian
    :package-linux {
      :tool "package-deb.attool"

      ;;The name of an atbin to repackage
      :name "foo"

      ;;Ensure the atbin was built by the time we got here 
      :dependencies ["atbin"]

      ;; supply the Debian "depends" field
      ;; https://www.debian.org/doc/debian-policy/ch-relationships.html
      :depends "build-essential"


      ;; supply the Debian "recommends" field
      ;; https://www.debian.org/doc/debian-policy/ch-relationships.html
      :recommends "xz-utils"

      ;; supply the Debian "suggests" field
      ;; https://www.debian.org/doc/debian-policy/ch-relationships.html
      :suggests "package-deb"
    }
}
```

package-deb outputs a `.deb` in `bin`.  You can host this in a Debian repository for use with `apt`, or install directly via `dpkg`.