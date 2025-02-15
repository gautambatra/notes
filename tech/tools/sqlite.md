# sqlite

```bash
# open db
sqlite3 /path/to/db/file

# list all tables
.tables

# see columns in a table
PRAGMA table_info(table_name);

# to run sql queries, enter directly in the sqlite3 prompt, or use cli:
    sqlit3 -line mydb.db sql_statement1; sql_statement2; 
    sqlite3 mydb.db statement

```
