---
layout: documentation
tags: docs
---

# atllbuild

The `atllbuild` tool uses the [`swift-llbuild`](https://github.com/apple/swift-llbuild) project to compile a swift module.

## API

```clojure
:tasks {

  :build {
    :tool "atllbuild"

    ;;Name of the module to build
    :name "build"

    ;;Type of build.  "static-library", "dynamic-library" and "executable" are supported.
    :output-type "library" 

    ;;walk the src directory and recursively find all swift files
    :sources ["src/**.swift"]

    ;;if true, we publish the product to the bin/ directory.
    ;;We also publish the swiftmodule and the modulemap, if applicable.
    ;;The modulemap is named "[target].modulemap" for static library targets.
    :publish-product false

    ;;If true, we don't build, we only output llbuild.yaml  False is the default value.
    :bootstrap-only false
    ;;Path to emit llbuild.yaml
    :llbuildyaml "llbuild.yaml"

    ;;Provide an array of compile options.  NOTHING IS IMPOSSIBLE
    :compile-options []
    :link-options [] ;;link options too!

    :link-sdk true #Whether to link the platform SDK.  True is the default value.

    ;;A product from another atllbuild task to link with.
    ;;You should supply a filename here, like "yaml.a".
    ;;Dynamic libraries have no platform-independent extension, so the 
    ;;pseudoextension ".dynamic" may be used.
    ;;Note that this is for linking dependencies built by atllbuild; 
    ;;for other libraries, you should use UNSUPPORTED https://github.com/AnarchyTools/atbuild/issues/13
    ;;If the specified atllbuild product used `:module-map "synthesized"` then its
    ;;modulemap will be loaded automatically
    :link-with-product ["attools.a" "atpkg.a"]

    ;; A path to an atbin to link with.
    ;; You should supply an atbin path here.
    :link-with-atbin ["foo.atbin"]

    ;; Add a path in the $ATBUILD_USER_PATH as an include path
    :include-with-user [] 

    ;; Inject platform-specific options related to
    ;; XCTest targets
    :xctestify false

    ;; Inject platform-specific behavior related to
    ;; checking for API differences across XCTest
    ;; platforms
    :xctest-strict false

    ;; Use an umbrella (bridging) header
    ;; By default, this option is not set, and
    ;; your project does not use a bridging header
    ;; This feature is EXPERIMENTAL.
    ;; must be used with :module-map "synthesized"
    :umbrella-header "MyHeader.h"

    ;; Whether to create a module map for the module or not.
    ;; "none" is the default value.  "synthesized" means
    ;; we we will generate a module map for the target
    :module-map "none"
    
    :whole-module-optimization false

    ;;Whether to build a library suitable for use as a framework.
    ;;Only supported on OSX hosts and OSX/iOS targets
    :framework false

    ;; Whether to emit bitcode.
    ;; This is currently only supported on the xcode toolchain, see SR-1493 for details
    :bitcode false
    
    ;; Platform-specific compile/link options you almost certainly want.
    ;; Set this to false to disable these and then you can replace with your own flags in the atpkg
    :magic true
  }
}
        
```

## Conditional compilation

If you're packaging a project for atbuild and wish to conditionally compile your sources, you can use

```swift
#if ATBUILD
    //use this code only when packaged with atbuild
#endif
```

This is useful when packaging Xcode projects that resolve their imports through bridging headers, for example.

## Incremental compiles

By default, atllbuild tries an incremental compile.  If you would like a full compile, use `--clean` on the command line.

## Implementation

The `atllbuild` tool emits an `llbuild.yaml` file, which is [now officially documented here](https://github.com/apple/swift-llbuild/blob/master/docs/buildsystem.rst).  Essentially, this file contains the compile/link commands for building the swift module.

Finally, we call `swift-build-tool -f /path/to/llbuild.yaml`.

## Boostrapping

In addition to building, we can choose to (only) emit the llbuild.yaml file, and save it for compiling later.

This is how we bootstrap atbuild, by creating an llbuild.yaml on a working machine and then using swift-build-tool on a new machine.  For this reason, the `llbuild.yaml` file for `atbuild` (and `atbuild`'s own dependencies, like `atfoundation` or `atpkg`) must be kept up in a bootstrappable state.
