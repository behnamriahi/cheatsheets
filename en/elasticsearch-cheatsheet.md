# 🔍 Elasticsearch Cheatsheet

## 🔌 Connection and Basics

```bash
# Check cluster health
curl -X GET "localhost:9200/_cluster/health?pretty"

# Get cluster info
curl -X GET "localhost:9200"

# List all nodes
curl -X GET "localhost:9200/_cat/nodes?v"

# List all indices
curl -X GET "localhost:9200/_cat/indices?v"

# Check cluster stats
curl -X GET "localhost:9200/_cluster/stats?pretty"
```

---

## 📇 Index Management

```bash
# Create index
curl -X PUT "localhost:9200/my_index?pretty"

# Create index with settings
curl -X PUT "localhost:9200/my_index?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 2
  }
}'

# Delete index
curl -X DELETE "localhost:9200/my_index?pretty"

# Get index settings
curl -X GET "localhost:9200/my_index/_settings?pretty"

# Update index settings
curl -X PUT "localhost:9200/my_index/_settings?pretty" -H 'Content-Type: application/json' -d'
{
  "index": {
    "number_of_replicas": 1
  }
}'

# Close index
curl -X POST "localhost:9200/my_index/_close?pretty"

# Open index
curl -X POST "localhost:9200/my_index/_open?pretty"

# Check if index exists
curl -I "localhost:9200/my_index"
```

---

## 🗺️ Mapping

```bash
# Define mapping
curl -X PUT "localhost:9200/my_index?pretty" -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "author": { "type": "keyword" },
      "publish_date": { "type": "date" },
      "pages": { "type": "integer" },
      "price": { "type": "float" }
    }
  }
}'

# Get mapping
curl -X GET "localhost:9200/my_index/_mapping?pretty"

# Add field to existing mapping
curl -X PUT "localhost:9200/my_index/_mapping?pretty" -H 'Content-Type: application/json' -d'
{
  "properties": {
    "isbn": { "type": "keyword" }
  }
}'
```

---

## 📝 Document Operations

```bash
# Index document (auto-generate ID)
curl -X POST "localhost:9200/my_index/_doc?pretty" -H 'Content-Type: application/json' -d'
{
  "title": "Elasticsearch Guide",
  "author": "John Doe",
  "pages": 350
}'

# Index document (specific ID)
curl -X PUT "localhost:9200/my_index/_doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "title": "Elasticsearch Guide",
  "author": "John Doe",
  "pages": 350
}'

# Get document
curl -X GET "localhost:9200/my_index/_doc/1?pretty"

# Update document
curl -X POST "localhost:9200/my_index/_update/1?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": {
    "pages": 375
  }
}'

# Delete document
curl -X DELETE "localhost:9200/my_index/_doc/1?pretty"

# Bulk operations
curl -X POST "localhost:9200/_bulk?pretty" -H 'Content-Type: application/json' -d'
{ "index": { "_index": "my_index", "_id": "1" }}
{ "title": "Book 1", "author": "Author 1" }
{ "index": { "_index": "my_index", "_id": "2" }}
{ "title": "Book 2", "author": "Author 2" }
{ "delete": { "_index": "my_index", "_id": "3" }}
'
```

---

## 🔍 Search Operations

```bash
# Search all documents
curl -X GET "localhost:9200/my_index/_search?pretty"

# Match query
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "title": "elasticsearch"
    }
  }
}'

# Match all
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_all": {}
  }
}'

# Term query (exact match)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "term": {
      "author.keyword": "John Doe"
    }
  }
}'

# Range query
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "range": {
      "pages": {
        "gte": 200,
        "lte": 500
      }
    }
  }
}'

# Bool query (must, should, must_not)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": [
        { "match": { "title": "elasticsearch" }}
      ],
      "filter": [
        { "range": { "pages": { "gte": 100 }}}
      ],
      "must_not": [
        { "term": { "author.keyword": "Unknown" }}
      ]
    }
  }
}'

# Wildcard query
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "wildcard": {
      "title": "*guide*"
    }
  }
}'

# Fuzzy query (typo tolerance)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "fuzzy": {
      "title": {
        "value": "elasticseerch",
        "fuzziness": "AUTO"
      }
    }
  }
}'
```

---

## 🎯 Filtering and Sorting

```bash
# Sort results
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "pages": { "order": "desc" }}
  ]
}'

# Pagination
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 20
}'

# Source filtering (select fields)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "_source": ["title", "author"]
}'
```

---

## 📊 Aggregations

```bash
# Terms aggregation (group by)
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "authors": {
      "terms": {
        "field": "author.keyword",
        "size": 10
      }
    }
  }
}'

# Stats aggregation
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "pages_stats": {
      "stats": {
        "field": "pages"
      }
    }
  }
}'

# Date histogram
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "by_month": {
      "date_histogram": {
        "field": "publish_date",
        "calendar_interval": "month"
      }
    }
  }
}'

# Nested aggregations
curl -X GET "localhost:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "authors": {
      "terms": {
        "field": "author.keyword"
      },
      "aggs": {
        "avg_pages": {
          "avg": {
            "field": "pages"
          }
        }
      }
    }
  }
}'
```

---

## 🔧 Cluster Management

```bash
# Get cluster settings
curl -X GET "localhost:9200/_cluster/settings?pretty"

# Update cluster settings
curl -X PUT "localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.enable": "all"
  }
}'

# Get node info
curl -X GET "localhost:9200/_nodes?pretty"

# Get node stats
curl -X GET "localhost:9200/_nodes/stats?pretty"

# Reroute shards
curl -X POST "localhost:9200/_cluster/reroute?pretty"

# Get shard allocation
curl -X GET "localhost:9200/_cat/shards?v"

# Explain allocation
curl -X GET "localhost:9200/_cluster/allocation/explain?pretty"
```

---

## 📊 Monitoring

```bash
# Index stats
curl -X GET "localhost:9200/my_index/_stats?pretty"

# Segments info
curl -X GET "localhost:9200/my_index/_segments?pretty"

# Recovery info
curl -X GET "localhost:9200/_cat/recovery?v"

# Thread pool stats
curl -X GET "localhost:9200/_cat/thread_pool?v"

# Pending tasks
curl -X GET "localhost:9200/_cat/pending_tasks?v"

# Hot threads
curl -X GET "localhost:9200/_nodes/hot_threads"
```

---

## 🔄 Snapshot and Restore

```bash
# Register snapshot repository
curl -X PUT "localhost:9200/_snapshot/my_backup?pretty" -H 'Content-Type: application/json' -d'
{
  "type": "fs",
  "settings": {
    "location": "/mount/backups/my_backup"
  }
}'

# Create snapshot
curl -X PUT "localhost:9200/_snapshot/my_backup/snapshot_1?wait_for_completion=true&pretty"

# List snapshots
curl -X GET "localhost:9200/_snapshot/my_backup/_all?pretty"

# Restore snapshot
curl -X POST "localhost:9200/_snapshot/my_backup/snapshot_1/_restore?pretty"

# Delete snapshot
curl -X DELETE "localhost:9200/_snapshot/my_backup/snapshot_1?pretty"
```

---

## 🎨 Analyzers and Tokenizers

```bash
# Test analyzer
curl -X POST "localhost:9200/_analyze?pretty" -H 'Content-Type: application/json' -d'
{
  "analyzer": "standard",
  "text": "The Quick Brown Fox"
}'

# Custom analyzer
curl -X PUT "localhost:9200/my_index?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "stop"]
        }
      }
    }
  }
}'
```

---

## 🔐 Security (X-Pack)

```bash
# Create user
curl -X POST "localhost:9200/_security/user/johndoe?pretty" -u elastic:password -H 'Content-Type: application/json' -d'
{
  "password": "userpassword",
  "roles": ["kibana_admin", "monitoring_user"],
  "full_name": "John Doe"
}'

# Create role
curl -X POST "localhost:9200/_security/role/my_role?pretty" -u elastic:password -H 'Content-Type: application/json' -d'
{
  "cluster": ["all"],
  "indices": [
    {
      "names": ["my_index"],
      "privileges": ["read", "write"]
    }
  ]
}'

# Get users
curl -X GET "localhost:9200/_security/user?pretty" -u elastic:password
```

---

## 🛠️ Index Templates

```bash
# Create index template
curl -X PUT "localhost:9200/_index_template/my_template?pretty" -H 'Content-Type: application/json' -d'
{
  "index_patterns": ["logs-*"],
  "template": {
    "settings": {
      "number_of_shards": 1
    },
    "mappings": {
      "properties": {
        "timestamp": { "type": "date" },
        "message": { "type": "text" }
      }
    }
  }
}'

# List templates
curl -X GET "localhost:9200/_index_template?pretty"

# Delete template
curl -X DELETE "localhost:9200/_index_template/my_template?pretty"
```

---

## 💡 Best Practices

- Use appropriate shard counts (5-10 GB per shard is optimal)
- Enable slow query logging for performance tuning
- Use bulk API for indexing multiple documents
- Use filter context instead of query context when possible
- Set proper mapping types to optimize storage and search
- Monitor cluster health and node stats regularly
- Use index aliases for zero-downtime reindexing
- Implement proper backup and snapshot strategies
- Use appropriate refresh intervals (default is 1s)
- Optimize field mappings (disable fields you don't need)
