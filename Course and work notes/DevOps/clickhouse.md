```python
CLICKHOUSE_HOST = 'clickhouse' 
CLICKHOUSE_PORT = 9000 
CLICKHOUSE_DB = 'tts' 
CLICKHOUSE_USER = 'default'

-------

query = f""" INSERT INTO tts_metadata (text_input, timestamp, wav_data) VALUES (%(text_input)s, %(timestamp)s, %(wav_data)s) """

```

- **CLICKHOUSE_HOST**: The host is now set to `'clickhouse'`, which is the **name of the ==service== as defined in your Docker Compose configuration**. This ensures that your Python app (if it's in the same Docker Compose network) will be able to reach the ClickHouse service using this hostname.
    
- **CLICKHOUSE_PORT**: We use port `9000` for the ==ClickHouse TCP connection==, which is the port mapped to ClickHouse’s query interface.
    
- **CLICKHOUSE_DB**: The database is set to `tts` as defined in your Docker Compose configuration. Make sure the table `tts_metadata` is created in this database.
    
- **CLICKHOUSE_USER**: The user is `default` according to your Docker Compose setup.
    
- **CLICKHOUSE_PASSWORD**: Set to an empty string `''` because no password is defined in your environment settings.

### **Testing Your Code**:

Now that you have updated your Python code, you can test it in the following steps:

1. **Start ClickHouse Service**: Make sure the ClickHouse service is running using `docker-compose up`. Ensure no errors occur and the ClickHouse container is healthy.
    
2. **Create the Table in ClickHouse**: Before running your Python code, you need to ==ensure the `tts_metadata` table exists in your `tts` database.(because query in python code is "insert into tts_metadata)== You can run the following SQL in ClickHouse:

```bash
1. docker exec -it <clickhouse-container-name> bash

2. CREATE DATABASE (often happens when you declare clickhouse_db variable in docker compose or any where else)

3. use DB_NAME

4. clickhouse-client --user=default --password='' --database=tts

5. CREATE TABLE tts_metadata ( text_input String, timestamp String, wav_data String ) ENGINE = MergeTree() ORDER BY timestamp;

6. SELECT * FROM tts_metadata;
```

or you can use web UI (since we prt forwarded port 8123 for http queries):

```shell
curl -X POST 'http://localhost:8123/' --data-binary 'CREATE TABLE tts_metadata (text_input String, timestamp String, wav_data String) ENGINE = MergeTree() ORDER BY timestamp;'
```

- Where to find clickhouse container name?

```shell
docker ps
```

```SQL
CREATE TABLE kafka_input
(
    text_input String,
    cleaned_text String,
    request_id String,
    wav_size_bytes UInt64,
    wav_data String,
    processing_time_seconds Float32,
)
ENGINE = Kafka()
SETTINGS
    kafka_broker_list = 'kafka:9092',
    kafka_topic_list = 'test',
    kafka_group_name = 'test_consumer_group',
    kafka_format = 'JSONEachRow',
    kafka_num_consumers = 1,
    kafka_max_block_size = 1000,
    kafka_skip_broken_messages = 5;

CREATE TABLE clickhouse_table
(
    text_input String,
    cleaned_text String,
    request_id String,
    wav_size_bytes UInt64,
    wav_data String,
    processing_time_seconds Float32,
)
ENGINE = MergeTree()
ORDER BY request_id;

-- insert data from Kafka into ClickHouse
CREATE MATERIALIZED VIEW kafka_to_clickhouse
TO clickhouse_table
AS
SELECT
    text_input,
    cleaned_text,
    request_id,
    wav_size_bytes,
    wav_data,
    processing_time_seconds
FROM kafka_input;


-- delete tables and materialized view if needed
DROP VIEW IF EXISTS kafka_to_clickhouse;
DROP TABLE clickhouse_table;
```
-----------

If you want to rebuild the base image (Dockerfile) of your docker compose file, use:

```shell
docker compose up --build
```

------------

docker compose environment for machine == gitops environment for kubernetes

--------

To automatically run SQL commands when the ClickHouse container starts, you can use an initialization script. ClickHouse supports executing custom SQL scripts placed in the `/docker-entrypoint-initdb.d` directory of the container.

Add a volume to mount the SQL script into the ClickHouse container’s `/docker-entrypoint-initdb.d` directory. ClickHouse will automatically execute this script at startup.

```yaml
clickhouse:
  image: docker.mci.dev/clickhouse/clickhouse-server:22.7.3.5
  environment:
    - CLICKHOUSE_DB=tts
    - CLICKHOUSE_USER=default
    - CLICKHOUSE_PASSWORD=password
  ports:
    - "8123:8123"  # HTTP port for web UI and query execution
    - "9000:9000"  # TCP port for ClickHouse queries
  volumes:
    - ./init.sql:/docker-entrypoint-initdb.d/init.sql  # Mount the initialization script
  networks:
    - tts-network
```

- We can also connect to a remote clickhouse server(or any database or service), for which we don't need any clickhouse app in docker compose.

on cluster

replicated merge tree 
sharding 

-----------

**MergeTree** is the most commonly used table engine in ClickHouse due to its high performance and suitability for large-scale analytical workloads. Here’s an explanation of why ClickHouse uses **MergeTree** and what it is:

### **What is MergeTree?**

**MergeTree** is a ==table engine== in ClickHouse designed for efficient data storage and retrieval, especially in analytical use cases. ==It organizes data into sorted parts on disk and periodically merges these parts to optimize performance. It supports features like primary key indexing, partitioning, and compression.==

### **Key Features of MergeTree**

1. **Primary Key Indexing**:
    
    - MergeTree organizes data based on a primary key, enabling fast range queries and lookups.
    - The data is sorted by this key during insertion, which makes querying large datasets efficient.
2. **Partitions**:
    
    - Data can be divided into logical partitions based on specific columns (e.g., date).
    - Partitioning helps in isolating queries to specific data ranges, improving query speed.
3. **Data Merging**:
    
    - Over time, smaller data parts are merged into larger parts in the background.
    - This reduces the number of files and optimizes disk space and query performance.
4. **Compression**:
    
    - MergeTree supports data compression, reducing storage requirements.
    - ClickHouse applies efficient compression algorithms to the data blocks.
5. **Support for Large Datasets**:
    
    - It is designed to handle petabytes of data while maintaining excellent query performance.
6. **High Write Throughput**:
    
    - MergeTree allows high-frequency inserts into the table while ensuring data remains sorted and queryable.
7. **Efficient Queries**:
    
    - The combination of primary key indexing, partitioning, and compression ensures that queries can scan only the relevant data, reducing the query time.

### **When to Use MergeTree in ClickHouse**

MergeTree is ideal for:

- Analytical workloads with large datasets.
- Time-series data.
- Applications requiring high write throughput and efficient reads.
- Scenarios where you need data compression and partitioning.


### **How MergeTree Works**

1. **Data Ingestion**:
    - Data is written into small parts, sorted by the primary key, and stored on disk.
2. **Query Execution**:
    - Queries use the primary key index and partitioning information to quickly locate relevant data parts.
3. **Background Merging**:
    - A background process periodically merges smaller parts into larger ones to optimize disk usage and query performance.


---------------

**ReplicatedMergeTree** is a variation of the MergeTree engine in ClickHouse that provides replication and high availability across multiple ClickHouse nodes. It ensures data consistency and fault tolerance by replicating data to multiple nodes in a cluster while maintaining the performance benefits of the MergeTree engine.

### **How ReplicatedMergeTree Works**

1. **Data Replication**:
    
    - When a table is created using `ReplicatedMergeTree`, the data written to the table is replicated across multiple nodes in the cluster.
    - Each node maintains a copy of the data parts, ensuring redundancy.
2. **ZooKeeper Coordination**:
    
    - **ZooKeeper** is required for managing the replication process.
    - ZooKeeper tracks metadata like part assignments, merges, and replica states to ensure consistency among replicas.
3. **Partitioning and Sharding**:
    
    - In a distributed cluster, data can be partitioned across shards (horizontal splitting) and replicated within each shard for redundancy.
4. **Consistency**:
    
    - ReplicatedMergeTree uses a quorum mechanism to ensure consistency when reading or writing data.
    - It ensures that replicas stay in sync even during merges or failures.
5. **Background Merges**:
    
    - Similar to MergeTree, data parts are merged in the background to optimize storage and query performance.
    - These merges are coordinated across replicas to avoid duplication of effort.

### ==**ZooKeeper's Role**==

ZooKeeper is essential for managing the replication process in **ReplicatedMergeTree**:

- **Metadata Management**:
    - Tracks information about replicas, data parts, and merges.
- **Replica Coordination**:
    - Ensures that all replicas are consistent and synchronized.
- **Failure Recovery**:
    - Automatically detects and rebalances replicas when nodes fail or are added.


### **Table Syntax Example**

Here’s how to create a **ReplicatedMergeTree** table:

```sql
CREATE TABLE replicated_table
(
    id UInt64,
    value String,
    event_date Date
)
ENGINE = ReplicatedMergeTree(
    '/clickhouse/tables/replicated_table/{shard}', -- ZooKeeper path for the table
    '{replica}'                                   -- Replica name
)
PARTITION BY toYYYYMM(event_date)                 -- Partitioning
ORDER BY id                                       -- Primary key
SETTINGS index_granularity = 8192;                -- Granularity for primary key index (not mendatory)

```

### **Explanation of Parameters**

1. **`/clickhouse/tables/replicated_table/{shard}`**:
    
    - The ZooKeeper path where metadata about the table and replicas is stored.
    - Each shard (logical group of replicas) has its own unique path.
2. **`{replica}`**:
    
    - A placeholder for the replica name.
    - The name is specified when starting a ClickHouse server using the `--replica` parameter.
3. **`PARTITION BY`**:
    
    - Specifies how data is divided into partitions (e.g., by date or another column).
4. **`ORDER BY`**:
    
    - Defines the primary key and sorting order for the data.

---------

A **Distributed table** acts as a virtual layer to distribute queries and inserts across multiple nodes in a ClickHouse cluster.

-  ReplicatedMergeTree engine creates replicated tables in a single shard
- Distributed creates separate shards of data in a cluster

```sql
CREATE TABLE distributed_table ON CLUSTER cluster_name
AS replicated_table  --same shema as the replicated table
ENGINE = Distributed(
    'cluster_name',       -- Cluster name (defined in ClickHouse configuration)
    'default',          -- Database name
    'replicated_table', -- Table name
    rand()              -- Sharding key (used to distribute data across shards)
);
```

-------------

Use this command to connect to a remote clickhouse server and apply sql commands :

```bash 
clickhouse-client --host=172.16.36.74 --user=neurobox --password=ytfrm --database=neurobox --multiquery < /path/to/your/sql_file.sql
```

```python
# remote clickhouse config and user

CLICKHOUSE_HOSTS = [

"172.16.36.74", "172.16.36.75", "172.16.36.76", "172.16.36.77",

"172.16.36.40", "172.16.36.41", "172.16.36.42", "172.16.36.43"

]

CLICKHOUSE_USER = "neurobox"

CLICKHOUSE_PASSWORD = "ytfrm"

CLICKHOUSE_DATABASE = "tts"

CLICKHOUSE_TABLE = "tts_service_data"

# client
client = Client(

host=','.join(CLICKHOUSE_HOSTS),

port=CLICKHOUSE_PORT,

user=CLICKHOUSE_USER,

password=CLICKHOUSE_PASSWORD,

database=CLICKHOUSE_DATABASE,

send_receive_timeout=60,

)

```

--------

Remote clickhouse kafka engine settings for rudabeh:

```sql 
-- clickhouse-client --user=default --password=password --database=tts

use tts;

CREATE TABLE IF NOT EXISTS tts_analytic_data_consumer

(

text_input String,

cleaned_text String,

request_id String,

wav_size_bytes UInt64,

wav_data String,

processing_time_seconds Float32,

date_time String,

time_stamp UInt32,

)

ENGINE = Kafka()

SETTINGS

kafka_broker_list = '172.16.1.15:30615',

kafka_topic_list = 'sre-data-box-tts-voice',

kafka_group_name = 'sre-data-box-tts',

kafka_format = 'JSONEachRow',

kafka_num_consumers = 8,

kafka_max_block_size = 10,

kafka_skip_broken_messages = 2,

kafka_security_protocol = 'SASL_PLAINTEXT',

kafka_sasl_mechanism = 'SCRAM-SHA-512',

kafka_sasl_username = 'sre-data-box',

kafka_sasl_password = 'L4qNHkIsEiqI',  
```

-------

In case you don't have access to kuber (in which exists kafka) from a vm (in which exists clickhouse) then use "vector" instead of kafka engine and materialized view:

![[Pasted image 20250104181446.png]]

-----------

Learn: Vector is a package. you can:

1. Install it locally and run it with "vector --config vector.yaml"
2. Make a costume image for it from its base image, and give the above command to its CMD.
3. Add it to a docker compose file and give the above command to its CMD.
4. Deploy it as a kuernetes pod, and and give the above command to its CMD.