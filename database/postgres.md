# PostgreSQL

Good to Read:

- (Sling Academy: PostgresSQL)[https://www.slingacademy.com/article/postgresql-created-at-and-updated-at-columns/]

### Case-insensetive query

References:

- (AWS: Manage case-insensitive data in PostgreSQL)[https://aws.amazon.com/blogs/database/manage-case-insensitive-data-in-postgresql/]
- (Stack overflow: How to make "case-insensitive" query in Postgresql?)[https://stackoverflow.com/questions/7005302/how-to-make-case-insensitive-query-in-postgresql]

```sql
SELECT id
  FROM groups
 WHERE LOWER(name)=LOWER('Administrator')
```

**Note:** If it is a large or frequently queried table, that could cause trouble. Use Case-insensetive collation, citext, or a function-based index will improve performance.
