# ApacheStreaming

## Stateless vs Stateful transformations 

### Stateless
- Stateless transformations are transformations applied on individual RDDs and have no memory of RDDs which passed before before them i.e. the transformations are applied on a single RDD
- Typically used in batch processing from a file. All data avaiable in single RDD and the Rdd can be as big as you want (millions of records) bit it is still a single RDD
- `map()`, `reduceByKey()`, `filter()`  are all stateless transformations that can be applied to a single RDD

### Stateful 
- Stateful transformation accumulate or summarize information across multiple
- Typically stateful transformation are applied to streaming data(stateless can also be apply to streaming data like `map()`, `reduceByKey()`, `filter()` in the **firstexample** but in most situations, it does not make much sense. 
- Accumulating information across all entities in stream or all entities in a particular window of time. 
