+++
title = 'Python Imports: Behind the scenes'
date = 2025-01-15T19:05:18+02:00
draft = true
+++

<i><small>This is another post based on [Dr. Fred Baptiste' Python 3: Deep Dive](https://www.udemy.com/course/python-3-deep-dive-part-1/) series. For more, head over to Udemy.</small></i>

What happens behind the scenes when you call `import` in python? Glad I asked. Here's how it works:

1. <b>Check `sys.modules`:</b> Python first checks `sys.modules` to see if your module has already been loaded.
   - `sys.modules` is a regular `dict` that caches modules. The keys are strings of the modules' names and the values are module objects (i.e. objects of type `ModuleType`)
   - If your module is already in `sys.modules`, python uses that version. It adds the cached version to `globals()` and skips the steps below.
2. <b>Create a module object:</b> If python does not find the module in `sys.modules`, it creates a new `ModuleType` object and loads the module's source code into it
3. <b>Cache the module:</b> The new `module` objects is added to the `sys.modules` cache
4. <b>Compile and execute:</b> Python compiles and _executes_ the source code from your module
5. <b>Add to `globals()`:</b> Python and adds your module to `globals()`, making it available for use going forward.

Important Points:

- The first time you import a module, python will execute all of the code in that module.
- <i><b> Importing a module twice will not re-execute it</b></i>. Subsequent references to your module will still refer back to the cached module as it was originally imported.
- <i><b>The `sys.modules` cache _is not file-specific_.</b></i> This means that the point above applies across files. If you import the same module in multiple files in your program, it will get cached _once_ in `sys.modules`. After that, the cached version will be used.

Below is a bit of code to bring all of this home. We will:

1. Create a simple module (`module1`) with a print statement so we can see when the module gets executed.
2. Import `module1` into `main.py`, which will execute `module1`.
3. Delete `module1` from `globals()`, then try accessing it to demonstrate that it is no longer available.
4. Re-import `module1`. This will add `module1` back to `globals()`, but will use the cached version from `sys.modules` and will not re-execute `module1`.
5. Access `module1` again to demonstrate that is has in fact been added back to `globals()`

```python
# module1.py
print("Running module1")


# main.py
print("======== Running main.py ======== ")
print("Importing module1")
import module1
print("Deleting module1")
del globals()["module1"] # Deletes from globals(), but not from sys.modules
try:
    print("Accessing module 1 after deleting from globals()")
    # This will cause an exception. module1 is still cached in `sys.modules`,
    # but the reference to the cache has been removed from globals()
    module1
except NameError as e:
    print("NameError", e)
print("Re-importing module1")
# Adds the cached version of module1 back to globals(), without
# re-executing the module
import module1
print("Accessing module 1 after re-importing from globals()")
module1
print("Done!")
print("=================================")
```

Output from main.py:

```
======== Running main.py ========
Importing module1
Running module1
Deleting module1
Accessing module 1 after deleting from globals()
NameError name 'module1' is not defined
Re-importing module1
Accessing module 1 after re-importing from globals()
Done!
=================================
```
