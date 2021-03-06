---
layout: documentation
tags: docs
---

# XCTest

atbuild is unopinionated about how you test your code; you can launch any executable as an atbuild [task](tasks.md), and use that executable to run tests.

However, if you want to use XCTest, it is tricky to configure, particularly as the implementation is quite different between platforms.  To smooth this problem out, `atbuild` includes some additonal options.

```clojure
(package
  :name "xctestexample"

  :tasks {
  ;; Define a task to build our library
    :build-lib {
      :tool "atllbuild"
      :sources ["src/**.swift"]
      :output-type "static-library"
      :name "Foo"

      ;; Make sure we enable testing so that 
      ;; we can import with @testable
      :compile-options ["-enable-testing"]
    }

    ;; Define a task to build the tests.
    ;; This is sometimes called a "test target" in Xcode.
    ;; This is primarily an executable that links our library.
    :build-tests {
      :tool "atllbuild"
      :sources ["test/**.swift"]
      :output-type "executable"
      :name "footests"
      :dependencies ["build-lib"]
      :link-with ["Foo.a"]

      ;; atbuild will inject platform-specific
      ;; compile options to make XCTest work
      :xctestify true

      ;; XCTest on Linux is a bit stricter
      ;; than on OSX.  Tell atbuild
      ;; we want stricter checks for API compliance
      :xctest-strict true

      ;;publish footests to bin/
      :publish-product true
    }

    ;; A task to run our tests
    :run-tests {
      :dependencies ["build-tests"]

      ;; The xctestrun tool is a cross-platform XCTest runner
      :tool "xctestrun"

            ;; Tell xctestrun where we built our tests
            :test-executable "bin/footests"
        }
     }
)
```

Now you can run your tests on any platform with just `atbuild run-tests`.

For more information, see a [complete example](https://github.com/AnarchyTools/atbuild/tree/master/tests/fixtures/xcs)
