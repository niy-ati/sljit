# sljit

[sljit](https://github.com/zherczeg/sljit), the stack-less JIT compiler, imported directly into D via [ImportC](https://dlang.org/spec/importc.html). No hand-written bindings, the real C source is compiled and used as-is.

```d
import sljitLir;

void main()
{
    auto compiler = sljit_create_compiler(null);
    scope(exit) sljit_free_compiler(compiler);

    // build and run JIT code using sljit's normal C API, called from D
}
```

## Platform support

Windows only for now. sljit's own CPU feature detection uses GNU inline assembly on Linux (`sljit_create_compiler` calls `get_cpu_features`, which contains an `asm volatile` block), and ImportC turns unsupported inline asm into a runtime assert rather than executing it. Windows uses MSVC intrinsics instead (`__cpuid`/`_xgetbv`) and works end to end.

## Why this works as a zero-wrapper package

ImportC compiles `sljit_src/sljitLir.c` directly, so this package is always as current and complete as sljit's own C API. There is nothing to keep in sync by hand, if sljit adds a function, importing it from D needs no new binding, just the function name.

## Source

Vendors sljit at [zherczeg/sljit@39c508d](https://github.com/zherczeg/sljit/commit/39c508d1). License is sljit's own 2-clause BSD, see LICENSE.

## Example

See `examples/add` for a minimal JIT-compiled `add(int, int) -> int` built and called from D.

Part of [SAOC 2026](https://symmetryinvestments.com/saoc), ImportC: Zero-Wrapper Integration for Real-World C Libraries.
