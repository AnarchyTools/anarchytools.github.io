---
layout: documentation
tags: docs
---

# atbin

atbin is a universal binary packaging format used by the AT toolchain.

# Comparison with other formats

Swift binaries are composed of several components: an binary (such as `.a` or executable), a `swiftdoc`, a `swiftmodule`, and sometimes a clang module.

On Darwin platforms these components are often distributed inside a Framework.  But Frameworks are awkward on Linux, and do not support some AT binary types, like executables.

# Specification

An `atbin` is a folder with extension `.atbin` containing at least a `compiled.atpkg` file.

`example.atbin/compiled.atpkg`:

```clojure
(package
:name "example"
:payload "example.dylib"
:platforms ["ios-x86_64" "ios-i386"]
:type "dynamic-library"
)
```

This `.atbin` will contain:

* `example.dylib`, a fat dynamic library for the 2 platforms indicated
* (optional) `ios-x86_64.swiftmodule` and `ios-i386.swiftmodule`, if the module is a library
* (optional) `ios-x86_64.swiftdoc` and `ios-i386.swiftdoc`, if the module is a library and documentation is available
* (optional) `module.modulemap`, if the module is a clang module, or overlays one.

# Building

You can build `.atbin`s by hand; just move the files into the right places and create your `compiled.atpkg`.

You can also build `atbin` from [`packageatbin`](packageatbin.html)

