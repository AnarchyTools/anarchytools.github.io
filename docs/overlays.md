---
layout: documentation
tags: docs
---

# Overlays

You can activate settings, called "overlays", by passing them on the command line.

```bash
$ atbuild --use-overlay bar
```

This activates the settings for the overlay across all targets:

```clojure
(package
  :name "Foo"
  :overlays {
    :bar {
        :compile-options ["-D" "FOO"]
    }
  }
)
```

You can add as many overlays as you like: `atbuild --use-overlay bar --use-overlay baz`.  They are processed in order.

# Task-scoped overlays

You can also only apply an overlay to a particular task.  To do this:

```clojure
(package
  :name "Foo"
  :tasks {
    :foo {
      :overlays {
        :bar {
          :compile-options ["-D" "BAZ"]
        }
      }
    }
  }
)
```

Now when we build with `--use-overlay bar`, this will add `-D BAZ` to compile options for the `foo` task, but not for other tasks in our file.

# 'Always' overlay

We can use an overlay even when it was not specified on the CLI.  This way we can share common configuration options between several tasks.

```clojure
(package
  :name "Foo"
  :overlays {
    :optimized {
      :compile-options ["-Owholemodule"] ;;whole module optimization
    }
  }
  :tasks {
    :foo {
      :use-overlays ["optimized"] ;;apply to this task
    }
    :baz {
      :use-overlays ["optimized"] ;;apply to this task too
    }
  }
)
```

# Imported overlays

Overlays can be [imported](import.md).  This allows libraries to export required compile flags to clients.

```clojure
(package
   :name "Library"
   :overlays {
    :compile-linux {
      :compile-options ["-Xcc" "-fblocks"] ;; work around https://bugs.swift.org/browse/SR-397
    }
   }
)
```

```clojure
(package
   :name "Executable"
   :import ["Library.atpkg"]
   :tasks {
    :foo {
      :use-overlays ["Library.compile-linux"] ;;apply to this task
    }
)
```

# Required overlays
 
You can specify that an overlay must be applied to a task.

```clojure
(package
   :tasks {
    :foo {
        :required-overlays [["osx" "linux"] ["debug" "release"]] ;;at least one of osx/linux and one of debug/release must be applied
    }
)
```

# Built-in overlays

The following overlays are enabled automatically by atbuild.

| overlay                | enabled?           |
|------------------------|--------------------|
| atbuild.platform.osx   | Building for OSX   |
| atbuild.platform.mac   | Building for OSX   |
| atbuild.platform.linux | Building for Linux |


