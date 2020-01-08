##Google:   
- current storage = 15 exabytes
- Processed per day = 100 petabytes
- number of pages indexed = 60 trillion
- unique seatch users per month > 1 billion
- searches by seconds = 2.3 million
  

# ApacheStreaming
Built a fault tolerant twiiter projects that live streams and tracks hashtags 
## Spark
- Spark can work as a replacement for MapReduce because it is a general processing framework fro distrubuted systems. (You can get Spark to work with YARN as it cluster manager)

- Spark offers a variety of enviroments in which you can function it has a **REPL** OR INTERACTIVE environment (REPL= read,evaluate, print loop) which you can quickly type in commands and see output quickly. Great for prototyping in python ot scala before production.
1. Spark SQL
2. MLLIB -machine learning
2. GraphX- deals with data best represented by graphs like social networks 
4. Spark Streaming

## Stream Processing with Spark Streaming 
Spark Streaming allows you to view streams as entities within Discretized Streams(**Dstreams**). Dstreams are made up of RDDs(**Resilient Distrubuted Streams**) which form fundamental programming abstraction in Spark. Abstractions allow a data stream to be seen just a sequence of batch data
## Stateless vs Stateful transformations 

### Stateless
- Stateless transformations are transformations applied on individual RDDs and have no memory of RDDs which passed before before them i.e. the transformations are applied on a single RDD
- Typically used in batch processing from a file. All data avaiable in single RDD and the Rdd can be as big as you want (millions of records) bit it is still a single RDD
- `map()`, `reduceByKey()`, `filter()`  are all stateless transformations that can be applied to a single RDD

### Stateful 
- Stateful transformation accumulate or summarize information across multiple
- Typically stateful transformation are applied to streaming data(stateless can also be apply to streaming data like `map()`, `reduceByKey()`, `filter()` in the **firstexample** but in most situations, it does not make much sense. 
- Accumulating information across all entities in stream or all entities in a particular window of time. 

## updateStateByKey() - Summarizing Strem Data

Example: Stream of integers --> 473890174

In a Dstream, you group integers into batches, which all integers which have arrived within a certain time interval form one RDD. The interval of time is called **batch interval** and a property of every Dstream.

(4)  (738) (901) (643) **The data arrived in 4 batches
RDD4  RDD3  RDD2  RDD1**

If you apply the **stateless** transformation `reduceByKey()` The output would be **14 18 10 13** which is basically a new dstream where individual RDDs in the result is the sum of individual RDDs original stream. (Not commonly used)

What you usually need is a running sum. For this, we need the **stateful** tranformation `updateStateByKey()`:

You can use the`updateStateByKey()` function provided that there are all pair RDDs in your Dstream because pair RDDs have special method, which takes in a update function and updates the global state. This global state can be used to summarize formations across all RDDs in a Dstream. **The individual RDDs have to pair though** To achieve this, we use the tuple as key/valeue pairs in python and in Java, pair RDDs are represented by a separate class derive from the RDDs class.

Explicitly the `updateStateByKey()` works like this: 
- it initalize the global state to some value
- specify an update function which updates the current state based on the values in the stream
- the update function is apply to all existing keys

All methods which apply to regular RDDs along with single entitiy apply to pair RDDS along with the methods `keys()` and `values()`

Going back to the previous example of strem of integers, assign the same key ("K" for example) ti each integer to make every element making a each elemen a key-value pair. Thus, making every RDD a pair RDD.

(4)  (738) (901) (643)
K     KKK   KKK   KKK

Now `updateStateByKey()` will sum all values with the same key ("K")
**(4)  (738) (901) (643)--->45
K     KKK   KKK   KKK**





