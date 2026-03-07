---
name: jit
description: Use this skill when working on CPython's experimental JIT compiler - modifying Tools/jit/ build scripts, editing Python/jit.c runtime code, changing bytecode that affects JIT stencils, debugging JIT failures, or working with the copy-and-patch compilation pipeline. Covers LLVM toolchain setup, stencil generation, JIT configure flags, and the template.c to jit_stencils.h build flow.
---

# CPython JIT Compiler Development

The JIT uses **copy-and-patch compilation**: at build time, each micro-op (uop) is compiled to a native code "stencil" with holes for runtime values. At runtime, `_PyJIT_Compile()` copies stencils and patches in actual addresses/operands.

**Architecture reference**: `InternalDocs/jit.md`

## Prerequisites

**Load the `build` skill first** if you haven't configured and built CPython yet.

LLVM 21 is required to build the JIT (only `clang` and `llvm-readobj`; `llvm-objdump` is recommended). Check availability:

```bash
clang-21 --version 2>/dev/null || clang --version 2>/dev/null
```

If not installed:
- Ubuntu/Debian: `wget https://apt.llvm.org/llvm.sh && chmod +x llvm.sh && sudo ./llvm.sh 21`
- Fedora 40+: `sudo dnf install 'clang(major) = 21' 'llvm(major) = 21'`
- macOS (Homebrew): `brew install llvm@21`
- Dev Containers / Codespaces: LLVM 21 is pre-installed
- Windows: Downloaded automatically by `PCbuild\build.bat`

## Building with JIT

```bash
# Full JIT (native code compilation)
cd $BUILD_DIR && CC="ccache gcc" ../configure --enable-experimental-jit --with-pydebug
make -j $(nproc)

# Tier 2 interpreter only (no native compilation, useful for debugging uop logic)
cd $BUILD_DIR && CC="ccache gcc" ../configure --enable-experimental-jit=interpreter --with-pydebug
make -j $(nproc)
```

### Configure options for `--enable-experimental-jit`

| Value | Effect |
|-------|--------|
| `yes` (default if flag given) | Full JIT + Tier 2 interpreter |
| `yes-off` | Full JIT compiled in but disabled at runtime by default |
| `interpreter` | Tier 2 interpreter only (no native code) |
| `interpreter-off` | Tier 2 interpreter compiled in but disabled by default |
| `no` | No JIT (default if flag omitted) |

Runtime toggle: set `PYTHON_JIT=0` or `PYTHON_JIT=1` to override the build default.

## Key Files

### Build-time stencil generation (`Tools/jit/`)

| File | Purpose |
|------|---------|
| `build.py` | Entry point - invokes stencil generation for target triples |
| `template.c` | C template; each uop's case from `executor_cases.c.h` is injected at `CASE` |
| `shim.c` | Entry shim for `preserve_none` calling convention conversion |
| `jit.h` | Macros: `PATCH_VALUE`, `DECLARE_TARGET`, `jit_func_preserve_none` typedef |
| `_targets.py` | Platform-specific compilation: `_ELF`, `_MachO`, `_COFF` target classes |
| `_stencils.py` | Data structures: `Hole`, `Stencil`, `StencilGroup`, `HoleValue` enum |
| `_optimizers.py` | Post-compilation assembly optimization (branch inversion, dead code removal) |
| `_writer.py` | Generates C `emit_*` functions and `StencilGroup` table for `jit_stencils.h` |
| `_llvm.py` | LLVM tool discovery and invocation (`_LLVM_VERSION = "21"`) |
| `_schema.py` | TypedDict schemas for `llvm-readobj` JSON output |

### Runtime (`Python/`)

| File | Purpose |
|------|---------|
| `Python/jit.c` | `_PyJIT_Compile()`, memory allocation, stencil patching, `_PyJIT_Free()` |
| `Python/optimizer.c` | Uop optimizer, trace construction, executor management |
| `Python/optimizer_analysis.c` | Uop analysis and optimization passes (type inference, constant folding) |
| `Python/executor_cases.c.h` | **Generated** - uop switch cases (from `Python/bytecodes.c`) |
| `Python/ceval.c` | `tier2_dispatch:` loop for the Tier 2 interpreter |
| `Python/perf_jit_trampoline.c` | Linux `perf` integration for JIT code profiling |

### Headers

| File | Purpose |
|------|---------|
| `Include/internal/pycore_jit.h` | `jit_func` typedef, `_PyJIT_Compile` signature, `PY_MAX_JIT_CODE_SIZE` |
| `Include/internal/pycore_optimizer.h` | `_PyExecutorObject` (has `jit_code` field), `_PyUOpInstruction` |

### Generated at build time

| File | Purpose |
|------|---------|
| `jit_stencils.h` (in build dir) | All `emit_*` functions and `stencil_groups[]` table |

## Build Pipeline

```
Python/bytecodes.c
    |  (Tools/cases_generator/tier2_generator.py)
    v
Python/executor_cases.c.h
    |  (Tools/jit/build.py reads each "case OPNAME:" block)
    v
For each uop: template.c + case → {OPNAME}.c
    |  (clang --target={triple} -Os -S)
    v
{OPNAME}.s
    |  (_optimizers.py: branch inversion, dead code removal)
    v
{OPNAME}.s (optimized)
    |  (clang -c)
    v
{OPNAME}.o
    |  (llvm-readobj → JSON → _stencils.py parses relocations)
    v
StencilGroup (code bytes + holes)
    |  (_writer.py)
    v
jit_stencils.h (emit_* functions + StencilGroup table)
```

## Regenerating JIT Stencils

**You must regenerate stencils after**:
- Editing `Python/bytecodes.c` (uop definitions)
- Modifying any file in `Tools/jit/`
- Changing `pyconfig.h` (configure options)

```bash
# Regenerate stencils
make -C $BUILD_DIR regen-jit

# Force full rebuild (ignores digest cache)
python3 Tools/jit/build.py --force -d -o $BUILD_DIR -p $BUILD_DIR <target-triple>

# Clean stencils (forces regeneration on next build)
make -C $BUILD_DIR clean-jit-stencils

# Then rebuild
make -C $BUILD_DIR -j $(nproc)
```

The stencil build uses a SHA-256 digest of all inputs. If the digest hasn't changed, `make` skips regeneration. Use `--force` or `clean-jit-stencils` to override.

## Testing

The optimizer and executor infrastructure is tested in `Lib/test/test_optimizer.py`. Otherwise, the JIT is tested by running the standard test suite with JIT enabled:

```bash
# Standard test run (JIT is active if configured with --enable-experimental-jit)
$BUILT_PY -m test -j $(nproc)

# Run specific tests
$BUILT_PY -m test test_capi test_sys -j $(nproc)

# Disable uop optimization (test stencils with unoptimized traces)
PYTHON_UOPS_OPTIMIZE=0 $BUILT_PY -m test -j $(nproc)

# Disable JIT at runtime (fall back to Tier 2 interpreter)
PYTHON_JIT=0 $BUILT_PY -m test -j $(nproc)

# Enable JIT at runtime (for yes-off builds)
PYTHON_JIT=1 $BUILT_PY -m test -j $(nproc)
```

## Debugging JIT Issues

### Common problems

- **Segfault in JIT code**: Usually a patching bug or stale stencils. Run `make clean-jit-stencils && make -j $(nproc)` and retest.
- **"Can't find clang-21!"**: LLVM 21 not installed or not on PATH. See Prerequisites.
- **Stencil digest mismatch**: After editing bytecodes, run `make regen-jit` before rebuilding.
- **Wrong results with JIT**: Compare `PYTHON_JIT=1` vs `PYTHON_JIT=0` to isolate. If only JIT fails, the bug is in stencil generation or patching.

### Inspecting generated stencils

```bash
# View the generated stencils header (contains disassembly in comments)
less $BUILD_DIR/jit_stencils.h

# Build with verbose output to see clang invocations
python3 Tools/jit/build.py -v -d -o $BUILD_DIR -p $BUILD_DIR x86_64-unknown-linux-gnu
```

### Debugging at the C level

Build with `--with-pydebug` for assertions in `Python/jit.c`. Key functions to set breakpoints on:
- `_PyJIT_Compile` - trace compilation entry point
- `_PyJIT_Free` - executor cleanup
- `_PyOptimizer_Optimize` - trace construction (in `Python/optimizer.c`)

### Environment variables for debugging

| Variable | Values | Effect |
|----------|--------|--------|
| `PYTHON_JIT` | `0` / `1` | Disable/enable JIT at runtime |
| `PYTHON_UOPS_OPTIMIZE` | `0` | Disable uop optimization (test unoptimized traces) |
| `PYTHON_OPT_DEBUG` | `0`-`5` | Optimizer debug tracing verbosity |

## Supported Platforms

| Target Triple | Object Format | Status |
|---------------|--------------|--------|
| `x86_64-unknown-linux-gnu` | ELF | Stable |
| `x86_64-apple-darwin` | Mach-O | Stable |
| `aarch64-unknown-linux-gnu` | ELF | Stable |
| `aarch64-apple-darwin` | Mach-O | Stable |
| `x86_64-pc-windows-msvc` | COFF | Experimental |
| `aarch64-pc-windows-msvc` | COFF | Experimental |
| `i686-pc-windows-msvc` | COFF | Experimental |

## Key Concepts

- **Stencil**: Pre-compiled native code for one uop, with "holes" for runtime values (operands, jump targets, symbol addresses).
- **Hole**: A relocation record (offset + type + patch function) filled at JIT compile time.
- **`preserve_none` calling convention**: Used for tail calls between JIT stencils. Only Clang supports this (`__attribute__((preserve_none))`), which is why LLVM/Clang is required.
- **`musttail`**: Guaranteed tail call elimination, essential for the continuation-passing style between stencils.
- **GOT (Global Offset Table)**: Per-stencil table for resolving external symbols at runtime.
- **Executor**: `_PyExecutorObject` - holds the uop trace and (if JIT-compiled) a `jit_code` function pointer.
- **`PY_MAX_JIT_CODE_SIZE`**: 1 MB - 1 byte. Traces exceeding this fall back to the Tier 2 interpreter.
