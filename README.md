# RDD-DataFrame-Sql

<h1>Apache Spark 2.0 API Improvements: RDD, DataFrame, Dataset and SQL</h1>
<h2>What’s New, What’s Changed and How to get Started.</h2>
<p>Are you ready for Apache Spark 2.0?</p>
<p>If you are just getting started with Apache Spark, the 2.0 release is the one to start with as the APIs have just gone through a major overhaul to improve ease-of-use.</p>
<p>If you are using an older version and want to learn what has changed then this article will give you the low down on why you should upgrade and what the impact to your code will be.</p>
<h2>What’s new with Apache Spark 2.0?</h2>
<p>Let’s start with the good news, and there’s plenty.</p>
<ul>
<li>There are really only two programmatic APIs now; RDD and Dataset. For backwards compatibility, DataFrame still exists but is just a synonym for a Dataset.</li>
<li>Spark SQL has been improved to support a wider range of queries, including correlated subqueries. This was largely led by an effort to run <a href="http://www.tpc.org/tpcds/" target="_blank">TPC-DS</a> benchmarks in Spark.</li>
<li>Performance is once again significantly improved thanks to advanced “<a href="https://databricks.com/blog/2016/05/23/apache-spark-as-a-compiler-joining-a-billion-rows-per-second-on-a-laptop.html" target="_blank">whole stage code generation</a>” when compiling query plans</li>
</ul>
<p><a href="https://github.com/databricks/spark-csv" target="_blank">CSV support</a> is now built-in and based on the DataBricks spark-csv project, making it a breeze to create Datasets from CSV data with little coding.</p>
<p><a href="https://spark.apache.org/releases/spark-release-2-0-0.html" target="_blank">Spark 2.0 is a major release</a>, and there are some breaking changes that mean you may need to rewrite some of your code. <a href="https://github.com/agildata/apache-spark-examples" target="_blank">Here </a>are some things we ran into when updating our <a href="https://github.com/agildata/apache-spark-examples" target="_blank">apache-spark-examples</a>.</p>
<ul>
<li>For Scala users, <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">SparkSession </span>replaces <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">SparkContext</span> and <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">SQLContext </span>as the top-level context, but still provides access to <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">SQLContext </span>and <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">SQLContext </span>for backwards compatibility</li>
<li><span style="font-family: Source Code Pro,monospace; font-size: 10pt;">DataFrame </span>is now a synonym for <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">Dataset[Row]</span> and you can use these two types interchangeably,  although we recommend using the latter.</li>
<li>Performing a <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">map()</span> operation on a <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">Dataset </span>now returns a <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">Dataset </span>rather than an RDD, reducing the need to keep switching between the two APIs, and improving performance.</li>
<li>Some Java functional interfaces, such as <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">FlatMapFunction</span>, have been updated to return <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">Iterator&lt;T&gt;</span> rather than <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">Iterable&lt;T&gt;</span>.</li>
</ul>

# RDD vs. Dataset 2.0


Both the RDD API and the Dataset API represent data sets of a specific class. For instance, you can create an RDD[Person] as well as a Dataset[Person] so both can provide compile-time type-safety. Both can also be used with the generic Row structure provided in Spark for cases where classes might not exist that represent the data being manipulated, such as when reading CSV files.

RDDs can be used with any Java or Scala class and operate by manipulating those objects directly with all of the associated costs of object creation, serialization and garbage collection.

Datasets are limited to classes that implement the Scala Product trait, such as case classes. There is a very good reason for this limitation. Datasets store data in an optimized binary format, often in off-heap memory, to avoid the costs of deserialization and garbage collection. Even though it feels like you are coding against regular objects, Spark is really generating its own optimized byte-code for accessing the data directly.

# RDD
    // raw object manipulation
    val rdd: RDD[Person] = …
    val rdd2: RDD[String] = rdd.map(person => person.lastName)
    
# Dataset    
      // optimized direct access to off-heap memory without deserializing objects
      val ds: Dataset[Person] = …
     val ds2: Dataset[String] = ds.map(person => person.lastName)
     
     
     
# Getting Started with Scala     

Here are some code samples to help you get started fast with Apache Spark 2.0 and Scala.

Creating SparkSession

SparkSession is now the starting point for a Spark driver program, instead of creating a SparkContext and a SQLContext.

        val spark = SparkSession.builder
              .master("local[*]")
              .appName("Example")
              .getOrCreate()

        // accessing legacy SparkContext and SQLContext 
        spark.sparkContext
        spark.sqlContext


Creating a Dataset from a collection

SparkSession provides a createDataset method that accepts a collection.


        val ds: Dataset[String] = spark.createDataset(List("one","two","three"))
        
Converting an RDD to a Dataset

SparkSession provides a createDataset method for converting an RDD to a Dataset. This only works if you import spark.implicits_ (where spark is the name of the SparkSession variable).

        // always import implicits so that Spark can infer types when creating Datasets
        import spark.implicits._

        val rdd: RDD[Person] = ??? // assume this exists
        val dataset: Dataset[Person] = spark.createDataset[Person](rdd)
        
        
Converting a DataFrame to a Dataset

A DataFrame (which is really a Dataset[Row]) can be converted to a Dataset of a specific class by performing a map() operation.

        // read a text file into a DataFrame a.k.a. Dataset[Row]
        var df: Dataset[Row] = spark.read.text("people.txt")

        // use map() to convert to a Dataset of a specific class
        var ds: Dataset[Person] = spark.read.text("people.txt")
              .map(row => parsePerson(row))

        def parsePerson(row: Row) : Person = ??? // fill in parsing logic here


Reading a CSV directly as a Dataset

The built-in CSV support makes it easy to read a CSV and return a Dataset of a specific case class. This only works if the CSV contains a header row and the field names match the case class.

        val ds: Dataset[Person] = spark.read
            .option("header","true")
            .csv("people.csv")
            .as[Person]
            
# Spark+Scala beats Spark+Java    

Using Apache Spark with Java is harder than using Apache Spark with Scala and we spent significantly longer upgrading our Java examples than we did with our Scala examples, including running into some confusing runtime errors that were hard to track down (for example, we hit a runtime error with Spark’s code generation because one of our Java classes was not declared as public).

Also, we weren’t always able to use concise lambda functions even though we are using Java 8, and had to revert to anonymous inner classes with verbose (and confusing) syntax.
