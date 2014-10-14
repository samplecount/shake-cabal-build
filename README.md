# shake-cabal-build

Build systems written in [Shake][] normally need to be compiled, see the [Shake manual][shake-manual] for more information. [shake](https://github.com/samplecount/shake-cabal-build/blob/master/shake) is a simple Haskell script that takes care of all the details. Using the [Cabal][] infrastructure, build scripts are compiled on the fly for execution and also be reused as library components.

The only prerequisite for running the script is installing the latest [Haskell platform](http://www.haskell.org/platform/).

[Copy](https://raw.githubusercontent.com/samplecount/shake-cabal-build/master/shake) or link the script to your source folder and write a file `shakefile.hs` containing Shake rule definitions. Create a file `shakefile.cabal` with the following contents:

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

`./shake .update` creates a [Cabal sandbox](http://www.haskell.org/cabal/users-guide/installing-packages.html#developing-with-sandboxes
), installs the dependencies and compiles your build system script.

`./shake .scrub` calls your build script's `clean` target and removes the Cabal sandbox and all build products.

`./shake .init` initializes the sandbox and configures your package. This might be necessary sometimes but usually you don't need that command.

Running `./shake` with any argument not starting with a `.` runs your build script with the arguments passed on the command line, usually Shake options and targets to build.

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
