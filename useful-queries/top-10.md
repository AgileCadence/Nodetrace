# Top 10

Who are my top 10 users:

```sql
AppPageViews
| summarize count() by UserId
| top 10 by count_
| render piechart 
```

Who may need to be trained as lots of exceptions thrown:

```sql
AppExceptions
| summarize count() by UserId
| top 10 by count_
| render piechart 
```

What are the top 10 pages viewed:

```sql
AppPageViews
| summarize count() by Name
| top 10 by count_
| render piechart
```

