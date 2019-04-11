# Preprocessing functions

We support 4 preprocessing functions to alternate data values or data structures: `map`, `filter`, `split`, `flatten`. 
Each preprocessing function is a Python function that takes a pair of a data value and its index as input and is applied on every node subtrees of the resources of a dataset (inorder depth-first traversal).
The function can modify the resource's data tree directly, or write its output to a new tree.
