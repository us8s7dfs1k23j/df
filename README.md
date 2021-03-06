# Preprocessing functions

We support 4 preprocessing functions to alternate data values or data structures: `map`, `filter`, `split`, `flatten`. 
Each preprocessing function is a Python function that takes a pair of a data value and its index as input and is applied on every node in subtrees of the resources of a dataset (inorder depth-first traversal).
The function can modify the resource's data tree directly, or write its output to a new tree.

Below is an excerpt of data of a life-expectancy table that we will use to explain preprocessing functions.

```json
[
  ["South Sudan", "", "2016", "", "2015", "", ...],
  ["Indicator", "age group", "male", "female", "male", "female", ...],
  ["Death probability", "< 1 year", "0.068", "0.058", "0.06", "0.072", ...],
  ["Death probability", "1 - 4 years", "0.008", "0.009", "0.009", "0.009", ...],
  ...
]
```

### `map` function

This function takes a pair of a data value and its index and maps it to a new value. We can use this function to split age group to start and end age as follow:

```python
def map(value, index):
  value = value.split("year")[0].strip()
  if value[0] == "<":
    return (0, value[2:])
  elif value.find("-") != -1:
    return value.split(" - ")
  else:
    return (value.split("+")[0], 999)
```

Note: in the representation language, we can omit the function declaration: `def map(value, index)`.

If we apply the function on the location of variable `age_group`, which is `[3:, 2]`, the updated data is going to be:
```json
[
  ["South Sudan", "", "2016", "", "2015", "", ...],
  ["Indicator", "age group", "male", "female", "male", "female", ...],
  ["Death probability", ["0", "1"], "0.068", "0.058", "0.06", "0.072", ...],
  ["Death probability", ["1", "4"], "0.008", "0.009", "0.009", "0.009", ...],
  ...
]
```

### `filter` function

This function takes a pair of a data value and its index and returns a boolean value. If the boolean value is false, we remove the data value out of the resource. For example, if we want to filter out empty value in the `year` variable, we can apply the below filter function below to the location `[1, 3:]`

```python
def filter(value, index):
  if value == "":
    return False
  return True
```
The updated data is:

```json
[
  ["South Sudan", "", "2016", "2015", ...],
  ["Indicator", "age group", "male", "female", "male", "female", ...],
  ["Death probability", "< 1 year", "0.068", "0.058", "0.06", "0.072", ...],
  ["Death probability", "1 - 4 years", "0.008", "0.009", "0.009", "0.009", ...],
  ...
]
```

### `split` function

This function also takes a pair of a data value and its index and returns a boolean value. When it is operating on child nodes that are elements of an array, if the returned value is true, it splits the array to two arrays at the current position. For example, we can make split values the variable `observation data` every two columns using the split function as follow:

```python
def split(value, index):
  # split every two columns
  return index[1] % 2 == 0
```

The updated data is:

```json
[
  ["South Sudan", "", "2016", "", "2015", "", ...],
  ["Indicator", "age group", "male", "female", "male", "female", ...],
  ["Death probability", "< 1 year", ["0.068", "0.058"], ["0.06", "0.072"], ...],
  ["Death probability", "1 - 4 years", ["0.008", "0.009"], ["0.009", "0.009"], ...],
  ...
]
```

### `flatten` function

This function is used to flatten a nested array. For example, we can reverse the effect of the split function by applying flatten on the same location (`[3:, 3:]`):

Data before flattening:

```json
[
  ["South Sudan", "", "2016", "", "2015", "", ...],
  ["Indicator", "age group", "male", "female", "male", "female", ...],
  ["Death probability", "< 1 year", ["0.068", "0.058"], ["0.06", "0.072"], ...],
  ["Death probability", "1 - 4 years", ["0.008", "0.009"], ["0.009", "0.009"], ...],
  ...
]
```

Data after flattening:
```json
[
  ["South Sudan", "", "2016", "", "2015", "", ...],
  ["Indicator", "age group", "male", "female", "male", "female", ...],
  ["Death probability", "< 1 year", "0.068", "0.058", "0.06", "0.072", ...],
  ["Death probability", "1 - 4 years", "0.008", "0.009", "0.009", "0.009", ...],
  ...
]
```
