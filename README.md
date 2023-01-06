# OpenSearch Index Shard Size
An index is composed of shards. There are two types of shards primary and replica shards. Each document in an index belongs to one primary shard. A replica shard is a copy of a primary shard. Each primary shard belongs to 1 data node.

When an index is created in Amazon OpenSearch service it defaults to 5 primary shards and 1 replica (total of 10 shards). This behavior differs from open source OpenSearch, which defaults to 1 primary and 1 replica shard (total of 2 shards). 

The number of primary shards is configured when an index is created. The number of primary shards can not be easily adjusted after an index is created. To adjust the number of primary shards you must create a new index with the desired number of shards and use the reindex API to copy data from the old index to the new.

The number of replicate shards for an existing index can be adjusted using the index setting API. 

# Adjust # of Primary Shards for an Existing Index 

# Adjust # of Replicate Shards for an Existing Index
