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
