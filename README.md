[![PureScript](https://raw.githubusercontent.com/purescript/purescript/master/logo.png)](http://purescript.org)

**Pure11** is an experimental C++11 compiler/backend for [PureScript](https://github.com/purescript/purescript). It attempts to generate "sane" and performant C++11 code (instead of JavaScript), in the spirit of PureScript.

---

### **!!! All code is in the [pure11 github organization](https://github.com/pure11) !!!**

---

#### Performance

* No runtime system (beyond some support classes and the standard C++11 runtime library)
* Uses native C++11 reference counting (`std::shared_ptr`) for relatively lightweight automatic memory management
* Uses PureScript's normal tail call optimizations for generated C++11 code

#### Differences from PureScript:

* Foreign imports are C++11 instead of JavaScript
* Compiler is `pcc` instead of `psc`
  - Generates a simple CMake file for easy experimentation
* No Pure11-specific REPL

#### Other notes:

* PureScript arrays are represented as `std::vector`
* `String` type corresponds to `std::string`
* `Number` is C++ `double`, `Int` is C++ `long`, `Char` is `char`, `Boolean` is `bool`
* [Standard packages currently building (lightly tested)](https://github.com/andyarvanitis/pure11/wiki/Packages)

#### TO-DO:

* Get automated builds/tests fully up and running [(some work already done)](https://github.com/pure11/purescript/blob/pure11/pcc/TestMain.hs)
* Unicode (UTF-8) support for `String` (possibly use code from my Idris backend)
* Lots of testing!

#### Future ideas:

* Unboxed math on a per-function basis
* Nice facilities (modules) for concurrency/parallelism, using `std::thread`, etc. under the hood
* Compiler or lib options for other types of memory management — e.g. the Boehm (or other) GC
* `BigInt` via GNU GMP
* Stricter exports in C++ code

#### Requirements

* Everything you need to build [PureScript](https://github.com/purescript/purescript)
* A C++11-capable toolchain, e.g. recent versions of clang, gcc
* Installed CMake is helpful (for the provided quickstart CMake file generated), though not required. You should be able to use your favorite C++ build system, tools, debuggers, etc., for the generated code.

#### Examples

**`fib.purs`**
```PureScript
module Main where
  import Prelude
  import Control.Monad.Eff.Console

  fib :: Int -> Int
  fib 0 = 0
  fib 1 = 1
  fib n = fib (n - 2) + fib (n - 1)

  main = do
    log "Here's the result of fib 10:"
    print (fib 10)
```
**`fib.cc`**
```c++
#include "Main/Main.hh"
namespace Main {
    using namespace PureScript;
    using namespace Prelude;
    using namespace Control_Monad_Eff_Console;
    using namespace Control_Monad_Eff;

    auto fib(const any& _0) -> any {
        if (_0 == 0L) {
            return 0L;
        };
        if (_0 == 1L) {
            return 1L;
        };
        return fib(_0 - 2L) + fib(_0 - 1L);
    };
    const any main = [](any::as_thunk) -> const any& {
        static const any _value_ = [=]() -> any {
            Control_Monad_Eff_Console::log("Here's the result of fib 10:")();
            return Control_Monad_Eff_Console::print(Prelude::showInt)(fib(10L))();
        };
        return _value_;
    };
};

auto main(int, char *[]) -> int {
    using namespace Main;
    Main::main(any::unthunk)();
    return 0L;
};
```
---
#### Getting Started
This assumes you are running OS X or a Unix-like system (Linux, *BSD, etc.), and already have the ability to [build PureScript] (http://www.purescript.org/download/). Instead of cloning the repository in the instructions, make sure to use [this project's repo](https://github.com/pure11/purescript).

1. Make sure you have developer tools for your system installed. For OS X, you'll need a recent version of Xcode. For Linux, etc., you need gcc 4.9.2 or later, including g++ support. You can also use clang 3.5 or later, but it still requires gcc for its C++ standard libraries.

2. Install [CMake] (https://cmake.org/install/). Afterwards, make sure `cmake` is in your `PATH`.

3. Create a working directory wherever you like. From the purescript source tree, copy `pcc/Makefile.example` to it, renaming it to `Makefile` (no extension).

4. Create a `src` directory in this working directory, which will be where you will place your own PureScript source files.

5. Edit your `Makefile` to your liking if you want it to use different locations for the `pcc` executable or PureScript packages.

6. Pull in your desired PureScript packages using `git` (bower will be supported later), making sure to use the pure11-specific versions in [this list](https://github.com/andyarvanitis/pure11/wiki/Packages).

6. You should now be ready to build a PureScript program. As stated above, place your source file(s) in the working directory's `src` subdirectory and execute `make`. This will generate the C++ source tree for your program, located in the `output` subdirectory it created. 

7. To build the C++ code, make a `build` subdirectory in your working directory and `cd` to it. From there, execute `cmake ../output; make`. If your machine has multiple cores, you might want to use `make -jN`, where `N` is equal to the number of cores.

8. If the build was successful, run your program using `./Main`.

---
