# Wheel Patching

You may notice a `wheelpatcher.py` script included in the repository. This is a standalone utility script and is **not** called automatically during the build process.

For the vast majority of users, this script is entirely out of scope and you will never need to run it.

## Why does it exist?

Python libraries that contain compiled C/C++ extensions (like `.so` files) can sometimes cause compatibility issues on Android if their filenames contain strict platform-specific tags (such as `.cpython-313.so`). 

The `wheelpatcher.py` script is used manually by project maintainers whenever the underlying **Panda3D wheels need to be updated** to the latest available version. It extracts the new wheels, renames the compiled shared libraries to remove those strict CPython version tags, and repackages them so the Android Python interpreter can locate and load them properly.

!!! tip "Do I need to run this?"
    No. The project already comes with the correctly patched wheels in the `project/wheels/` directory. You only need to run this script if you are manually updating the core Panda3D or Ursina `.whl` files yourself.