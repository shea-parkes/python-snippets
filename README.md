# Sporadic Python Snippets

I am usually a big proponent of creating Python packages.  Even some fairly small amount of logic often benefits from being packaged, versioned and reusable.

However, there are some times you want or need to stick with just the Python standard library.  The Python standard library is huge, but there are some gaps.  This repo contains a few snippets that I've found useful.  This is in the spirit of the "common recipes" section at the bottom of the `itertools` docs: [https://docs.python.org/3/library/itertools.html#itertools-recipes](itertools recipes).


## Logging

The default options to `logging.basicConfig()` are not to my taste.  This small code block matches my desires:

```python
import logging
import sys

logging.basicConfig(
    stream=sys.stdout,
    level=logging.DEBUG,
    format="%(asctime)s|%(name)s|%(levelname)s|%(message)s",
)
```

## Functional Programming

The `functools` standard library is nice.  But I really enjoy when I get to bring in something like [pytoolz](https://github.com/pytoolz/toolz).  There are times that you need to stick with the basic library, so these quick definitions at least provide some tools I miss:

```python
# From: https://en.wikipedia.org/wiki/Tacit_programming
from functools import partial, reduce
def compose(*funcs):
    """compose(f, g, h)(x) == h(g(f(x)))"""
    return partial(reduce, lambda val, func: func(val), funcs)
```

```python
# From: https://github.com/pytoolz/toolz
def pipe(val, *funcs):
    """pipe(x, f, g, h) == h(g(f(x)))"""
    for func in funcs:
        val = func(val)
    return val
```

```python
# From: https://github.com/pytoolz/toolz
def juxt(*funcs):
    """juxt(f, g, h)(x) = (f(x), g(x), h(x))"""
    return lambda val: tuple(func(val) for func in funcs)
```

If you do happen to have [pytoolz](https://github.com/pytoolz/toolz) (or even [cytoolz](https://github.com/pytoolz/cytoolz) for extra speed) available, then here is the import style I most identify with:
```python
import toolz.curried as z
import toolz.curried.operator as zop

# Or if speed is really important, and you bother to install `cytoolz`...
import cytoolz.curried as z
import cytoolz.curried.operator as zop
```

## CI Nonsense

Here's a reasonable python snippet to create a useful cache key:
```python
import hashlib, os, sys
version_hash = hashlib.sha256(sys.version.encode() + sys.executable.encode()).hexdigest()
with open(os.environ["GITHUB_OUTPUT"], mode="a") as fh_out:
    fh_out.write(f"pyver={version_hash}")
```
