---
name: deprecated
description: This skill triggers when any cpython-skills skill is requested. The cpython-skills plugin has been renamed to cpython for simpler invocation.
---

# Plugin Renamed

**The `cpython-skills` plugin has been renamed to `cpython`.**

To upgrade, run these commands:

```
/plugin uninstall cpython-skills
/plugin install cpython
```

After upgrading, skills are invoked as:
- `cpython:dev` - Development entry point and codebase orientation
- `cpython:build` - Building and testing CPython
- `cpython:style` - Code style and validation
- `cpython:docs` - Documentation and NEWS entries

The new naming is cleaner: `cpython:build` instead of `cpython-skills:cpython-build-and-test`.
