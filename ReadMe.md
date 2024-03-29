RecursionSafe.py [![Unlicensed work](https://raw.githubusercontent.com/unlicense/unlicense.org/master/static/favicon.png)](https://unlicense.org/)
================================
~~[wheel (GitLab)](https://gitlab.com/KOLANICH/RecursionSafe.py/-/jobs/artifacts/master/raw/dist/RecursionSafe-0.CI-py3-none-any.whl?job=build)~~
~~[wheel (GHA via `nightly.link`)](https://nightly.link/KOLANICH-libs/RecursionSafe.py/workflows/CI/master/RecursionSafe-0.CI-py3-none-any.whl)~~
~~![GitLab Build Status](https://gitlab.com/KOLANICH/RecursionSafe.py/badges/master/pipeline.svg)~~
~~![GitLab Coverage](https://gitlab.com/KOLANICH/RecursionSafe.py/badges/master/coverage.svg)~~
~~[![GitHub Actions](https://github.com/KOLANICH-libs/RecursionSafe.py/workflows/CI/badge.svg)](https://github.com/KOLANICH-libs/RecursionSafe.py/actions/)~~
![N∅ hard dependencies](https://shields.io/badge/-N∅_Ъ_deps!-0F0)
[![Libraries.io Status](https://img.shields.io/librariesio/github/KOLANICH-libs/RecursionSafe.py.svg)](https://libraries.io/github/KOLANICH-libs/RecursionSafe.py)
[![Code style: antiflash](https://img.shields.io/badge/code%20style-antiflash-FFF.svg)](https://codeberg.org/KOLANICH-tools/antiflash.py)

A context manager to protect you from infinite recursion when walking collections.

Just wrap a recursive function with it. If you visit any object you are already in you will get ellipsis instead of the object, be ready for it.

Example
-------
```python
recSafe = RecursionSafe()

objs = {
	"a": {},
	"a.b": {},
	"a.b.c": {},
	"a.b.d": {},
	"a.b.c.d": 14,
	"a.e": 10,
	"a.f": {},
}

objs["a"]["b"] = objs["a.b"]
objs["a.b"]["c"] = objs["a"]
objs["a.b"]["d"] = objs["a.b.d"]
objs["a.b.c"]["d"] = objs["a.b.c.d"]
objs["a"]["e"] = objs["a.e"]
objs["a"]["f"] = objs["a.f"]
objs["a.f"]["g"] = objs["a"]
a = objs["a"]

idsToNames = {id(objs[k]): k for k in objs}


@recSafe.wrap
def ownRepr(a):
	if isinstance(a, dict):
		return "{" + ", ".join((ownRepr(k) + ":" + ownRepr(v) for k, v in a.items())) + "}"
	else:
		return repr(a)


print("repr", repr(a))  # even non-recursive objects are ellipsed
print("ownRepr", ownRepr(a))  # feel the difference - non-self-referencing objects are not ellipsed
```

