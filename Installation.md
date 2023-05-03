
## MacOS

Here we'll show you how to install Spark 3.2.1 for MacOS.
We tested it on MacOS Monterey 12.0.1, but it should work
for other MacOS versions as well

### Installing Java

Ensure Brew and Java installed in your system:

```bash
xcode-select –install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
brew install java
```

Add the following environment variables to your `.bash_profile` or `.zshrc`:

```bash
export JAVA_HOME=/usr/local/Cellar/openjdk@11/11.0.12
export PATH="$JAVA_HOME/bin/:$PATH"
```

Make sure Java was installed to `/usr/local/Cellar/openjdk@11/11.0.12`: Open Finder > Press Cmd+Shift+G > paste "/usr/local/Cellar/openjdk@11/11.0.12". If you can't find it, then change the path location to appropriate path on your machine. You can also run `brew info java` to check where java was installed on your machine.

### Installing Spark

1. Install Scala

```bash
brew install scala@2.11
```

2. Install Apache Spark

```bash
brew install apache-spark
```

3. Add environment variables: 

Add the following environment variables to your `.bash_profile` or `.zshrc`. Replace the path to `SPARK_HOME` to the path on your own host. Run `brew info apache-spark` to get this.

```bash
export SPARK_HOME=/usr/local/Cellar/apache-spark/3.2.1/libexec
export PATH="$SPARK_HOME/bin/:$PATH"
```


### Testing Spark

Execute `spark-shell` and run the following in scala:

```scala
val data = 1 to 10000
val distData = sc.parallelize(data)
distData.filter(_ < 10).collect()
```



## PySpark

This document assumes you already have python.

To run PySpark, we first need to add it to `PYTHONPATH`:

```bash
export PYTHONPATH="${SPARK_HOME}/python/:$PYTHONPATH"
export PYTHONPATH="${SPARK_HOME}/python/lib/py4j-0.10.9-src.zip:$PYTHONPATH"
```

Make sure that the version under `${SPARK_HOME}/python/lib/` matches the filename of py4j or you will
encounter `ModuleNotFoundError: No module named 'py4j'` while executing `import pyspark`.

For example, if the file under `${SPARK_HOME}/python/lib/` is `py4j-0.10.9.3-src.zip`, then the
`export PYTHONPATH` statement above should be changed to

```bash
export PYTHONPATH="${SPARK_HOME}/python/lib/py4j-0.10.9.3-src.zip:$PYTHONPATH"
```


Now you can run Jupyter or IPython to test if things work. Go to some other directory, e.g. `~/tmp`.

Download a CSV file that we'll use for testing:

```bash
wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv
```

Now let's run `ipython` (or `jupyter notebook`) and execute:

```python
import pyspark
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .master("local[*]") \
    .appName('test') \
    .getOrCreate()

df = spark.read \
    .option("header", "true") \
    .csv('taxi+_zone_lookup.csv')

df.show()
```

Test that writing works as well:

```python
df.write.parquet('zones')
```



