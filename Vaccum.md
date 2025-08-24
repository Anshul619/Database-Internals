# Vacuum
- [Vacuum](https://www.postgresql.org/docs/current/sql-vacuum.html) regularly in PostgreSQL to prevent bloat and maintain healthy performance in high-churn tables.

# Why Vacuum needed?
- PostgreSQL uses MVCC (Multi-Version Concurrency Control), which means old row versions from UPDATE / DELETE are left behind as dead tuples.
- Over time, this causes table bloat and slows queries. VACUUM is how you clean it up.

# VACUUM Types

| Command          | What It Does                                                                  | Locking                      |
|------------------|-------------------------------------------------------------------------------|------------------------------|
| `VACUUM`         | Removes dead tuples so space can be reused **inside the table**.              | Non-blocking                 |
| `VACUUM ANALYZE` | Cleans dead tuples **and** updates statistics for the query planner.          | Non-blocking                 |
| `VACUUM FULL`    | Rewrites the whole table to reclaim space back to the OS (shrinks file size). | ⚠️ Blocking (exclusive lock) |

# Usage Examples

````
-- Basic cleanup
VACUUM;

-- Cleanup + update planner stats (recommended after big updates/deletes)
VACUUM ANALYZE;

-- Reclaim disk space (use sparingly, locks the table)
VACUUM FULL;
````

# Best Practices
- ✅ Let autovacuum handle routine cleanup (enabled by default).
- ✅ Run manual VACUUM ANALYZE after:
  - Bulk INSERT / UPDATE / DELETE
  - Schema migrations
- ✅ Use VACUUM FULL only when:
  - Disk space is severely bloated
  - You can tolerate downtime (locks table)

# Monitoring Bloat
- Find tables with many dead tuples

````
SELECT relname AS table,
       n_live_tup AS live_rows,
       n_dead_tup AS dead_rows
FROM pg_stat_user_tables
ORDER BY n_dead_tup DESC
LIMIT 10;
```
