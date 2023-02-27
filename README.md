# epicepoch

Test handling of pep440 epochs on pypi

## Testing PyPI

Published to https://test.pypi.org/project/epicepoch/

Published versions (in chronological order):

- 1.0.0
- 2.0.0
- 2023.1.1 (CalVer!)
- 1!1.0.1 (no more CalVer)
- 1!1.1.0


## pip install

Attempts to install (from test.pypi.org):

- `python3 -m pip install -i https://test.pypi.org/simple epicepoch` - `1!1.1.0` ✔️
- `python3 -m pip install -i https://test.pypi.org/simple epicepoch==1.*` - `1.0.0` (epoch 0)
- `python3 -m pip install -i https://test.pypi.org/simple epicepoch==1!1.*` - `1!1.1.0` ✔️

### Pip bugs (some more than others)

Definitely a bug:

- `python3 -m pip install -i https://test.pypi.org/simple epicepoch==0!1.*` -
  ```
  ERROR: No matching distribution found for epicepoch==0!1.*
  ```

  This is a failure to implement pep440, which [specifies](https://peps.python.org/pep-0440/#version-epochs):

  > If no explicit epoch is given, the implicit epoch is 0.

  so `0!1.0.1` and `1.0.1` are the same version, but only one form works.

- `python3 -m pip install -i https://test.pypi.org/simple epicepoch==1!*` -
  ```
  ERROR: Could not find a version that satisfies the requirement epicepoch==1!* (from versions: 1.0.0, 2.0.0, 2023.1.1, 1!1.0.1, 1!1.1.0)
  ```

  (this one is arguable - pip doesn't support `*` as a version pattern)


Another debateable bug (more a Arguably a bug

```
$ python3 -m pip install -i https://test.pypi.org/simple 'epicepoch>=1.5'
Looking in indexes: https://test.pypi.org/simple
Collecting epicepoch>=1.5
  Using cached https://test-files.pythonhosted.org/packages/88/cb/4af2a6dc1a17f40a75de8a86084009a8ed60638d293c32c80cfd5f2553c4/epicepoch-1%211.1.0-py3-none-any.whl (2.2 kB)
Installing collected packages: epicepoch
Successfully installed epicepoch-1!1.1.0
```

So `>=1.5` successfully installs `1!1.1.0`.

This is correct if you consider it `>=0!1.5`, but incorrect if you consider the version constraint itself, which is not satisfied.

## Suggested pip behavior

Instead of implicitly adding `0!` to install requests, it could make sense if `pip install` constraints without an epoch specified accepted _any_ epoch.
i.e. `pip install epicepoch>=1.5` would accept any version from any epoch where the version number matches `>=1.5`.
So it would accept `0!2.0.0`, but _not_ `1!1.1.0`.
That seems far more likely to match user intent in practice than installing `1!1.1.0`.

This would be a big step toward epochs being transparent to users, which they cannot be at the moment.

## `packaging.version.parse`

`packaging.version` handles `epoch` ordering

```python
from packaging.version import parse as V

V("2023.1.1") < V("1!1.1.0") # True
V("2.0.0") < V("1!1.1.0")  # True
```
