---
name: cpython-build
description: Use this skill when you need to configure, build, or rebuild CPython from source. It covers initial setup with configure, incremental builds, platform-specific considerations, and working with Argument Clinic generated code.
---

# Building CPython

## Purpose

This skill provides comprehensive guidance for building CPython from source, including initial configuration, incremental builds, and platform-specific considerations.

## Critical Build Rule

**ONLY build in a `build/` subdirectory** that you create at the repo root.

Never build in the source tree itself - always use an out-of-tree build directory.

## Build Directory Setup

Define these variables for your build process:
```bash
REPO_ROOT=<path-to-cpython-git-repo>
BUILD_DIR=$REPO_ROOT/build
```

## Initial Configuration

### For Development (with Debug Support)

**IMPORTANT**: Use sub-agents when running configure and make build steps!

```bash
cd $BUILD_DIR
../configure --with-pydebug
```

This enables:
- Debug symbols
- Assertions
- Additional runtime checks
- Memory debugging

### Platform-Specific Considerations

#### Linux
```bash
# Standard debug build
cd $BUILD_DIR && ../configure --with-pydebug
```

#### macOS
```bash
# Standard debug build
cd $BUILD_DIR && ../configure --with-pydebug

# Built executable will be: BUILD_DIR/python.exe (note the .exe extension on macOS)
```

#### Windows
**Ask the user how to build on Windows** - the build process is significantly different and uses Visual Studio.

## Building

### Initial Build
```bash
# Use all available CPU cores
make -C $BUILD_DIR -j $(nproc)
```

### Incremental Rebuild
After making changes to source files:
```bash
make -C $BUILD_DIR -j $(nproc)
```

This will only rebuild changed files and their dependencies.

## Locating the Built Executable

### Linux
```bash
BUILT_PY=$BUILD_DIR/python
```

### macOS
```bash
BUILT_PY=$BUILD_DIR/python.exe
```

### Windows
Ask the user for the path - it depends on Visual Studio configuration.

## Argument Clinic Code Generation

### What is Argument Clinic?
Argument Clinic generates C boilerplate for parsing function arguments in C extension modules.

### When to Regenerate
After editing a `.c` file in a way that changes:
- C extension module function signatures
- Function docstrings
- Argument specifications

### How to Regenerate
```bash
make -C $BUILD_DIR clinic
```

### Important Notes
- **NEVER** edit files in `**/clinic/**` subdirectories directly
- Always edit the source `.c` file, then regenerate
- The generated code will be in a `clinic/` subdirectory

## Verifying Your Build

After building, verify the executable works:
```bash
$BUILT_PY --version
$BUILT_PY -c "print('Hello from CPython!')"
```

## Build Troubleshooting

### Common Issues

1. **Missing dependencies**: Configure will report missing libraries
2. **Stale build**: Try `make clean` in BUILD_DIR and rebuild
3. **Clinic files out of sync**: Run `make -C $BUILD_DIR clinic`

### Clean Build
If you need to start fresh:
```bash
rm -rf $BUILD_DIR
mkdir $BUILD_DIR
cd $BUILD_DIR && ../configure --with-pydebug
make -C $BUILD_DIR -j $(nproc)
```

## Integration with Other Skills

After building, you'll typically want to:
- **cpython-testing**: Run tests with your built Python
- **cpython-code-style**: Run linting and checks before committing

## Automated Build Agents

Remember: **Use sub-agents when running configure and make build steps!**

This allows:
- Build processes to run in the background
- Better handling of long-running compilation
- Cleaner output management
