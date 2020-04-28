# Functional Testing for Databricks notebooks

To be able to test a notebook, it needs to be testable. A simple way to achieve this is to pass parameters which define input and output of the notebook. To execute the test cases and perform assertions, a separate notebook can be used.



# Remarks

The use case implemented in this article loads [MovieLens](https://grouplens.org/datasets/movielens/) data into spark table.

Target table contains columns:

* movieId
* title
* min_rating
* max_rating
* num_ratings
* average_rating

To download, prepare and explore the test data I create a [prepare_test_data](prepare_test_data.ipynb) notebook.