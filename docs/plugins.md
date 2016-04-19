---
layout: documentation
tags: docs
---

# Plugins

The Anarchy Tools mission is to build "simple, separate" tools.  While we want atbuild to be a great build system out of the box, we also want to prevent it from becoming monolithic, and therefore, unhackable.  Many features that are very useful do not make sense for inclusion in atbuild core:

1.  Optional or lesser-used features
2.  Features that only make sense on a few platforms
3.  Features that don't require tight integration with the atbuild sourcetree

Examples of features like this include: [xcode-emit](https://github.com/AnarchyTools/xcode-emit).

To better support this usecase, we've developed an extremely simple plugin model.

# Programs

Plugins are just programs, that are written to be used from `atbuild`.
You can use them just like any builtin tool:

```clojure
(package
  :name "program"
  
  :tasks {
    :default {
      :tool "echo.plugin"
      :key "value"
  }
}

)
```

To use a plugin:

1.  Place a binary in your path
2.  Set your tool name to be `mybinaryname.plugin`
3.  Set keys and values as needed

The program will be started with `--key1 value1 --key2 value2`.  Note that build.atpkg is unordered, so the order of keys provided to the plugin is undefined.

# Comparison with shell

Our [shell tool](shell.html) can be used to run any program, not just ones that understand `--key value` syntax.

With plugins, you can write a program (or a wrapper for an existing program) that is specifically designed for Anarchy Tools.

# Variable expansion

[atpkg variable expansion](variable.html) is supported for all key values.

# Environment

We set `ATBUILD_USER_PATH` environment variable to be a path to the user path.  See the [shell tool](shell.html) for more information.

# Tips

1.  Consider statically linking your program with the swift static library.  Upstream [does not support this](https://bugs.swift.org/browse/SR-730) but you can do it with [CaffeinatedSwift](https://code.sealedabstract.com/CaffeinatedSwift/swift)
2.  If you need to work with atpkg files for some reason, you can link our [atpkg library](https://github.com/AnarchyTools/atpkg)