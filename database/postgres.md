# PostgreSQL

### Case-insensetive query

References:

- (Stack overflow: How to make "case-insensitive" query in Postgresql?)[https://stackoverflow.com/questions/7005302/how-to-make-case-insensitive-query-in-postgresql]

```sql
SELECT id
  FROM groups
 WHERE LOWER(name)=LOWER('Administrator')
```

**Note:** If it is a large or frequently queried table, that could cause trouble. Use Case-insensetive collation, citext, or a function-based index will improve performance.
