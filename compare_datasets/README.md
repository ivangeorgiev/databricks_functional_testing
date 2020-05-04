# Compare Datasets

Almost every test scenario includes equality test. Spark DataFrame doesn't support equality operation. `unittest` framework also doesn't support `assertEqual`  for Spark DataFrame.

However `unittest` can compare lists and dictionaries.

The technique described here can be used with virtually any test framework, e.g. `unittest`, `pytest`, `doctest`, `nose`, etc.

## `as_list` function

The function takes Spark DataFrame as an argument and coverts it to a list of dictionaries.

```python
# Convert Spark DataFrame into a list of dictionaries
movies_list = as_list(movies_df)
```

`movies_list` variable points to a list of dictionaries:

```
[
  { "movieId": 9999, "title": "Unknow within" },
  { "movieId": 6548, "title": "Bad Boys II (2003)" }
]
```

Convenient option, provided by the `as_list` function is the capability to sort the list. To get a sorted list, the `order_by` must be set to a key function, similar to Python's [sort](https://docs.python.org/3/howto/sorting.html#key-functions) function:

```python
from operator import itemgetter
# Convert Spark DataFrame into a sorted list of dictionaries
movies_list_sorted = as_list(movies_df, order_by=itemgetter("movieId"))
```

Now the `movies_list_sorted`  is sorted:

```
[
  { "movieId": 6548, "title": "Bad Boys II (2003)" },
  { "movieId": 9999, "title": "Unknow within" }
]
```

The sorted list could be passed to the `assertEqual` method:

```python
import unittest
expect = [
  { "movieId": 6548, "title": "Bad Boys II (2003)", },
  { "movieId": 9999, "title": "Unknow within", },
]

test_case = unittest.TestCase()
test_case.assertEqual(expect, movies_list_sorted)
```



## Notesbooks

* The [as_list](as_list.ipynb) notebook contains the definition of the `as_list` function.
* The [test_as_list](test_as_list.ipynb) contains unit test case for testing the `as_list` function.

