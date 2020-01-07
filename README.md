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

