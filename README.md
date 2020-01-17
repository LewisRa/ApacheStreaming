

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

## updateStateByKey() - Summarizing Stream Data

Example: Stream of integers --> 473890174

In a Dstream, you group integers into batches, which all integers which have arrived within a certain time interval form one RDD. The interval of time is called **batch interval** and a property of every Dstream.

(4)  (738) (901) (643) **The data arrived in 4 batches<br>
RDD4  RDD3  RDD2  RDD1**

If you apply the **stateless** transformation `reduceByKey()` The output would be **14 18 10 13** which is basically a new dstream where individual RDDs in the result is the sum of individual RDDs original stream. (Not commonly used)

What you usually need is a running sum. For this, we need the **stateful** tranformation `updateStateByKey()`:

You can use the`updateStateByKey()` function provided that there are all pair RDDs in your Dstream because pair RDDs have special method, which takes in a update function and updates the global state. This global state can be used to summarize formations across all RDDs in a Dstream. **The individual RDDs have to pair though** To achieve this, we use the tuple as key/valeue pairs in python and in Java, pair RDDs are represented by a separate class derive from the RDDs class.

Explicitly the `updateStateByKey()` works like this: 
- it initalize the global state to some value
- specify an update function which updates the current state based on the values in the stream
- the update function is apply to all existing keys

All methods which apply to regular RDDs along with single entitiy apply to pair RDDS along with the methods `keys()` and `values()`

Going back to the previous example of strem of integers, assign the same key ("K" for example) to each integer to make every element making a each elemen a key-value pair. Thus, making every RDD a pair RDD.

(4)  (738) (901) (643) <br>
K     KKK   KKK   KKK

Now `updateStateByKey()` will sum all values with the same key ("K")<br>
**(4)  (738) (901) (643)--->45 <br>
K     KKK   KKK   KKK**

## CountByWindow() - Summarizing Stream Data by Window.

  Tranformations on streams with a specified interval is accomplishedusing window operations. Consider a stream of logs for a website. We want the cumulative error rate over a 30 seconf timeframe. The **batch interval** is fixed at 10s but we are interested in data over 30 seconds. We will define what is called a **sliding window**. The size of th sliding window is how many RDDs it includes. 
  
  Sliding windows are larger than batch intervals!
  
  A sliding window includes multiple RDDs within it and it moves over the RDDs in a well defined way including differenr RDDs can different times.
  
  **Sliding intervals** determines how many RDDs leave the sliding windows and how mnay Rdds enter the sliding window for every point in time, In this example, pur sliding interval is 20 seconds which means for every slide of the window 2 RDDs leave and 2 RDDs enter the sliding window.
  
The `CountByWindow` method allows you to count the number of messges within a certain window of time. The result of the `CountByWindow ` operation is another Dstream where every RDD in the Dstream containes the summary of individual RDDs within the sliding window. 
  
In summary, the batch interval determines which messages get grouped into a single RDD. The window size determine hoe far back in time you want to go in order to perform summary operation and rher slidiinf interval determines how the windown will move at every time instance.

## Summary and Interval Functions
 Windowing Operationa can get pretty expensice especially if the window size is large.
 
 To optimize summary calculation: Use inverse functions. 
 
 Example: you wnat to count of error messges within a cerain window within window, you wnat to count the number of messges in red. 
 The summary fuction you can peform is the sum() operation. 
 
 **Sliding interval**
 Initial count = 9 errors in red
 2 RDDs enter the window: 9+2=11
 2 RDDs leave the window to compensate for the 2 RDDs that entered
 Since 2 RDDs left window, subtract thr number of errors ing red: 11-6 = 5 total errors in red currently.
 
 2 RDDs entered the window ---> summary function
 2 RDDs left the window ---> inverse function
 In the same way, if summary functions is muliplication, the inverse is some kind of division
 
 **Specifying summary function and inverse functions essentially make windowing more efficent** 
 
 ## Building a Robust Spark Streaming Applications 
 A Robust Application has the ability to recover from failures (fault tolerance). 
 
 Spark is a distributed computing engine. It runs on a cluster of machines. So it has a lot of robustness built into its distributed computing. Any data stored in Spark will be replicated across multiple data noses in the cluster so data can't be lost easily. 
 
The processes that we run in parallel on multiple machines in this cluster also tendd to be robust. IF a process fails on a particular machine the process is restarted on another machine. 

**Once you get Spark to work with streaming data, streaming applications also require additional feture to protect against data loss**

Ephermal = streaming data. This streaming applications that run on spark requie checkpointing. Checkpointing invovles periodically saving the data that you recieve in the form of stremas to a reliable storage system. YoU just don't store source data. All computations and transformations that are performed on streams and the intermediate statitics are also stored at the check point.
**Advantage**
- Limiting state recomputation in case of failure
- Fault tolerance for the driver job

Driver programs in spark spin off the distributed computing tasks on your cluster. 
---
### Spark streaming = Directed Acyclic Graph 
 
 A spark program which you submit to the spark cluster is broken into discrete tasks that can run in parallel on data.Every task has a RDD associated with it and that task thatworks on the RDD.
 
 The RDD are partitionsed acroess machines in your cluster, ThE task and it asoccited RDD depend on other tasks. Thus the tasks and RDDs form a DAG. 
 
 Thus, an RDD can always be reconstructed using its lineage. The RDD kept track of it lineage such that if there are if nodes crash and data is lost, an RDD know how to recontruct itselfbased on its lineage.
 
 If you checkpoint data, it reduces(how much lineage) how much statehas to be recomputed. 
 
 **Checkpointing stores intermediate versions of RDDs in storage such as HDFS or Amazon S3** This reduced 50-100 transformation to 4 to 5. 



