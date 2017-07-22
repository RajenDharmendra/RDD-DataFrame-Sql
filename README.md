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
<blockquote><p><strong>Get help upgrading to Apache Spark 2.0 or making the transition from Java to Scala. <a href="http://www.agildata.com/contact-us/">Contact Us!</a></strong></p></blockquote>
<h2>RDD vs. Dataset 2.0</h2>
<p>Both the RDD API and the Dataset API represent data sets of a specific class. For instance, you can create an <span style="font-family: Source Code Pro,monospace; font-size: 10pt;">RDD[Person]</span> as well as a <span style="font-size: 10pt; font-family: Source Code Pro,monospace;">Dataset[Person]</span> so both can provide compile-time type-safety. Both can also be used with the generic Row structure provided in Spark for cases where classes might not exist that represent the data being manipulated, such as when reading CSV files.</p>
<p>RDDs can be used with any Java or Scala class and operate by manipulating those objects directly with all of the associated costs of object creation, serialization and garbage collection.</p>
<p>Datasets are limited to classes that implement the Scala Product trait, such as case classes. There is a very good reason for this limitation. Datasets store data in an optimized binary format, often in off-heap memory, to avoid the costs of deserialization and garbage collection. Even though it feels like you are coding against regular objects, Spark is really generating its own optimized byte-code for accessing the data directly.</p>
<p><strong>RDD</strong></p>

		
// raw object manipulation
val rdd: RDD[Person] = …
val rdd2: RDD[String] = rdd.map(person =&gt; person.lastName)</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf37476095573-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf37476095573-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf37476095573-3">3</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf37476095573-1"><span class="crayon-c">// raw object manipulation</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf37476095573-2"><span class="crayon-e">val </span><span class="crayon-v">rdd</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">RDD</span><span class="crayon-sy">[</span><span class="crayon-v">Person</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span>…</div><div class="crayon-line" id="crayon-5973b048cdf37476095573-3"><span class="crayon-e">val </span><span class="crayon-v">rdd2</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">RDD</span><span class="crayon-sy">[</span><span class="crayon-t">String</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">rdd</span><span class="crayon-sy">.</span><span class="crayon-e">map</span><span class="crayon-sy">(</span><span class="crayon-v">person</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-o">&gt;</span><span class="crayon-h"> </span><span class="crayon-v">person</span><span class="crayon-sy">.</span><span class="crayon-v">lastName</span><span class="crayon-sy">)</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p><strong>Dataset</strong></p>
// optimized direct access to off-heap memory without deserializing objects
val ds: Dataset[Person] = …
val ds2: Dataset[String] = ds.map(person =&gt; person.lastName)</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
				
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf3e484041759-1"><span class="crayon-c">// optimized direct access to off-heap memory without deserializing objects</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf3e484041759-2"><span class="crayon-e">val </span><span class="crayon-v">ds</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Dataset</span><span class="crayon-sy">[</span><span class="crayon-v">Person</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span>…</div><div class="crayon-line" id="crayon-5973b048cdf3e484041759-3"><span class="crayon-e">val </span><span class="crayon-v">ds2</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Dataset</span><span class="crayon-sy">[</span><span class="crayon-t">String</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">ds</span><span class="crayon-sy">.</span><span class="crayon-e">map</span><span class="crayon-sy">(</span><span class="crayon-v">person</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-o">&gt;</span><span class="crayon-h"> </span><span class="crayon-v">person</span><span class="crayon-sy">.</span><span class="crayon-v">lastName</span><span class="crayon-sy">)</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h2>Getting Started with Scala</h2>
<p>Here are some code samples to help you get started fast with Apache Spark 2.0 and Scala.</p>
<h3>Creating SparkSession</h3>
<p>SparkSession is now the starting point for a Spark driver program, instead of creating a SparkContext and a SQLContext.</p>
val spark = SparkSession.builder
      .master("local[*]")
      .appName("Example")
      .getOrCreate()

// accessing legacy SparkContext and SQLContext 
spark.sparkContext
spark.sqlContext</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf40069825256-1"><span class="crayon-e">val </span><span class="crayon-v">spark</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">SparkSession</span><span class="crayon-sy">.</span><span class="crayon-i">builder</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf40069825256-2"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">master</span><span class="crayon-sy">(</span><span class="crayon-s">"local[*]"</span><span class="crayon-sy">)</span></div><div class="crayon-line" id="crayon-5973b048cdf40069825256-3"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">appName</span><span class="crayon-sy">(</span><span class="crayon-s">"Example"</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf40069825256-4"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">getOrCreate</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span></div><div class="crayon-line" id="crayon-5973b048cdf40069825256-5">&nbsp;</div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf40069825256-6"><span class="crayon-c">// accessing legacy SparkContext and SQLContext </span></div><div class="crayon-line" id="crayon-5973b048cdf40069825256-7"><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-e">sparkContext</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf40069825256-8"><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-v">sqlContext</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h3>Creating a Dataset from a collection</h3>
<p>SparkSession provides a createDataset method that accepts a collection.</p><!-- Crayon Syntax Highlighter v_2.7.2_beta -->

	
var ds: Dataset[String] = spark.createDataset(List("one","two","three"))</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf43465253657-1">1</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf43465253657-1"><span class="crayon-t">var</span><span class="crayon-h"> </span><span class="crayon-v">ds</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Dataset</span><span class="crayon-sy">[</span><span class="crayon-t">String</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-e">createDataset</span><span class="crayon-sy">(</span><span class="crayon-e">List</span><span class="crayon-sy">(</span><span class="crayon-s">"one"</span><span class="crayon-sy">,</span><span class="crayon-s">"two"</span><span class="crayon-sy">,</span><span class="crayon-s">"three"</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h3>Converting an RDD to a Dataset</h3>
<p>SparkSession provides a createDataset method for converting an RDD to a Dataset. This only works if you import spark.implicits_ (where spark is the name of the SparkSession variable).</p><!-- Crayon Syntax Highlighter v_2.7.2_beta -->

		
// always import implicits so that Spark can infer types when creating Datasets
import spark.implicits._

val rdd: RDD[Person] = ??? // assume this exists
val dataset: Dataset[Person] = spark.createDataset[Person](rdd)</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf45162821502-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf45162821502-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf45162821502-3">3</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf45162821502-4">4</div><div class="crayon-num" data-line="crayon-5973b048cdf45162821502-5">5</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf45162821502-1"><span class="crayon-c">// always import implicits so that Spark can infer types when creating Datasets</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf45162821502-2"><span class="crayon-e">import </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-v">implicits</span><span class="crayon-sy">.</span><span class="crayon-i">_</span></div><div class="crayon-line" id="crayon-5973b048cdf45162821502-3">&nbsp;</div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf45162821502-4"><span class="crayon-e">val </span><span class="crayon-v">rdd</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">RDD</span><span class="crayon-sy">[</span><span class="crayon-v">Person</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-sy">?</span><span class="crayon-sy">?</span><span class="crayon-sy">?</span><span class="crayon-h"> </span><span class="crayon-c">// assume this exists</span></div><div class="crayon-line" id="crayon-5973b048cdf45162821502-5"><span class="crayon-e">val </span><span class="crayon-v">dataset</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Dataset</span><span class="crayon-sy">[</span><span class="crayon-v">Person</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-v">createDataset</span><span class="crayon-sy">[</span><span class="crayon-v">Person</span><span class="crayon-sy">]</span><span class="crayon-sy">(</span><span class="crayon-v">rdd</span><span class="crayon-sy">)</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>
<!-- [Format Time: 0.0005 seconds] -->
<p></p>
<h3>Converting a DataFrame to a Dataset</h3>
<p>A DataFrame (which is really a Dataset[Row]) can be converted to a Dataset of a specific class by performing a map() operation.</p>
// read a text file into a DataFrame a.k.a. Dataset[Row]
var df: Dataset[Row] = spark.read.text("people.txt")

// use map() to convert to a Dataset of a specific class
var ds: Dataset[Person] = spark.read.text("people.txt")
      .map(row =&gt; parsePerson(row))

def parsePerson(row: Row) : Person = ??? // fill in parsing logic here</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf47119930592-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf47119930592-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf47119930592-3">3</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf47119930592-4">4</div><div class="crayon-num" data-line="crayon-5973b048cdf47119930592-5">5</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf47119930592-6">6</div><div class="crayon-num" data-line="crayon-5973b048cdf47119930592-7">7</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf47119930592-8">8</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf47119930592-1"><span class="crayon-c">// read a text file into a DataFrame a.k.a. Dataset[Row]</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf47119930592-2"><span class="crayon-t">var</span><span class="crayon-h"> </span><span class="crayon-v">df</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Dataset</span><span class="crayon-sy">[</span><span class="crayon-v">Row</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-v">read</span><span class="crayon-sy">.</span><span class="crayon-e">text</span><span class="crayon-sy">(</span><span class="crayon-s">"people.txt"</span><span class="crayon-sy">)</span></div><div class="crayon-line" id="crayon-5973b048cdf47119930592-3">&nbsp;</div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf47119930592-4"><span class="crayon-c">// use map() to convert to a Dataset of a specific class</span></div><div class="crayon-line" id="crayon-5973b048cdf47119930592-5"><span class="crayon-t">var</span><span class="crayon-h"> </span><span class="crayon-v">ds</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Dataset</span><span class="crayon-sy">[</span><span class="crayon-v">Person</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-v">read</span><span class="crayon-sy">.</span><span class="crayon-e">text</span><span class="crayon-sy">(</span><span class="crayon-s">"people.txt"</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf47119930592-6"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">map</span><span class="crayon-sy">(</span><span class="crayon-v">row</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-o">&gt;</span><span class="crayon-h"> </span><span class="crayon-e">parsePerson</span><span class="crayon-sy">(</span><span class="crayon-v">row</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span></div><div class="crayon-line" id="crayon-5973b048cdf47119930592-7">&nbsp;</div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf47119930592-8"><span class="crayon-e">def </span><span class="crayon-e">parsePerson</span><span class="crayon-sy">(</span><span class="crayon-v">row</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Row</span><span class="crayon-sy">)</span><span class="crayon-h"> </span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Person</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-sy">?</span><span class="crayon-sy">?</span><span class="crayon-sy">?</span><span class="crayon-h"> </span><span class="crayon-c">// fill in parsing logic here</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h3>Reading a CSV directly as a Dataset</h3>
<p>The built-in CSV support makes it easy to read a CSV and return a Dataset of a specific case class. This only works if the CSV contains a header row and the field names match the case class.</p><!-- Crayon Syntax Highlighter v_2.7.2_beta -->

val ds: Dataset[Person] = spark.read
    .option("header","true")
    .csv("people.csv")
    .as[Person]</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf49097271905-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf49097271905-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf49097271905-3">3</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf49097271905-4">4</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf49097271905-1"><span class="crayon-e">val </span><span class="crayon-v">ds</span><span class="crayon-o">:</span><span class="crayon-h"> </span><span class="crayon-v">Dataset</span><span class="crayon-sy">[</span><span class="crayon-v">Person</span><span class="crayon-sy">]</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-i">read</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf49097271905-2"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">option</span><span class="crayon-sy">(</span><span class="crayon-s">"header"</span><span class="crayon-sy">,</span><span class="crayon-s">"true"</span><span class="crayon-sy">)</span></div><div class="crayon-line" id="crayon-5973b048cdf49097271905-3"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">csv</span><span class="crayon-sy">(</span><span class="crayon-s">"people.csv"</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf49097271905-4"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-st">as</span><span class="crayon-sy">[</span><span class="crayon-v">Person</span><span class="crayon-sy">]</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h2>Getting Started with Java</h2>
<p>Here are some code samples to help you get started fast with Spark 2.0 and Java.</p>
<h3>Creating SparkSession</h3>
<p></p>
SparkSession spark = SparkSession.builder()
  .master("local[*]")
  .appName("Example")
  .getOrCreate();

  // Java still requires of the JavaSparkContext 
  JavaSparkContext sc = new JavaSparkContext(spark.sparkContext());</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf4b435255968-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf4b435255968-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf4b435255968-3">3</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf4b435255968-4">4</div><div class="crayon-num" data-line="crayon-5973b048cdf4b435255968-5">5</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf4b435255968-6">6</div><div class="crayon-num" data-line="crayon-5973b048cdf4b435255968-7">7</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf4b435255968-1"><span class="crayon-e">SparkSession </span><span class="crayon-v">spark</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">SparkSession</span><span class="crayon-sy">.</span><span class="crayon-e">builder</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf4b435255968-2"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">master</span><span class="crayon-sy">(</span><span class="crayon-s">"local[*]"</span><span class="crayon-sy">)</span></div><div class="crayon-line" id="crayon-5973b048cdf4b435255968-3"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">appName</span><span class="crayon-sy">(</span><span class="crayon-s">"Example"</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf4b435255968-4"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">getOrCreate</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-sy">;</span></div><div class="crayon-line" id="crayon-5973b048cdf4b435255968-5">&nbsp;</div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf4b435255968-6"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-c">// Java still requires of the JavaSparkContext </span></div><div class="crayon-line" id="crayon-5973b048cdf4b435255968-7"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-e">JavaSparkContext </span><span class="crayon-v">sc</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-r">new</span><span class="crayon-h"> </span><span class="crayon-e">JavaSparkContext</span><span class="crayon-sy">(</span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-e">sparkContext</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span><span class="crayon-sy">;</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h3>Creating a Dataset from a collection</h3>
<p>SparkSession provides a createDataset method that accepts a collection.</p><!-- Crayon Syntax Highlighter v_2.7.2_beta -->

Dataset&lt;Person&gt; ds = spark.createDataset(
    Collections.singletonList(new Person(1, "Joe", "Bloggs")),
    Encoders.bean(Person.class)
);</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf4d882973012-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf4d882973012-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf4d882973012-3">3</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf4d882973012-4">4</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf4d882973012-1"><span class="crayon-v">Dataset</span><span class="crayon-o">&lt;</span><span class="crayon-v">Person</span><span class="crayon-o">&gt;</span><span class="crayon-h"> </span><span class="crayon-v">ds</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-e">createDataset</span><span class="crayon-sy">(</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf4d882973012-2"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-v">Collections</span><span class="crayon-sy">.</span><span class="crayon-e">singletonList</span><span class="crayon-sy">(</span><span class="crayon-r">new</span><span class="crayon-h"> </span><span class="crayon-e">Person</span><span class="crayon-sy">(</span><span class="crayon-cn">1</span><span class="crayon-sy">,</span><span class="crayon-h"> </span><span class="crayon-s">"Joe"</span><span class="crayon-sy">,</span><span class="crayon-h"> </span><span class="crayon-s">"Bloggs"</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span><span class="crayon-sy">,</span></div><div class="crayon-line" id="crayon-5973b048cdf4d882973012-3"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-v">Encoders</span><span class="crayon-sy">.</span><span class="crayon-e">bean</span><span class="crayon-sy">(</span><span class="crayon-v">Person</span><span class="crayon-sy">.</span><span class="crayon-t">class</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf4d882973012-4"><span class="crayon-sy">)</span><span class="crayon-sy">;</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h3>Converting an RDD to a Dataset</h3>
<p>SparkSession provides a createDataset method for converting an RDD to a Dataset.</p>
Dataset&lt;Person&gt; ds = spark.createDataset(
  javaRDD.rdd(), // convert a JavaRDD to an RDD
  Encoders.bean(Person.class)
);</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf4f464791789-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf4f464791789-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf4f464791789-3">3</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf4f464791789-4">4</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf4f464791789-1"><span class="crayon-v">Dataset</span><span class="crayon-o">&lt;</span><span class="crayon-v">Person</span><span class="crayon-o">&gt;</span><span class="crayon-h"> </span><span class="crayon-v">ds</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-e">createDataset</span><span class="crayon-sy">(</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf4f464791789-2"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-v">javaRDD</span><span class="crayon-sy">.</span><span class="crayon-e">rdd</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-sy">,</span><span class="crayon-h"> </span><span class="crayon-c">// convert a JavaRDD to an RDD</span></div><div class="crayon-line" id="crayon-5973b048cdf4f464791789-3"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-v">Encoders</span><span class="crayon-sy">.</span><span class="crayon-e">bean</span><span class="crayon-sy">(</span><span class="crayon-v">Person</span><span class="crayon-sy">.</span><span class="crayon-t">class</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf4f464791789-4"><span class="crayon-sy">)</span><span class="crayon-sy">;</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>
<!-- [Format Time: 0.0004 seconds] -->
<p></p>
<h3>Converting a DataFrame to a Dataset</h3>
<p>A DataFrame (which is really a Dataset[Row]) can be converted to a Dataset of a specific class by performing a map() operation.</p>
Dataset&lt;Person&gt; ds = df.map(new MapFunction&lt;Row, Person&gt;() {
  @Override
  public Person call(Row value) throws Exception {
    return new Person(Integer.parseInt(value.getString(0)), 
                      value.getString(1), 
                      value.getString(2));
  }
}, Encoders.bean(Person.class));</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf51235861378-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf51235861378-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf51235861378-3">3</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf51235861378-4">4</div><div class="crayon-num" data-line="crayon-5973b048cdf51235861378-5">5</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf51235861378-6">6</div><div class="crayon-num" data-line="crayon-5973b048cdf51235861378-7">7</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf51235861378-8">8</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf51235861378-1"><span class="crayon-v">Dataset</span><span class="crayon-o">&lt;</span><span class="crayon-v">Person</span><span class="crayon-o">&gt;</span><span class="crayon-h"> </span><span class="crayon-v">ds</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">df</span><span class="crayon-sy">.</span><span class="crayon-e">map</span><span class="crayon-sy">(</span><span class="crayon-r">new</span><span class="crayon-h"> </span><span class="crayon-v">MapFunction</span><span class="crayon-o">&lt;</span><span class="crayon-v">Row</span><span class="crayon-sy">,</span><span class="crayon-h"> </span><span class="crayon-v">Person</span><span class="crayon-o">&gt;</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-h"> </span><span class="crayon-sy">{</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf51235861378-2"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-sy">@</span><span class="crayon-e">Override</span></div><div class="crayon-line" id="crayon-5973b048cdf51235861378-3"><span class="crayon-e">&nbsp;&nbsp;</span><span class="crayon-m">public</span><span class="crayon-h"> </span><span class="crayon-e">Person </span><span class="crayon-e">call</span><span class="crayon-sy">(</span><span class="crayon-e">Row </span><span class="crayon-v">value</span><span class="crayon-sy">)</span><span class="crayon-h"> </span><span class="crayon-e">throws</span><span class="crayon-h"> </span><span class="crayon-e">Exception</span><span class="crayon-h"> </span><span class="crayon-sy">{</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf51235861378-4"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-st">return</span><span class="crayon-h"> </span><span class="crayon-r">new</span><span class="crayon-h"> </span><span class="crayon-e">Person</span><span class="crayon-sy">(</span><span class="crayon-t">Integer</span><span class="crayon-sy">.</span><span class="crayon-e">parseInt</span><span class="crayon-sy">(</span><span class="crayon-v">value</span><span class="crayon-sy">.</span><span class="crayon-e">getString</span><span class="crayon-sy">(</span><span class="crayon-cn">0</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span><span class="crayon-sy">,</span><span class="crayon-h"> </span></div><div class="crayon-line" id="crayon-5973b048cdf51235861378-5"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-v">value</span><span class="crayon-sy">.</span><span class="crayon-e">getString</span><span class="crayon-sy">(</span><span class="crayon-cn">1</span><span class="crayon-sy">)</span><span class="crayon-sy">,</span><span class="crayon-h"> </span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf51235861378-6"><span class="crayon-h">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="crayon-v">value</span><span class="crayon-sy">.</span><span class="crayon-e">getString</span><span class="crayon-sy">(</span><span class="crayon-cn">2</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span><span class="crayon-sy">;</span></div><div class="crayon-line" id="crayon-5973b048cdf51235861378-7"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-sy">}</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf51235861378-8"><span class="crayon-sy">}</span><span class="crayon-sy">,</span><span class="crayon-h"> </span><span class="crayon-v">Encoders</span><span class="crayon-sy">.</span><span class="crayon-e">bean</span><span class="crayon-sy">(</span><span class="crayon-v">Person</span><span class="crayon-sy">.</span><span class="crayon-t">class</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span><span class="crayon-sy">;</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h3>Reading a CSV directly as a Dataset</h3>
<p>The built-in CSV support makes it easy to read a CSV and return a Dataset of a specific case class. This only works if the CSV contains a header row and the field names match the case class.</p><!-- Crayon Syntax Highlighter v_2.7.2_beta -->

Dataset&lt;Person&gt; ds = spark.read()
  .option("header", "true")
  .csv("testdata/people.csv")
  .as(Encoders.bean(Person.class));</textarea></div>
			<div class="crayon-main" style="">
				<table class="crayon-table">
					<tr class="crayon-row">
				<td class="crayon-nums " data-settings="show">
					<div class="crayon-nums-content" style="font-size: 14px !important; line-height: 26px !important;"><div class="crayon-num" data-line="crayon-5973b048cdf52237264663-1">1</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf52237264663-2">2</div><div class="crayon-num" data-line="crayon-5973b048cdf52237264663-3">3</div><div class="crayon-num crayon-striped-num" data-line="crayon-5973b048cdf52237264663-4">4</div></div>
				</td>
						<td class="crayon-code"><div class="crayon-pre" style="font-size: 14px !important; line-height: 26px !important; -moz-tab-size:4; -o-tab-size:4; -webkit-tab-size:4; tab-size:4;"><div class="crayon-line" id="crayon-5973b048cdf52237264663-1"><span class="crayon-v">Dataset</span><span class="crayon-o">&lt;</span><span class="crayon-v">Person</span><span class="crayon-o">&gt;</span><span class="crayon-h"> </span><span class="crayon-v">ds</span><span class="crayon-h"> </span><span class="crayon-o">=</span><span class="crayon-h"> </span><span class="crayon-v">spark</span><span class="crayon-sy">.</span><span class="crayon-e">read</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf52237264663-2"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">option</span><span class="crayon-sy">(</span><span class="crayon-s">"header"</span><span class="crayon-sy">,</span><span class="crayon-h"> </span><span class="crayon-s">"true"</span><span class="crayon-sy">)</span></div><div class="crayon-line" id="crayon-5973b048cdf52237264663-3"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-e">csv</span><span class="crayon-sy">(</span><span class="crayon-s">"testdata/people.csv"</span><span class="crayon-sy">)</span></div><div class="crayon-line crayon-striped-line" id="crayon-5973b048cdf52237264663-4"><span class="crayon-h">&nbsp;&nbsp;</span><span class="crayon-sy">.</span><span class="crayon-st">as</span><span class="crayon-sy">(</span><span class="crayon-v">Encoders</span><span class="crayon-sy">.</span><span class="crayon-e">bean</span><span class="crayon-sy">(</span><span class="crayon-v">Person</span><span class="crayon-sy">.</span><span class="crayon-t">class</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span><span class="crayon-sy">;</span></div></div></td>
					</tr>
				</table>
			</div>
		</div>

<p></p>
<h2>Spark+Scala beats Spark+Java</h2>
<p>Using Apache Spark with Java is harder than using Apache Spark with Scala and we spent significantly longer upgrading our Java examples than we did with our Scala examples, including running into some confusing runtime errors that were hard to track down (for example, we hit a runtime error with Spark’s code generation because one of our Java classes was not declared as public).</p>
<p>Also, we weren’t always able to use concise lambda functions even though we are using Java 8, and had to revert to anonymous inner classes with verbose (and confusing) syntax.</p>
<h2>Conclusion</h2>
<p>Spark 2.0 represents a significant milestone in the evolution of this open source project and provides cleaner APIs and improved performance compared to the 1.6 release.</p>
<p>The <a href="https://spark.apache.org/docs/2.0.0-preview/api/scala/index.html#org.apache.spark.package" target="_blank">Scala API</a> is a joy to code with, but the <a href="https://spark.apache.org/docs/2.0.0-preview/api/java/index.html">Java API</a> can often be frustrating. It’s worth biting the bullet and switching to Scala.</p>
<p>Full source code for a number of examples is available from our github repo <a href="https://github.com/agildata/apache-spark-examples" target="_blank">here</a>.</p>
