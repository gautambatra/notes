# SQL

```sql
-- Getting fields from 2 tables with a common field

select a.id a.name a.age b.city from (select name,age,id from table1 where male = 1) as a left join (select id,city from table2) as b on a.id = b.id
```
