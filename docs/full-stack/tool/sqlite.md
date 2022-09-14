# sqlite

- show tables
```sql
select name from sqlite_schema where type='table' and name not like 'sqlite_%'
select sqlite_version();
```