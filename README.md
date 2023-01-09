# bf-wasm

Compile a [brainfuck](https://en.wikipedia.org/wiki/Brainfuck) program into WebAssembly binary code.

The resulting webassembly code is very inefficient, but we rely on the browser to optimise it.
So it is actually running pretty fast because the webassembly code is optimised and compiled into native code by the browser.

As a reference, on my machine the mandelbrot.bf program runs in:

- 5.1s with Chrome 108
- 1.3s with Firefox 108
- 1.3s with Safari 16.1
- 0.9s when compiled directly with [bf-llvmlite](https://github.com/jrouviere/bf-llvmlite)
- 30+ min with a naive python interpreter

## Overview

That very simple brainfuck program:

```bf
>+
```

would be transpiled into this webassembly module:

```wat
;; static program preamble
(module
    (memory (import "js" "mem") 1)
    (import "js" "putc" (func $putc (param i32)))
    (func (export "main")
        (local $ptr i32)

        ;; this is for '>'
        (local.set $ptr (i32.add (local.get $ptr) (i32.const 1)))

        ;; this is for '+'
        (i32.store8 (local.get $ptr) (i32.add (i32.load8_u (local.get $ptr)) (i32.const 1)))
    )
)
```

The code directly generates the webassembly binary into a `Uint8Array` buffer, pass it to [WebAssembly.instantiate](https://developer.mozilla.org/en-US/docs/WebAssembly/JavaScript_interface/instantiate) and call the exported main function.

## Useful commands

The `wat2wasm` can be used to generate commented webassembly binary, which was very useful when implementing the transpiler:

```sh
# wasm text format to commented binary
wat2wasm test.wat -v

# wasm text format to hexdump
wat2wasm test.wat --output=- | hexdump -C
```

## References

- [wasm reference](https://webassembly.github.io/spec/core/syntax/instructions.html#syntax-instr-control)
- [MDN](https://developer.mozilla.org/en-US/docs/WebAssembly/Reference/Variables)
- [wasm binary toolkit](https://github.com/WebAssembly/wabt)
- [brainfuck online editor](https://copy.sh/brainfuck/)