# Tool Box Postgresql <!-- omit in toc -->

- [Analyse requests](#analyse-requests)
  - [Enable extension](#enable-extension)
  - [Check the requests](#check-the-requests)
  - [Reset stats](#reset-stats)

## Analyse requests

### Enable extension

Add pg_stat_statements to shared_preload_libraries in the postgresql.conf file.
Restart the database server.

```ssh
vi path\postgresql.conf
shared_preload_libraries = 'pg_stat_statements' 
```

On the database create extension

```sql
CREATE EXTENSION pg_stat_statements;
```

### Check the requests

```sql
SELECT query, calls, total_time, mean_time, rows, 100.0 * shared_blks_hit /
               nullif(shared_blks_hit + shared_blks_read, 0) AS hit_percent
          FROM pg_stat_statements ORDER BY total_time DESC LIMIT 10;
```

### Reset stats

```sql
select pg_stat_statements_reset();
```
