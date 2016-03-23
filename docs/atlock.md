---
layout: documentation
tags: docs
---

# The AnarchyTools Package Manager Lockfile

The purpose of the lock file is to record the last working state of the external dependencies. You may ignore the lock file if you want to, but it is advised to check it into the git repository to be able to reconstruct the exact same state where the last build worked.

You should never have to modify the lock file by hand because the `atpm` command line tool can do everything you may need to do.

The minimal structure of the lock file is defined like the following:

```clojure 
(lock-file
  :packages [
    {
      :url "https://github.com/AnarchyTools/dummyPackageB.git"
      :used-commit "005d5710eaa1c424264f905e9a2ac6497a202de3"
    }
  ]
)
```

The lock file is always named `build.atlock` and is kept in the same dir as the `build.atpkg`.

## Pinning commits

To pin a dependency to an exact commit id add the following line to the corresponding package definition:

```clojure
:pin-commit "<commit id>"
```

This will disable version resolution of the package manager and instead check out this exact commit id on the next update.

You may use `atpm pin <packagename>` to let the package manager add this line for you (it uses the currently checked out commit), or remove that line to unpin the version by issuing a `atpm unpin <packagename>` command


## Overriding git repository URLs

If you are afraid of a dependency vanishing or develop a dependency in parallel with your package you may override the git repo URL by adding:

```clojure
:override-url "<new url>"
```

This will force the package manager to look at `<new url>` instead of the original git repo url.

You may use the `atpm` commandline to add and remove that line for you:

- `atpm override <packagename> <new url>` will add an override
- `atpm override <packagename>` will remove an override
