---
layout: documentation
tags: docs
---

# The variable expansion engine

Some atbuild fields support variable expansion.  To see if your field does, check the tool documentation.

A varaible expansion looks like `${FOO}`.  To escape an expansion, use `\${FOO}`.

*Warning: we intend to deprecate unescaped `$` in a future release.*

# Supported expansions

* `${collect_sources:taskName}` This expands to the space-separated collected sources of the named task.  Paths are given relative to the directory containing the atpkg file.  This expansion may be used to run tasks that need to know all sources of a build task.