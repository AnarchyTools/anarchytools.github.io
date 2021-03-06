---
layout: documentation
tags: docs
---

# The AnarchyTools Package Manager

The `atpm` executable uses git to manage external dependencies to packages.

## Configuring a external dependency to your project

External dependencies are defined in your `build.atpkg` file. The syntax is rather simple:

```clojure
:external-packages [
  {
    :url "https://github.com/AnarchyTools/dummyPackageB.git"
    :version [ ">=1.0" "<=2.0" ]
  }
]
```

You just have to configure the package URL to use and some kind of version specifier.

## Result

External packages are placed into their own directory below the `external` dir on the same level as the `build.atpkg`. If a dependency has sub-dependencies the `external` directory of the sub-dependency is symlinked to the toplevel `external` dir so the complete dependency tree is flattened into one level. This results in a structure like this (provided `build.atpkg` has dependency `dep1` and `dep1` has `dep2`):

```
pkg root
  +- external
     +- dep1
        -> external -> symlinked to ../external
        +- src
           - dep1.swift
        - build.atpkg
     +- dep2
        +- src
           - dep2.swift
        - build.atpkg
  +- src
     - main.swift
  - build.atpkg
  - build.atlock
```

The *package name* is the last part of the git repo url (without the `.git` suffix if there is one).

You have multiple methods specifying a version:

- `:version` the prefered method of defining a version. Use this to allow the package manager to resolve conflicts for you.
- `:branch` defines a branch to check out (git packages only)
- `:tag` defines a git tag to check out (git packages only)
- `:commit` defines a commit id to check out (git packages only).

If all packages are fetched they behave like [imported packages](import.md) and can be referenced like them.

## Binary packages

Binary packages are also supported.  To use binaries, point the dependency URL to a **manifest**, like so:

```clojure
:external-packages [
  {
    :url "https://raw.githubusercontent.com/AnarchyTools/dummyBinaryPackage/master/manifest.atpkg"
    :version [ ">=0.1" "<=2.0" ]
    :channels ["osx"]
  }
]
```

`channels` here lists the binary feeds to which you want to subscribe.  Each dependency may offer various channels such as `stable`, `beta`, `osx`, `linux`, etc.  Consult your dependency documentation for details on what channels are available for the package.

Ensure the manifest specifies binary feeds:

```clojure
(package
:name "Example"

:binaries {
    :channels {
        :osx {
            :0.2 {
                :url "https://github.com/AnarchyTools/dummyBinaryPackage/releases/download/0.2/osx.tar.xz"
            }
            :0.1 {
                :url "https://github.com/AnarchyTools/dummyBinaryPackage/releases/download/0.1/osx.tar.xz"
            }
        }
    }
}
)
```

`atpm` will now download and unpack these tarballs to the `external` directory as appropriate as part of its normal `fetch` and `update` operations.

###  The `version` specification

To specify a version you use the `:version` parameter. This parameter accepts an array of version specification strings. You can use the following operators to specify a version:

- `==` or no operator: Only accept this version
- `>` and `>=`: use a version greater or greater equal than the number given
- `<` and `<=`: use a version smaller or smaller equal than the number given

For example to define a range of possible versions between `1.0` (inclusive) and `2.0` (exclusive) use `:version [">=1.0" "<2.0"]`.

Versions are just git tags. You may use simple version tags or tags prefixed with a `v` (like `v1.0.1`, case insensitive). The package manager does not prefer any kind of versioning scheme (except: the format has to be some kind of `x.y.z<suffix>`) but it is advisable to use [semantic versioning](http://semver.org/) to make it easier for your package users to define which versions to accept.

**Attention:** The package manager can resolve the exact version to pick when all packages that use a dependency use the `version` specification. So please use this if you don't have a important use case for another one. If even one package, that defines the same dependency as another one, and different versioning methods are specified the package manager will exit and force the user of the top-level package to [pin a defined commit](atlock.md) to be able to build.

### The `branch` specification

If you don't have a released version for a package yet or want to track a bleeding edge branch of a package (beware of breakage!) you can use the `:branch` parameter which tracks the specified branch of the git repository.

If you want to have a stable build it is advised to pin a commit in the [lock file](atlock.md) to be able to pick up work exactly where it worked the last time. 

### The `tag` specification

If a package uses another versioning scheme than recognized by the `:version` parameter you may specify a tag to checkout here. If the tag could be interpreted as a version number don't use this.

### The `commit` specification

If you're really desperate you may even specify a SHA commit id to check out, you should probably not use this, better use another method (as seen above) and [pin a defined commit](atlock.md) in the lock file.

### The `if-including` specification

If a dependency is not required for ordinary build/use of the library (but may be required for testing or certain targets) you can specify an `if-including` directive which will only download the package if the directive is provided on the command line.

 ```clojure
:name "packageName"
:external-packages [
  {
    :url "https://github.com/AnarchyTools/dummyPackageB.git"
    :version [ ">=1.0" "<=2.0" ]
    :if-including ["my-special-string"]
  }
]
```

Now the package will only be fetched if `--include packageName.my-special-string` is passed on the command line.  Including any of the specified strings will cause the package to be fetched.

When passed on the CLI, include strings are namespaced to the package (e.g. `PackageName.string`).  This is so multiple packages can use a string like `test` to specify test dependencies.

## Command line interface

`atpm` is command based, these are the currently recognized commands:

- `info`: Displays package dependency info, may display incomplete information if not all packages are fetched
- `fetch`: Fetch all needed dependencies recursively, does not modify already cloned git repos
- `update`: Updates all git repos that have been fetched to the current version specification
- `pin <packagename>`: Pin the named package to the currently checked out commit id and record that in the [lock file](atlock.md), `update` will not modify that package anymore
- `unpin <packagename>`: Remove the pinned commit id from the lock file, `update` will now be able to modify that package again
- `override <packagename> <overriden url>`: Override the git URL of a package. Future `fetch` actions will use the overridden URL instead of the original URL.
- `restore <packagename>`: restore fetching from the original URL. You'll have to delete the git repo in `external` if you already fetched a copy