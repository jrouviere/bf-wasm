# bf-wasm

Transform a [brainfuck](https://en.wikipedia.org/wiki/Brainfuck) program into WebAssembly binary code.
This is pretty fast because the webassembly code is optimised and JIT'd into native code by the browser.

## Overview

That very simple brainfuck program:

```bf
>+
```

would be transpiled into this webassembly module:

```wat
(module
    (memory (import "js" "mem") 1)
    (import "js" "putc" (func $putc (param i32)))
    (func (export "main")
        (local $ptr i32)

        (local.set $ptr (i32.add (local.get $ptr) (i32.const 1))) ;; this is '>'
        (i32.store8 (local.get $ptr) (i32.add (i32.load8_u (local.get $ptr)) (i32.const 1))) ;; this is '+'
    )
)
```

except that we directly generate the program into a `Uint8Array` buffer and pass it to [WebAssembly.instantiate](https://developer.mozilla.org/en-US/docs/WebAssembly/JavaScript_interface/instantiate).

## Useful commands

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