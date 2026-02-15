# 🔴 Redis Cheatsheet

## 🔌 Connection

```bash
redis-cli                              # Connect to local Redis
redis-cli -h hostname -p port          # Connect to remote Redis
redis-cli -a password                  # Connect with password
redis-cli --raw                        # Show output in raw format
redis-cli -n database_number           # Connect to specific database
redis-cli --eval script.lua            # Execute Lua script
```

---

## 🔑 Key Operations

```bash
SET key value                          # Set key to value
GET key                                # Get value of key
DEL key                                # Delete key
EXISTS key                             # Check if key exists
KEYS pattern                           # Find keys matching pattern
KEYS *                                 # List all keys (avoid in production)
SCAN cursor                            # Iterate keys incrementally
TYPE key                               # Get type of key
RENAME oldkey newkey                   # Rename key
RENAMENX oldkey newkey                 # Rename if new key doesn't exist
```

---

## ⏱️ Expiration

```bash
EXPIRE key seconds                     # Set expiration time in seconds
EXPIREAT key timestamp                 # Set expiration as Unix timestamp
TTL key                                # Get remaining time to live in seconds
PTTL key                               # Get TTL in milliseconds
PERSIST key                            # Remove expiration from key
SETEX key seconds value                # Set key with expiration
PSETEX key milliseconds value          # Set key with expiration (ms)
```

---

## 📝 String Operations

```bash
SET key value                          # Set string value
GET key                                # Get string value
MSET key1 val1 key2 val2              # Set multiple keys
MGET key1 key2                         # Get multiple keys
APPEND key value                       # Append to string
STRLEN key                             # Get string length
GETRANGE key start end                 # Get substring
SETRANGE key offset value              # Overwrite part of string
INCR key                               # Increment by 1
DECR key                               # Decrement by 1
INCRBY key amount                      # Increment by amount
DECRBY key amount                      # Decrement by amount
INCRBYFLOAT key amount                 # Increment by float
SETNX key value                        # Set if not exists
GETSET key value                       # Set and return old value
```

---

## 📋 List Operations

```bash
LPUSH key value                        # Push to left (head)
RPUSH key value                        # Push to right (tail)
LPOP key                               # Pop from left
RPOP key                               # Pop from right
LRANGE key start stop                  # Get range of elements
LRANGE key 0 -1                        # Get all elements
LLEN key                               # Get list length
LINDEX key index                       # Get element at index
LSET key index value                   # Set element at index
LINSERT key BEFORE|AFTER pivot value   # Insert before/after element
LREM key count value                   # Remove elements
LTRIM key start stop                   # Trim list to range
BLPOP key timeout                      # Blocking left pop
BRPOP key timeout                      # Blocking right pop
RPOPLPUSH source dest                  # Pop from source, push to dest
```

---

## 🗂️ Set Operations

```bash
SADD key member                        # Add member to set
SREM key member                        # Remove member from set
SMEMBERS key                           # Get all members
SISMEMBER key member                   # Check if member exists
SCARD key                              # Get set size
SPOP key                               # Remove and return random member
SRANDMEMBER key [count]                # Get random member(s)
SMOVE source dest member               # Move member between sets

# Set operations
SUNION key1 key2                       # Union of sets
SINTER key1 key2                       # Intersection of sets
SDIFF key1 key2                        # Difference of sets
SUNIONSTORE dest key1 key2             # Store union result
SINTERSTORE dest key1 key2             # Store intersection result
SDIFFSTORE dest key1 key2              # Store difference result
```

---

## 📊 Sorted Set Operations

```bash
ZADD key score member                  # Add member with score
ZREM key member                        # Remove member
ZSCORE key member                      # Get score of member
ZINCRBY key increment member           # Increment score
ZCARD key                              # Get set size
ZCOUNT key min max                     # Count members in score range
ZRANK key member                       # Get rank (ascending)
ZREVRANK key member                    # Get rank (descending)
ZRANGE key start stop [WITHSCORES]     # Get range (ascending)
ZREVRANGE key start stop [WITHSCORES]  # Get range (descending)
ZRANGEBYSCORE key min max              # Get range by score
ZREVRANGEBYSCORE key max min           # Get range by score (desc)
ZREMRANGEBYRANK key start stop         # Remove range by rank
ZREMRANGEBYSCORE key min max           # Remove range by score
```

---

## 🗺️ Hash Operations

```bash
HSET key field value                   # Set hash field
HGET key field                         # Get hash field
HMSET key field1 val1 field2 val2      # Set multiple fields
HMGET key field1 field2                # Get multiple fields
HGETALL key                            # Get all fields and values
HDEL key field                         # Delete field
HEXISTS key field                      # Check if field exists
HKEYS key                              # Get all field names
HVALS key                              # Get all values
HLEN key                               # Get number of fields
HINCRBY key field increment            # Increment field value
HINCRBYFLOAT key field increment       # Increment by float
HSETNX key field value                 # Set if field doesn't exist
```

---

## 🔄 Transaction Operations

```bash
MULTI                                  # Start transaction
EXEC                                   # Execute transaction
DISCARD                                # Cancel transaction
WATCH key                              # Watch key for changes
UNWATCH                                # Cancel watch

# Example
MULTI
SET key1 value1
SET key2 value2
EXEC
```

---

## 📡 Pub/Sub Operations

```bash
PUBLISH channel message                # Publish message to channel
SUBSCRIBE channel                      # Subscribe to channel
UNSUBSCRIBE channel                    # Unsubscribe from channel
PSUBSCRIBE pattern                     # Subscribe to pattern
PUNSUBSCRIBE pattern                   # Unsubscribe from pattern
PUBSUB CHANNELS [pattern]              # List active channels
PUBSUB NUMSUB [channel]                # Number of subscribers
PUBSUB NUMPAT                          # Number of pattern subscriptions
```

---

## 💾 Persistence

```bash
SAVE                                   # Synchronous save (blocks)
BGSAVE                                 # Background save
LASTSAVE                               # Last save timestamp
BGREWRITEAOF                           # Rewrite AOF file
```

---

## 🗄️ Database Operations

```bash
SELECT database_number                 # Select database (0-15)
FLUSHDB                                # Clear current database
FLUSHALL                               # Clear all databases
DBSIZE                                 # Get number of keys
SWAPDB db1 db2                         # Swap databases
```

---

## 📊 Server Management

```bash
INFO                                   # Server information
INFO section                           # Specific section info
MONITOR                                # Monitor commands in real-time
CLIENT LIST                            # List connected clients
CLIENT KILL ip:port                    # Kill client connection
CONFIG GET parameter                   # Get configuration
CONFIG SET parameter value             # Set configuration
CONFIG REWRITE                         # Rewrite config file
SLOWLOG GET [count]                    # Get slow queries
SLOWLOG LEN                            # Get slow log length
SLOWLOG RESET                          # Clear slow log
TIME                                   # Get server time
PING                                   # Ping server
ECHO message                           # Echo message
QUIT                                   # Close connection
SHUTDOWN [SAVE|NOSAVE]                 # Shutdown server
```

---

## 🔐 Security

```bash
AUTH password                          # Authenticate with password
ACL LIST                               # List ACL rules
ACL USERS                              # List users
ACL SETUSER username ...               # Create/modify user
ACL DELUSER username                   # Delete user
ACL WHOAMI                             # Get current user
```

---

## 🔄 Replication

```bash
REPLICAOF host port                    # Set master
REPLICAOF NO ONE                       # Promote to master
ROLE                                   # Get replication role
INFO replication                       # Replication info
```

---

## 💡 Advanced Features

```bash
# Bit operations
SETBIT key offset value                # Set bit
GETBIT key offset                      # Get bit
BITCOUNT key                           # Count set bits
BITOP operation destkey key [key ...]  # Bitwise operations

# HyperLogLog
PFADD key element                      # Add to HyperLogLog
PFCOUNT key                            # Get cardinality estimate
PFMERGE destkey sourcekey [sourcekey ...]  # Merge HyperLogLogs

# Geospatial
GEOADD key longitude latitude member   # Add geo point
GEODIST key member1 member2 [unit]     # Get distance
GEORADIUS key long lat radius unit     # Query by radius
GEOPOS key member                      # Get coordinates

# Streams
XADD stream * field value              # Add to stream
XREAD COUNT count STREAMS stream id    # Read from stream
XLEN stream                            # Get stream length
```

---

## 🛠️ CLI Tips

```bash
redis-cli --scan --pattern 'user:*'    # Scan with pattern
redis-cli --bigkeys                    # Find big keys
redis-cli --latency                    # Check latency
redis-cli --stat                       # Show statistics
redis-cli --rdb dump.rdb               # Dump RDB file
redis-cli --pipe                       # Pipe mode for mass insert
redis-cli SHUTDOWN SAVE                # Graceful shutdown
redis-cli --no-raw                     # Disable raw output
```

---

## 💡 Best Practices

- Use `SCAN` instead of `KEYS` in production
- Set appropriate expiration times to prevent memory issues
- Use pipelining for batch operations
- Monitor memory usage regularly
- Use appropriate data structures for your use case
- Enable persistence (RDB/AOF) for data durability
- Use Redis Sentinel or Cluster for high availability
- Avoid large values (keep values under 1MB)
- Use connection pooling in applications
- Regular backup of RDB/AOF files
