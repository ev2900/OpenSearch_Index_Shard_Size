# OpenSearch Index Shard Size

<img width="85" alt="map-user" src="https://img.shields.io/badge/views-965-green"> <img width="125" alt="map-user" src="https://img.shields.io/badge/unique visits-550-green">

If you prefer to watch a video instead of reading the instructions below, check out the link below

YouTube video - https://youtu.be/xadv93LlbY4

Blog post - https://opensearch.org/blog/optimize-opensearch-index-shard-size/

## Overview

An index is composed of shards. There are two types of shards primary and replica shards. Each document in an index belongs to one primary shard. A replica shard is a copy of a primary shard. Each primary shard belongs to 1 data node.

When an index is created in Amazon OpenSearch service it defaults to 5 primary shards and 1 replica (total of 10 shards). This behavior differs from open source OpenSearch, which defaults to 1 primary and 1 replica shard (total of 2 shards).

The number of primary shards is configured when an index is created. The number of primary shards cannot be easily adjusted after an index is created. To adjust the number of primary shards you must create a new index with the desired number of shards and use the reindex API to copy data from the old index to the new.

The number of replicate shards for an index can be easily adjusted using the index setting API.

OpenSearch has an ideal shard size of 10 - 50 GB per shard is recommended. 10 - 30 GB per shard is prefered for application search / read heavy work loads. 30 - 50 GB is preferred for write heavy workloads such as log analytics.

Having too many small shards can be an issue. Meta-data for a shard is stored in the JVM heap memory. If you have to many small shards you can exhaust memory storing meta-data unnecessarily.

In the example below. I will illustrate a small an index that has too many shards allocated to it. I will demonstrate how to reduce both the number of primary and replica shards.

## View the # Shards, Size ... for an Existing Index

Running ```GET _cat/indices/?v```

<img width="800" alt="cat_indicies_1" src="https://github.com/ev2900/OpenSearch_Index_Shard_Size/blob/main/README/cat_indicies_1.png">

The index *sample-data-5-1* has a total size less than 1 MB. Yet the index has the Amazon OpenSeach Service default of 5 primary shards and 1 replicate (total of 10 shards). Given the ideal shard size of 10 - 50 GB per shard the shards for this index are probably too small

Running ```GET _cat/shards/sample-data-5-1?v```

<img width="800" alt="cat_indicies_1" src="https://github.com/ev2900/OpenSearch_Index_Shard_Size/blob/main/README/cat_shards_1.png">

The size of the shards that are part of the index *sample-data-5-1* is clear. The size of each shard is small. Let's reduce the number of shards for the *sample-data-5-1* index from 5 to 1

## Adjust # of Primary Shards for an Existing Index

The number of primary shards is configured when an index is created. To adjust the number of shards we need to create a new index with the desired number of shards and use the reindex API to copy data from the old index to the new.

To create the new index with the desired number of shards

```
PUT sample-data-1-1
{
  "settings": {
    "index": {
      "number_of_shards": 1,
      "number_of_replicas": 1
    }
  }
}
```

To copy data from the old index (*sample-data-5-1*) to the new index (*sample-data-1-1*)

```
POST _reindex
{
  "source": {
    "index": "sample-data-5-1"
  },
  "dest": {
    "index": "sample-data-1-1"
  }
}
```

*Note* before reindexing or copying data from the old index to the new index you may consider setting the old index to read only. This will ensure that no new data is written to the old index during the copy. For information on how to set an index to read only reference [OpenSearch_Read_Only_Index](https://github.com/ev2900/OpenSearch_Read_Only_Index)

We can confirm the *sample-data-1-1* has fewer shards by running ```GET _cat/shards/sample-data-1-1?v```

<img width="800" alt="cat_indicies_1" src="https://github.com/ev2900/OpenSearch_Index_Shard_Size/blob/main/README/cat_shards_2.png">

If desired the old index (*sample-data-5-1*) can be deleted via. ```DELETE sample-data-5-1```

## Adjust # of Replicate Shards for an Existing Index

The number of replicate shards for an index can be easily adjusted using the index setting API. Adjusting the replicate shards does NOT require creating a new index and copying data.

Running

```
PUT sample-data-1-1/_settings
{
  "index" : {
    "number_of_replicas": 0
  }
}
```

will reduce the number replicate shards from 1 to 0. Running ```GET _cat/shards/sample-data-1-1?v``` can confirm

<img width="800" alt="cat_indicies_1" src="https://github.com/ev2900/OpenSearch_Index_Shard_Size/blob/main/README/cat_shards_3.png">
