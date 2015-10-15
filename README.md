# shake-cabal-build

[![Build Status](https://travis-ci.org/samplecount/shake-cabal-build.svg?branch=develop)](https://travis-ci.org/samplecount/shake-cabal-build)

**NOTE**: This package is deprecated and unmaintained. These days we use [stack](https://github.com/commercialhaskell/stack) in our build script wrappers. Something like:

    #!/bin/sh -e
    stack build
    stack exec -- shakefile-executable "$@"

Build systems written in [Shake][] normally need to be compiled, see the [Shake manual][shake-manual] for more information. [shake](https://github.com/samplecount/shake-cabal-build/blob/master/shake) is a simple Haskell script that takes care of all the details. Using the [Cabal][] infrastructure, build scripts are compiled on the fly for execution and also be reused as library components.

The only prerequisite for running the script is the latest [Haskell platform](http://www.haskell.org/platform/).

## Installation and usage

[Copy](https://raw.githubusercontent.com/samplecount/shake-cabal-build/master/shake) or link the script to your source folder. You can also install `shake-cabal-build` from [Hackage](http://hackage.haskell.org/package/shake-cabal-build) with `cabal install`. The executable is called `shake-cabal-build` in order to avoid a clash with a different executable installed by [Shake][], you can create an alias with a different name if you want.

Write a file `shakefile.hs` containing Shake rule definitions and create a file `shakefile.cabal` with the following contents:

    Name: hearhearme-shakefile
    Version: 0.1.0
    Cabal-Version: >= 1.2
    Build-Type: Simple

    Executable hearhearme-shakefile
      Main-Is: shakefile.hs
      Ghc-Options: -rtsopts -with-rtsopts=-I0
      Build-Depends:
          base == 4.*
        , shake

If you're using [shake-language-c](https://github.com/samplecount/shake-language-c), add it to the dependencies as well. Refer to the [Cabal manual][cabal] for more information about configuration file features. `cabal init` is another way of creating the Cabal file. It can automatically pick up the dependencies referenced from package imports in your `shakefile.hs` (if it exists already).

The `shake` script recognises a number of commands for updating your build system, all of them starting with a `.` character:

  * `.update` creates a [Cabal sandbox](http://www.haskell.org/cabal/users-guide/installing-packages.html#developing-with-sandboxes
) and installs your build system script along with the dependencies by executing `cabal install` with profiling and documentation disabled in order to minimise build times. If needed this behaviour could be made configurable in the future.

  * `.scrub` calls your build script's `clean` target and removes the Cabal sandbox and all build products.

  * `.repl` loads your build script into `ghci` by calling `cabal repl`. This is useful while developing the build script because it is the fastest way to type-check and compile your code. From within `ghci` you can call

        > :main arg1 arg2 arg3 ...

    in order to execute your build script, just as from the command line. Executing

        > :reload

    will recompile and reload your code and is very fast compared to `cabal install`.

Running `./shake` with an argument not starting with a `.` runs your build script with the arguments passed on the command line, usually Shake options and targets to build.

## Cabal file extensions

The `shake` script supports the custom Cabal configuration setting `x-shake-package-dirs`. It allows to specify Cabal source packages that should be added to the sandbox with `cabal add-source`, for example:

    X-Shake-Package-Dirs:
      external_libraries/methcla/external_libraries/shake
      external_libraries/methcla/external_libraries/shake-language-c
      external_libraries/methcla

Add this setting before the `Executable` section containing your build script. See [here](https://github.com/samplecount/methcla/blob/develop/shakefile.cabal) for an example of a production configuration file that also exports a library.

[cabal]: http://www.haskell.org/cabal/users-guide/
[shake]: https://github.com/ndmitchell/shake
[shake-manual]: https://github.com/ndmitchell/shake/blob/master/docs/Manual.md
