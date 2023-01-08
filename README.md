# OpenSearch Index Shard Size
An index is composed of shards. There are two types of shards primary and replica shards. Each document in an index belongs to one primary shard. A replica shard is a copy of a primary shard. Each primary shard belongs to 1 data node.

When an index is created in Amazon OpenSearch service it defaults to 5 primary shards and 1 replica (total of 10 shards). This behavior differs from open source OpenSearch, which defaults to 1 primary and 1 replica shard (total of 2 shards). 

The number of primary shards is configured when an index is created. The number of primary shards cannot be easily adjusted after an index is created. To adjust the number of primary shards you must create a new index with the desired number of shards and use the reindex API to copy data from the old index to the new.

The number of replicate shards for an index can be easily adjusted using the index setting API. 

OpenSearch has an ideal shard size of 10 - 50 GB per shard is recommended. 10 - 30 GB per shard is prefered for application search / read heavy work loads. 30 - 50 GB is preferred for write heavy workloads such as log analytics. 

Having too many small shards can be an issue. Meta-data for a shard is stored in the JVM heap memory. If you have to many small shards you can exhaust memory storing meta-data unnecessarily.

In the example below. I will illustrate a small an index that has too many shards allocated to it. I will demonstrate how to reduce both the number of primary and replica shards.

## Adjust # of Primary Shards for an Existing Index

Running ```GET _cat/indices/?v``` 

<img width="800" alt="cat_indicies_1" src="https://github.com/ev2900/OpenSearch_Index_Shard_Size/blob/main/README/cat_indicies_1.png">

The index *sample-data-5-1* has a total size less than 1 MB. Yet the index has the Amazon OpenSeach Service default of 5 primary shards and 1 replicate (total of 10 shards). Given the ideal shard size of 10 - 50 GB per shard the shards for this index are probably too small

Running ```GET _cat/shards/sample-data-5-1?v```



## Adjust # of Replicate Shards for an Existing Index
