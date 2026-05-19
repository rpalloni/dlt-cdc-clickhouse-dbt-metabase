### architecture
dlt and cdc ingestion and modeling in clickhouse

Ingestion streams in ClickHouse:
```
PostgreSQL  -- WAL logical replication  --> ClickHouse (pg_cdc dataset)
MinIO       -- dlt filesystem source    --> ClickHouse (events dataset)
```

### PostgreSQL -> ClickHouse
Uses ClickHouse's built in **MaterializedPostgreSQL** database engine.
ClickHouse starts after PG is healty, then runs `pg_cdc.sql` initialization to create the `pg_cdc` database with `companies` and `invoices` data.
Once booted, ClickHouse opens a replication slot and continuously syncs changes.

### MinIO -> ClickHouse
A **dlt** pipeline reads JSONL (json lines) files from the `events` MinIO bucket and loads them into the `events` ClickHouse table.
Credentials configuration are resolved from `.dlt/secrets.toml`

### run
```
make up (runs init scripts both in PG and CH)
make events (terminal 1)
make ingest (terminal 2)

make down
make destroy
```
