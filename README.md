# Functional testing for databricks notebooks

To be able to test a notebook, it needs to be testable. A simple way to achieve this is to pass parameters which define input and output of the notebook. To execute the test cases and perform assertions, a separate notebook can be used.

The sample use-case project (https://github.com/ivangeorgiev/databricks_functional_testing) illustrates this approach with two notebooks:

* The [load_movies](load_movies.ipynb) notebook loads movie and rating data stored in JSON format, enriches the movie data ( [MovieLens](https://grouplens.org/datasets/movielens/)) with rating aggregations and writes the result into Spark table.
* The [test_load_movies](test_load_movies.ipynb) notebook creates pre-defined data for the test case than calls the `load_movies` notebook and performs assertions on the loaded table.

## `load_movies` Notebook

The input and output of the notebook is defined via notebook parameters.

```python
default_root = "dbfs:/FileStore/movielens/test-data"
dbutils.widgets.text("movie_data", 
                     defaultValue=f"{default_root}/movies.json", 
                     label="Movie source path")
dbutils.widgets.text("ratings_data", 
                     defaultValue=f"{default_root}/ratings.json", 
                     label="Ratings source path")
dbutils.widgets.text("target_table",
                     defaultValue="test_load_movies",
                     label="Output movie table name")
```

Setting default values for the parameters to actual development data makes easier the development process. It is convenient to store development and test data in DBFS. In production the location could be remote URL, for example Blob, ADLS Gen 2.

## `test_load_movies` Notebook

To implement proper test case Python `unittest` module is used.

```python
class TestLoadMovies(unittest.TestCase):
  # Define test case constants
  movies_path = "dbfs:/FileStore/movielens/test-data/movies.json"
  ratings_path = "dbfs:/FileStore/movielens/test-data/ratings.json"
  target_table = "test_load_movies"
  
  # Define input fixtures
  fixture_movies_json = """..."""
  fixture_ratings_json = """..."""
    
  # Define helpers
  def get_loaded_movies(self, movies_table):
    # ...
  
  # The setUp method is called before each test.
  # Use it to setup sandbox.
  def setUp(self):
    # ...
  
  # Define helpers for setting up test data 
  def given_test_data(self):
    # ...

  # Stimulus (actions) can be defined as methods.
  # This is the place where we run the notebook.
  def when_load_movies(self):
    # ...
  
  # All methods starting with "test" are tests, executed by unittest.
  # use the test method to build your test scenario:
  #   GIVEN ... (setup)
  #   WHEN  ... (action or stimulus)
  #   THEN  ... (validation or assert)
  def test_movies_loaded(self):
    # ...
```



`unittest` provides simple execution approach which is compatible with Databricks notebooks.

```python
suite = unittest.TestLoader().loadTestsFromTestCase(TestLoadMovies)
runner = unittest.TextTestRunner(verbosity=2)
runner.run(suite)
```

Functional test notebooks can be integrated into CI/CD pipelines and executed automatically.

## How and when to use?

Illustrated approach can be used efficiently for notebook functional testing. Notebooks themselves are not great way to engineer software. For big projects it is recommended to follow good software development practices and package your code in modules and execute them directly as Databricks jobs. The modules should be unit tested using frameworks like `pytest`, `unittest` etc.

Notebooks still can used, but they should be short with only responsibility to orchestrate the data flow - glue the boundaries between input, processing and output. These notebooks can be tested with the described approach.

## What's next?

Almost every test scenario requires to verify that actually produced dataset is equal to the exepected dataset. Here is a useful technique which helps to [test two Spark DataFrames are equal](datasets_equal/).

