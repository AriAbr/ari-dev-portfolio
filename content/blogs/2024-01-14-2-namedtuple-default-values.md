+++
title = 'Namedtuple Default Values'
date = 2025-01-14T21:49:10+02:00
draft = false
+++

I've been making my way through a deep-dive python series on Udemy (I highly recommend it - [Python 3: Deep Dive by Dr. Fred Baptiste](https://www.udemy.com/course/python-3-deep-dive-part-1/)). The course recently touched on the question of how to set default arguments on a `namedtuple`. I found out afterwards that as of python 3.7 `namedtuple` has a `defaults` argument ([see the docs](https://docs.python.org/3/library/collections.html#namedtuple-factory-function-for-tuples-with-named-fields)), making the following neat little trick largely irrelevant. We'll pretend that doesn't exist for the moment.

Dr. Baptiste's way around the problem relies on the `__new__` method and the `__defaults__` property, neither of which typically get much air time:

- `__new__` is a built in method on all classes. It creates an instance of the class (before `__init__` gets called)
- `__defaults__` is a property on all functions that stores the default values, and it's writable
  - if there are fewer defaults than function arguments, the values get "right aligned". See the screenshot:
  <div class="text-center">
      <img src="/images/blogs/2024-01-14-2-namedtuple-default-values/right-aligned.png" height="350">
      <br>
      <i class="small">Defaults get "right aligned"</i>
  </div>

If you're on python 3.7+, stick with the built-in option. If not though, good to know you can do this:

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y", "z"])
Point.__new__.__defaults__ = (2, 3) # Default `y` to 2, and `z` to 3. `x` has no default

print(Point(1))
# Point(x=1, y=2, z=3)
```
