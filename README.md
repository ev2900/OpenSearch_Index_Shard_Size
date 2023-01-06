# OpenSearch Index Shard Size
An index is composed of shards. There are two types of shards primary and replica shards. Each document in an index belongs to one primary shard and a replica shard is a copy of a primary shard. 

When an index is created in Amazon OpenSearch service it defaults to 5 primary shards and 1 replica (total of 10 shards). This behavior differs from open source OpenSearch, which defaults to 1 primary and 1 replica shard (total of 2 shards).

