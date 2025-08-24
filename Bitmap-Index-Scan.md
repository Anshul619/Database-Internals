# Bitmap Index Scan
- A Bitmap Index Scan is a query execution method used by some relational databases (like PostgreSQL, Oracle, etc.) to efficiently retrieve rows that match a condition using an index — especially when many rows qualify.

## Normal Index Scan
- The database walks through the index and fetches matching rows one by one.
- Good when few rows are returned (high selectivity).

## Bitmap Index Scan
- Instead of immediately fetching each row, the database uses the index to build a bitmap (a compact in-memory array of bits).
- Each bit corresponds to a row (or a page/block) in the table.

If a row matches, the bit is set to 1.
- Multiple bitmaps (from different indexes) can be combined with logical operations (AND, OR).

Example: WHERE city = 'London' AND age > 30
- One bitmap for city = 'London'
- One bitmap for age > 30

Combine with AND to get only rows matching both.
- After the bitmap is constructed, the DB scans the relevant table pages (in bulk, sorted order), which is much faster than random index lookups.

## When is it used?
- When many rows match the condition, but not so many that a full table scan would be cheaper.
- When multiple indexes can be combined.
- Useful for low-to-medium selectivity predicates.