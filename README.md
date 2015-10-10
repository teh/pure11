[![PureScript](https://raw.githubusercontent.com/purescript/purescript/master/logo.png)](http://purescript.org)

A small strongly typed programming language with expressive types that compiles to Javascript, written in and inspired by Haskell.

---

**Pure11** is an experimental C++11 compiler/backend for [PureScript](https://github.com/purescript/purescript). It attempts to generate "sane" and performant C++11 code (instead of JavaScript), in the spirit of PureScript.

---

## **All code is located in the [pure11 github organization](https://github.com/pure11)**

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
