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
- `python3 -m pip install -i https://test.pypi.org/simple epicepoch==1!*` -
  ```
  ERROR: Could not find a version that satisfies the requirement epicepoch==1!* (from versions: 1.0.0, 2.0.0, 2023.1.1, 1!1.0.1, 1!1.1.0)
  ```

## `packaging.version.parse`

`packaging.version` handles `epoch` ordering

```python
from packaging.version import parse as V

V("2023.1.1") < V("1!1.1.0") # True
V("2.0.0") < V("1!1.1.0")  # True
```
