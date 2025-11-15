---
name: cpython-build
description: Use this skill when you need to configure, build, or rebuild CPython from source. It covers initial setup with configure, incremental builds, platform-specific considerations, and working with Argument Clinic generated code.
---

# Building CPython

**ONLY build in a `build/` subdirectory** at repo root. Never build in the source tree.

## Setup and Configuration

**IMPORTANT**: Use sub-agents when running configure and make build steps!

```bash
# Build directory setup
REPO_ROOT=<path-to-cpython-git-repo>
BUILD_DIR=$REPO_ROOT/build

# Configure with debug support (enables debug symbols, assertions, runtime checks)
cd $BUILD_DIR && ../configure --with-pydebug

# Build using all CPU cores (initial or incremental)
make -C $BUILD_DIR -j $(nproc)
```

**Platform notes**:
- Linux: `BUILT_PY=$BUILD_DIR/python`
- macOS: `BUILT_PY=$BUILD_DIR/python.exe` (note .exe extension)
- Windows: Ask user how to build (uses Visual Studio, different process)

## Argument Clinic

After editing `.c` files that change function signatures, docstrings, or argument specs:
```bash
make -C $BUILD_DIR clinic
```

**NEVER** edit files in `**/clinic/**` subdirectories - they're auto-generated.

## Verify Build

```bash
$BUILT_PY --version
$BUILT_PY -c "print('Hello from CPython!')"
```

## Troubleshooting

- **Missing dependencies**: Configure reports missing libraries
- **Stale build**: `make clean` in BUILD_DIR and rebuild
- **Clinic files out of sync**: `make -C $BUILD_DIR clinic`
- **Clean build**: `rm -rf $BUILD_DIR && mkdir $BUILD_DIR && cd $BUILD_DIR && ../configure --with-pydebug && make -C $BUILD_DIR -j $(nproc)`
